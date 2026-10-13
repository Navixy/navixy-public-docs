---
description: >-
  Connect Power BI to IoT Query Premium and load the tables you need.
tags:
  - guide
  - preview
---

# Connect Power BI

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

Power BI connects to IoT Query Premium through its ClickHouse connector, which uses the ClickHouse ODBC driver. In this guide, you connect Power BI Desktop and load the trips table. Then you build a chart of the distance that your assets drive per day. This guide is for analysts and developers who build Power BI reports. Before you start, test your connection details as described in [Connection setup](../connection-setup.md#test-the-connection).

## Prerequisites

Before you connect, make sure that you have the following:

* Windows with Power BI Desktop `2.137.751.0` or later. Earlier versions don't include the ClickHouse connector.
* The host, port, user name, and password of IoT Query Premium. See [Connection setup](../connection-setup.md#connection-details).
* Permission to install the ODBC driver on the computer.

## Connect Power BI Desktop to IoT Query Premium

To connect Power BI Desktop and load a table, follow these steps:

{% stepper %}
{% step %}
### Install the ClickHouse ODBC driver

The ClickHouse connector of Power BI sends its queries through the ClickHouse ODBC driver.

1. Download the latest `.msi` installer from the [ClickHouse ODBC driver releases](https://github.com/ClickHouse/clickhouse-odbc/releases).
2. Run the installer.
3. If Power BI Desktop is open, restart it.
{% endstep %}

{% step %}
### Open the ClickHouse connector

1. In Power BI Desktop, click **Get Data**.
2. Search for `ClickHouse`.
3. Select the ClickHouse connector and click **Connect**.
{% endstep %}

{% step %}
### Enter the connection details

Fill in the connection dialog:

| Field | Value |
| --- | --- |
| **Host** | `<HOST>`, without `http://` or `https://` |
| **Port** | `<PORT>` |
| **Database** | `report` |
| **Data Connectivity mode** | **DirectQuery** |

**Database** sets which database the **Navigator** opens first. The connection can still read `core_bdr` and `telemetry`.

**DirectQuery** sends a query to IoT Query Premium each time a visual changes, so large tables such as `report.trips_stops` stay in ClickHouse. **Import** copies the table into the report file. Use **Import** only for small tables, such as the `core_bdr` tables of assets and geo objects.

Click **OK**.
{% endstep %}

{% step %}
### Enter your user name and password

Power BI asks for the credentials in a separate dialog. Enter `<USER>` and `<PASSWORD>` from your connection details and click **Connect**.

Power BI stores the credentials for this data source on your computer. They aren't saved in the report file.
{% endstep %}

{% step %}
### Load the trips table

The **Navigator** window lists the databases and tables that your user can read.

1. Expand the `report` database.
2. Select `trips_stops`.
3. Click **Load**.

The table appears in the **Data** pane, with the columns described in [report database](../data-schema/transformation-layer/report.md#columns-of-report.trips_stops).
{% endstep %}
{% endstepper %}

{% hint style="info" %}
A `core_bdr` table loaded from the **Navigator** can show an asset or a device more than once. The **Navigator** reads a table without `FINAL`, and the `core_bdr` tables need `FINAL`. `report.trips_stops` doesn't need `FINAL`. See [Data schema](../data-schema/#read-the-current-version-of-each-row) to learn why.
{% endhint %}

## Chart the distance driven per day

The following chart shows the total distance of all trips for each day. It uses the `kind`, `date`, and `distance_m` columns of `report.trips_stops`.

To build the chart, follow these steps:

1. In the **Visualizations** pane, select **Clustered column chart**.
2. Drag `date` from `trips_stops` to **X-axis**.
3. Drag `distance_m` to **Y-axis**. Power BI sums the values.
4. Drag `kind` to the **Filters** pane, under **Filters on this visual**.
5. In the `kind` filter, select only `trip`.

The chart shows one column per UTC day, with the distance in meters. The same numbers come from the following query, which you can run in `clickhouse-client` to check the chart:

```sql
SELECT
    date,
    sum(distance_m) AS distance_m
FROM report.trips_stops
WHERE kind = 'trip'
GROUP BY date
ORDER BY date
```

The result has one row per day:

| date | distance_m |
| --- | --- |
| 2026-09-25 | 1284310 |
| 2026-09-26 | 1402877 |
| 2026-09-27 | 968245 |

## Refresh the data in Power BI Service

A report that you publish to Power BI Service refreshes through an on-premises data gateway. The gateway runs the ClickHouse ODBC driver, so install the driver on the gateway computer. Then add IoT Query Premium as a data source of the gateway, with the same connection details. Follow the Microsoft documentation for [on-premises data gateways](https://learn.microsoft.com/power-bi/connect-data/service-gateway-onprem).

## Fix common connection errors

The following table lists the most common errors when Power BI connects to IoT Query Premium:

| Error | Cause | What to do |
| --- | --- | --- |
| Power BI doesn't list the ClickHouse connector | Power BI Desktop is older than `2.137.751.0` | Update Power BI Desktop |
| The connector reports that the ODBC driver is missing | The ClickHouse ODBC driver isn't installed | Install the driver, then restart Power BI Desktop |
| `Authentication failed` | The user name or password is wrong | Open **File**, then **Options and settings**, then **Data source settings**. Edit the credentials of the ClickHouse source and enter them again |
| Connection timed out or refused | The host or port is wrong, or a firewall blocks the port | Check that **Host** has no `https://` and that **Port** is `<PORT>`. Ask your network administrator to allow outgoing connections to that port |
| Connection reset, or an unexpected packet | The driver connects without TLS | Enable TLS in the ODBC options of the connector, in the **Options** field |
| Certificate error | Windows doesn't trust the certificate of the server | Update the trusted root certificates of Windows. Don't turn off certificate checks |
| A column fails to load, or has the wrong type | Power BI doesn't support the ClickHouse type `UInt64` | Click **Transform Data** and change the type of the column before you load it. `count()` in ClickHouse returns `UInt64` |
| Assets or devices appear twice | A `core_bdr` table was loaded without `FINAL`, so it has older versions of some rows | In **Transform Data**, keep the row with the highest `version` for each `id`, and remove the rows whose `deleted_at` has a value. Or use a `report` table that has the values that you need |
| Refresh fails in Power BI Service | No gateway, or the gateway has no ClickHouse ODBC driver | Set up the gateway as described in [Refresh the data in Power BI Service](#refresh-the-data-in-power-bi-service) |

If the error continues, run the test query in [Connection setup](../connection-setup.md#test-the-connection) from the same computer. When the test query works and Power BI doesn't, the problem is in the Power BI settings.

## See also

* [report database](../data-schema/transformation-layer/report.md) and [agg database](../data-schema/transformation-layer/agg.md): Find the columns of trips and stops, sensor values, and positions in geo objects.
* [core_bdr database](../data-schema/raw-data-layer/core-bdr.md): Find the tables with the names of your assets, devices, and geo objects.
* [Use third-party BI tools](./): Compare Power BI with the other BI tools that connect to IoT Query Premium.
