---
description: >-
  Add IoT Query Premium to Apache Superset as a database and build charts on
  its tables.
tags:
  - guide
  - preview
---

# Connect Apache Superset

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

Apache Superset connects to IoT Query Premium through the `clickhouse-connect` Python driver. In this guide, you add IoT Query Premium as a database in Superset and run a query in SQL Lab. Then you turn the result into a chart of the distance that each asset drove in the last 7 days. This guide is for developers and data engineers who maintain a Superset server. Before you start, test your connection details as described in [Connection setup](../connection-setup.md#test-the-connection).

## Prerequisites

Before you connect, make sure that you have the following:

* A running Superset server. To install one, see the [Superset documentation](https://superset.apache.org/docs/intro).
* A Superset user with permission to add databases, such as an admin user.
* Access to the Python environment or the Docker setup of Superset, to install the driver.
* The host, port, user name, and password of IoT Query Premium. See [Connection setup](../connection-setup.md#connection-details).

## Add IoT Query Premium to Superset

To add IoT Query Premium as a database, follow these steps:

{% stepper %}
{% step %}
### Install the clickhouse-connect driver

Superset reads ClickHouse through `clickhouse-connect` version `0.13.0` or later. Install it in the Python environment where Superset runs, then restart Superset:

```bash
pip install "clickhouse-connect>=0.13.0"
```

If you run Superset with Docker Compose, add the driver to `docker/requirements-local.txt` instead, then restart the containers:

```bash
echo "clickhouse-connect>=0.13.0" >> ./docker/requirements-local.txt
docker compose down
docker compose up -d
```

After the restart, **ClickHouse Connect** is in the list of database types.
{% endstep %}

{% step %}
### Open the database connection form

1. In Superset, click **Settings**, then **Database Connections**.
2. Click **+ Database**.
3. In **Connect a database**, select **ClickHouse Connect**.
{% endstep %}

{% step %}
### Enter the SQLAlchemy URI

Superset accepts the connection details as one SQLAlchemy URI. Choose the option to connect with a SQLAlchemy URI and enter the URI in this form:

```
clickhousedb://<USER>:<PASSWORD>@<HOST>:<PORT>/report?secure=true
```

The parts of the URI are:

* `<USER>`, `<PASSWORD>`, `<HOST>`, and `<PORT>`: The values from your connection details. If the password has characters such as `@`, `:`, or `/`, replace each character with its URL encoding, for example `%40` for `@`.
* `report`: The default database of the connection. Queries can still read `core_bdr` and `telemetry` when each table name includes its database.
* `secure=true`: Connects with TLS. Connections to IoT Query Premium are encrypted, so the driver must use TLS.

Enter a display name, such as `IoT Query Premium`.
{% endstep %}

{% step %}
### Test and save the connection

1. Click **Test Connection**. Superset confirms that it can connect.
2. Click **Connect**.

IoT Query Premium appears in the list of database connections.
{% endstep %}
{% endstepper %}

## Chart the distance of each asset

A chart in Superset starts from a query or a dataset. The following query sums the trip distance of the last 7 days for each asset. It joins `core_bdr.asset` to get the asset names:

```sql
SELECT
    a.title AS asset,
    round(sum(t.distance_m) / 1000, 1) AS distance_km
FROM report.trips_stops AS t
INNER JOIN
(
    SELECT id, title
    FROM core_bdr.asset FINAL
) AS a ON a.id = toUUID(t.asset_id)
WHERE t.kind = 'trip'
  AND t.start_time >= now() - INTERVAL 7 DAY
GROUP BY asset
ORDER BY distance_km DESC
```

The result has one row per asset:

| asset | distance_km |
| --- | --- |
| Truck 12 | 1842.6 |
| Van 3 | 1215.9 |
| Truck 7 | 987.4 |

With `FINAL`, the query reads the current version of each asset. The query doesn't filter out deleted assets, so the trips of an asset that was deleted since still count. `toUUID` converts `t.asset_id`, which is text in `report.trips_stops`, to the `UUID` type of `core_bdr.asset`. See [Data schema](../data-schema/#rules-for-every-query) to learn why both are needed.

To run the query and chart the result, follow these steps:

1. Click **SQL**, then **SQL Lab**.
2. Select the IoT Query Premium database.
3. Paste the query and click **Run**.
4. Click the arrow next to **Save**, and click **Save dataset**. Enter a name, such as `distance_per_asset_7_days`.
5. Click **Save & explore** to open the dataset in the chart editor.
6. Select the **Bar Chart** type.
7. Set the **X-axis** to `asset` and the metric to `SUM(distance_km)`.
8. Click **Update chart**, then **Save**, and add the chart to a dashboard.

The chart shows one bar per asset, with the distance in kilometers. Because the dataset is a query, Superset runs the query again each time that it refreshes the chart.

## Fix common connection errors

The following table lists the most common errors when Superset connects to IoT Query Premium:

| Error | Cause | What to do |
| --- | --- | --- |
| **ClickHouse Connect** isn't in the list of database types | `clickhouse-connect` isn't installed in the environment where Superset runs | Install the driver as described in [Add IoT Query Premium to Superset](#add-iot-query-premium-to-superset), then restart Superset |
| `Authentication failed` | The user name or password in the URI is wrong | Copy both again from your connection details. URL-encode special characters in the password |
| Connection timed out or refused | The host or port is wrong, or a firewall blocks the port from the Superset server | Check the host and port. Run the test query in [Connection setup](../connection-setup.md#test-the-connection) from the Superset server |
| SSL error, or the connection closes at once | The URI has no `?secure=true`, so the driver connects without TLS | Add `?secure=true` to the end of the URI |
| `UNKNOWN_TABLE` | The table name has no database | Write the table with its database, for example `core_bdr.asset` |
| Assets appear twice in a chart | A `core_bdr` table was read without `FINAL` | Add `FINAL` after the table name, as in the example query |

## See also

* [report database](../data-schema/transformation-layer/report.md): Find the columns of trips, geo object visits, sensor readings, and device status.
* [core_bdr database](../data-schema/raw-data-layer/core-bdr.md): Find the tables with the names of your assets, devices, and geo objects.
* [Use third-party BI tools](./): Compare Superset with the other BI tools that connect to IoT Query Premium.
