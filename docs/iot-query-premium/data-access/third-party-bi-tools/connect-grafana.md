---
description: >-
  Add IoT Query Premium to Grafana as a data source and build dashboards on
  its tables.
tags:
  - guide
  - preview
---

# Connect Grafana

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

Grafana connects to IoT Query Premium through the ClickHouse data source plugin for Grafana. In this guide, you install the plugin and add IoT Query Premium as a data source. Then you build a panel that shows the hourly fuel level of one asset. This guide is for developers and operations teams who monitor GPS device data in Grafana. Before you start, test your connection details as described in [Connection setup](../connection-setup.md#test-the-connection).

## Prerequisites

Before you connect, make sure that you have the following:

* Grafana Cloud, or a Grafana server that you run.
* A Grafana user with the Admin role, to install plugins and add data sources.
* The host, port, user name, and password of IoT Query Premium. See [Connection setup](../connection-setup.md#connection-details).

## Add IoT Query Premium to Grafana

To install the plugin and add the data source, follow these steps:

{% stepper %}
{% step %}
### Install the ClickHouse plugin

1. In Grafana, go to **Connections**, then **Add new connection**.
2. Search for `ClickHouse`.
3. Select the ClickHouse data source and click **Install**.

On a Grafana server without access to the plugin catalog, install the plugin from the command line and restart Grafana:

```bash
grafana cli plugins install grafana-clickhouse-datasource
```
{% endstep %}

{% step %}
### Add the data source

On the page of the ClickHouse plugin, click **Add new data source**. Enter a name, such as `IoT Query Premium`.
{% endstep %}

{% step %}
### Enter the connection details

Fill in the settings of the data source:

| Setting | Value |
| --- | --- |
| **Server address** | `<HOST>` |
| **Server port** | `<PORT>` |
| **Protocol** | **HTTP** |
| **Secure connection** | On |
| **Username** | `<USER>` |
| **Password** | `<PASSWORD>` |
| **Default database** | `report` |

`<PORT>` is the port of the ClickHouse HTTP interface, so **Protocol** must be **HTTP**. **Secure connection** turns on TLS, which IoT Query Premium connections use. **Default database** sets which database the query builder opens first. Queries can still read `core_bdr` and `telemetry`.

Leave the other settings at their default values.
{% endstep %}

{% step %}
### Test and save the data source

Click **Save & test**. Grafana confirms that the data source works.
{% endstep %}
{% endstepper %}

## Show a sensor value over time

A Grafana panel that shows a time series needs a query that returns a time column named `time` and one or more value columns. The following query returns the average fuel level per hour of the asset `Truck 12`, for the time range that the dashboard shows:

```sql
SELECT
    toStartOfHour(bucket_time) AS time,
    round(sum(val_sum) / sum(sample_count), 1) AS fuel_level
FROM agg.sensor_readings_5m FINAL
WHERE raw_key = 'lls_level_0'
  AND asset_id IN
  (
      SELECT toString(id)
      FROM core_bdr.asset FINAL
      WHERE title = 'Truck 12' AND deleted_at IS NULL
  )
  AND $__timeFilter(bucket_time)
GROUP BY time
ORDER BY time
```

The parts of the query that are specific to Grafana and IoT Query Premium are:

* `$__timeFilter(bucket_time)` is a macro of the plugin. Grafana replaces it with a filter for the time range of the dashboard.
* `agg.sensor_readings_5m` has the values of each signal per minute. `toStartOfHour` and `GROUP BY` add the minutes up to hours. For a range of several weeks, use `toStartOfDay` for one point per day.
* `toString(id)` converts the `UUID` of `core_bdr` to text, because the `agg` tables store `asset_id` as text.

For the last 3 hours, the result is:

| time | fuel_level |
| --- | --- |
| 2026-09-27 13:00:00.000 | 184.2 |
| 2026-09-27 14:00:00.000 | 181.6 |
| 2026-09-27 15:00:00.000 | 176.2 |

Replace `lls_level_0` with the name of a signal that your GPS devices send. See [telemetry database](../data-schema/raw-data-layer/telemetry.md#how-sensor-values-are-stored) to find a query that lists the signal names.

To build the panel, follow these steps:

1. Go to **Dashboards**, then create a new dashboard.
2. Click **Add visualization** and select the `IoT Query Premium` data source.
3. Switch the query editor to **SQL Editor**.
4. Set **Query type** to **Time series**.
5. Paste the query and click **Run query**.
6. Select the **Time series** visualization.
7. Click **Save dashboard** and enter a name for the dashboard.

The panel shows one line with the fuel level of `Truck 12` per hour. When you change the time range of the dashboard, Grafana runs the query again for the new range.

## Fix common connection errors

The following table lists the most common errors when Grafana connects to IoT Query Premium:

| Error | Cause | What to do |
| --- | --- | --- |
| ClickHouse isn't in the list of data sources | The plugin isn't installed | Install the plugin as described in [Add IoT Query Premium to Grafana](#add-iot-query-premium-to-grafana) |
| `Authentication failed` | The user name or password is wrong | Enter both again from your connection details. Check that no space was added before or after the password |
| Connection timed out or refused | The host or port is wrong, or a firewall blocks the port from the Grafana server | Check the host and port. Run the test query in [Connection setup](../connection-setup.md#test-the-connection) from the Grafana server |
| SSL error, or the connection closes at once | **Secure connection** is off, or **Protocol** is **Native** | Turn on **Secure connection** and set **Protocol** to **HTTP** |
| The panel shows no data | The time range has no data, or your devices don't send the signal name in the query | Widen the time range. List the signal names of your devices, and check the asset name |
| The panel shows a table, not a line | The query has no column named `time`, or **Query type** is **Table** | Name the time column `time` and set **Query type** to **Time series** |
| `UNKNOWN_TABLE` | The table name has no database | Write the table with its database, for example `agg.sensor_readings_5m` |

## See also

* [report database](../data-schema/transformation-layer/report.md) and [agg database](../data-schema/transformation-layer/agg.md): Find the columns of trips and stops, sensor values, and positions in geo objects.
* [telemetry database](../data-schema/raw-data-layer/telemetry.md): Find the signal names that your GPS devices send.
* [Use third-party BI tools](./): Compare Grafana with the other BI tools that connect to IoT Query Premium.
