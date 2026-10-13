---
description: >-
  Connect a Streamlit app to IoT Query Premium and show your data in it.
tags:
  - guide
  - preview
---

# Connect Streamlit

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

Streamlit turns a Python script into a web app, and the `clickhouse-connect` Python driver connects the script to IoT Query Premium. In this guide, you write a small app that shows the distance that each asset drove per day. A slider in the app sets the number of days. This guide is for Python developers who build their own apps on IoT Query Premium data. Before you start, test your connection details as described in [Connection setup](../connection-setup.md#test-the-connection).

## Prerequisites

Before you start, make sure that you have the following:

* Python 3.10 or later, and `pip`.
* The host, port, user name, and password of IoT Query Premium. See [Connection setup](../connection-setup.md#connection-details).

## Build a Streamlit app on IoT Query Premium

To create and run the app, follow these steps:

{% stepper %}
{% step %}
### Install Streamlit and the driver

Create a folder for the app and a virtual environment in it, then install the packages:

```bash
mkdir fleet-distance
cd fleet-distance
python3 -m venv .venv
source .venv/bin/activate
pip install streamlit clickhouse-connect pandas
```

On Windows, activate the environment with `.venv\Scripts\activate` instead of `source .venv/bin/activate`. `clickhouse-connect` returns query results as pandas DataFrames, so the app needs `pandas` too.
{% endstep %}

{% step %}
### Store the connection details

Streamlit reads secrets from `.streamlit/secrets.toml` in the app folder. Create the file with your connection details:

{% code title=".streamlit/secrets.toml" %}
```toml
[clickhouse]
host = "<HOST>"
port = "<PORT>"
username = "<USER>"
password = "<PASSWORD>"
```
{% endcode %}

The app reads these values through `st.secrets`, so they never appear in the code. Add `.streamlit/secrets.toml` to `.gitignore`, so that the password isn't committed to your repository:

```bash
echo ".streamlit/secrets.toml" >> .gitignore
```

When you deploy the app, enter the same values in the secrets settings of your hosting service instead of copying the file.
{% endstep %}

{% step %}
### Write the app

Create `app.py` in the app folder:

{% code title="app.py" %}
```python
import clickhouse_connect
import streamlit as st

QUERY = """
SELECT
    t.date AS day,
    a.title AS asset,
    round(sum(t.distance_m) / 1000, 1) AS distance_km
FROM report.trips_stops AS t
INNER JOIN
(
    SELECT id, title
    FROM core_bdr.asset FINAL
) AS a ON a.id = toUUID(t.asset_id)
WHERE t.kind = 'trip'
  AND t.date > today() - {days:UInt32}
GROUP BY day, asset
ORDER BY day, asset
"""


@st.cache_data(ttl=600)
def load_distance(days):
    settings = st.secrets["clickhouse"]
    client = clickhouse_connect.get_client(
        host=settings["host"],
        port=int(settings["port"]),
        username=settings["username"],
        password=settings["password"],
        secure=True,
    )
    try:
        return client.query_df(QUERY, parameters={"days": days})
    finally:
        client.close()


st.title("Distance per asset")
days = st.slider("Days", min_value=1, max_value=30, value=7)

df = load_distance(days)

if df.empty:
    st.info("No trips in the selected period.")
else:
    st.bar_chart(df, x="day", y="distance_km", color="asset")
    st.dataframe(df, hide_index=True)
```
{% endcode %}

The parts of the app that matter for IoT Query Premium are:

* `secure=True` connects with TLS. Connections to IoT Query Premium are encrypted, so the driver must use TLS.
* `{days:UInt32}` is a query parameter. ClickHouse inserts the value of `days` from `parameters` as a number, so the value can't change the SQL.
* With `FINAL`, the query reads the current version of each asset. The query doesn't filter out deleted assets, so the trips of an asset that was deleted since still count. `toUUID` converts `t.asset_id`, which is text in `report.trips_stops`, to the `UUID` type of `core_bdr.asset`. See [Data schema](../data-schema/#rules-for-every-query) to learn why both are needed.
* `@st.cache_data(ttl=600)` keeps each result for 10 minutes. Streamlit runs the script again each time that you move the slider. The cache stops each run from sending the same query again.

For the last 2 days, `load_distance` returns a DataFrame with one row per day and asset:

| day | asset | distance_km |
| --- | --- | --- |
| 2026-09-26 | Truck 12 | 212.4 |
| 2026-09-26 | Van 3 | 148.0 |
| 2026-09-27 | Truck 12 | 158.9 |
| 2026-09-27 | Van 3 | 173.5 |
{% endstep %}

{% step %}
### Run the app

Start the app from the app folder:

```bash
streamlit run app.py
```

Streamlit prints the address of the app:

```
  You can now view your Streamlit app in your browser.

  Local URL: http://localhost:8501
```

Open the address in a browser. The app shows a bar chart with one bar per day, split by asset, and a table with the same rows. Move the slider to change the number of days.
{% endstep %}
{% endstepper %}

## Fix common connection errors

The following table lists the most common errors when a Streamlit app connects to IoT Query Premium:

| Error | Cause | What to do |
| --- | --- | --- |
| Streamlit reports that no secrets are found, or `KeyError: 'clickhouse'` | `.streamlit/secrets.toml` is missing, isn't in the app folder, or has no `[clickhouse]` section | Create the file as described in [Build a Streamlit app on IoT Query Premium](#build-a-streamlit-app-on-iot-query-premium), and run `streamlit run` from the app folder |
| `ValueError: invalid literal for int()` | `port` in `secrets.toml` still has the placeholder | Replace `<PORT>` with the port from your connection details |
| `DatabaseError` with `Authentication failed` | The user name or password is wrong | Copy both again from your connection details into `secrets.toml` |
| `OperationalError` with a timeout, or a refused connection | The host or port is wrong, or a firewall blocks the port | Check the host and port. Run the test query in [Connection setup](../connection-setup.md#test-the-connection) from the same computer |
| Connection reset, or an unexpected packet | The client connects without TLS | Keep `secure=True` in `get_client` |
| Certificate error | Python doesn't trust the certificate of the server | Update the `certifi` package, or the trusted certificates of your operating system. Don't turn off certificate checks |
| `DatabaseError` with `UNKNOWN_TABLE` | The table name has no database | Write the table with its database, for example `core_bdr.asset` |
| The app shows old values | The cache keeps results for 10 minutes | Wait 10 minutes, or stop the app and run `streamlit run app.py` again |

## See also

* [report database](../data-schema/transformation-layer/report.md) and [agg database](../data-schema/transformation-layer/agg.md): Find the columns of trips and stops, sensor values, and positions in geo objects.
* [core_bdr database](../data-schema/raw-data-layer/core-bdr.md): Find the tables with the names of your assets, devices, and geo objects.
* [Use third-party BI tools](./): Compare Streamlit with the other BI tools that connect to IoT Query Premium.
