---
description: >-
  Write SQL queries for Dashboard Studio panels: parameters, periods of time,
  and the result that each panel type needs.
tags:
  - guide
  - preview
---

# Write SQL queries

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

Every panel of a Dashboard Studio dashboard runs one SQL query. The result of that query must have the shape that the panel type reads. This guide shows how to write such queries:

* Which result each panel type needs
* How to add parameters that readers choose
* How to group values by time
* How to fix a query that fails

This guide is for analysts who write panel and report queries in Dashboard Studio.


This page covers what is particular to Dashboard Studio. The [Data schema](../data-access/data-schema/) page describes the tables and the joins between them. It also describes the rules that every ClickHouse query needs, such as `FINAL` and table names with their database. Read it first.

Before you start, you need the Admin or Editor role in Dashboard Studio and basic knowledge of SQL.

## 1. Explore the data in the SQL editor


Dashboard Studio runs SQL in three places:

* **Panel queries** are written in the **SQL query** tab of the panel editor. Each panel runs one query, and its result must have the shape that the panel type needs.
* **Report queries** are written in the report editor. One query provides the data for the data table, the chart, and the location map of the report.
* **The SQL editor** runs any `SELECT` query and shows the full result. Use it to explore a table and to test a query before you put it in a panel.

Every query is read-only: Dashboard Studio runs `SELECT` statements only.

To open the SQL editor, click **Tools** on the sidebar, then select **SQL editor**. Enter a query, click **Execute query**, and read the result under the query. **Export CSV** downloads the result. Use **Add new tab** to keep several queries open.


Run a few short queries to see what a table has before you write the query of a panel. The following query returns the first rows of the trips table:

```sql
SELECT *
FROM report.trips_stops
WHERE kind = 'trip'
LIMIT 10
```

The following query shows the period that the table covers and how many trips it has:

```sql
SELECT
    min(start_time) AS earliest,
    max(start_time) AS latest,
    count() AS trips
FROM report.trips_stops
WHERE kind = 'trip'
```

The result has one row:

| earliest | latest | trips |
| --- | --- | --- |
| 2026-03-28 04:12:09.000 | 2026-09-28 13:55:41.000 | 18342 |

When the query returns what you expect, change its `SELECT` list to the shape of the panel, as described in [Match the result to the panel type](#id-2.-match-the-result-to-the-panel-type).

## 2. Match the result to the panel type

Each panel type reads the result in its own way. The **SQL query** tab of the panel editor shows the shape under **Dataset requirements** for the type that you selected.

| Panel type | What the result needs | Example columns |
| --- | --- | --- |
| Stat | One row with one number | `value` |
| Bar chart | A category column, then a value column. A third column splits the bars into series | `category`, `value`, `series` |
| Pie chart | A label column, then a value column | `label`, `value` |
| Line chart, Time series | A time column, then a value column. A third column splits the lines into series | `bucket_start`, `value`, `series` |
| Table | Any columns | Every column becomes a table column |
| Map | A latitude column and a longitude column in decimal degrees. Other columns appear in the popup of each point | `latitude`, `longitude`, `asset` |
| Text | No query | |

Column names become the labels that readers see. Name each column by what it contains, and add the unit when a value has one.

A result that has the name of an asset needs a join to `core_bdr.asset`. The `report` tables store IDs as text, and `core_bdr` stores them as `UUID`, so convert the ID with `toUUID`. [Data schema](../data-access/data-schema/#how-tables-join) explains the join.

<details>

<summary>Stat</summary>

A stat panel shows one number. The query returns one row with one numeric column.

The following query counts the trips of the current month, by UTC date:

```sql
SELECT count() AS value
FROM report.trips_stops
WHERE kind = 'trip'
  AND start_time >= toStartOfMonth(now())
```

The following query sums the distance of the last 7 days, in kilometers:

```sql
SELECT round(sum(distance_m) / 1000, 1) AS value
FROM report.trips_stops
WHERE kind = 'trip'
  AND start_time >= now() - INTERVAL 7 DAY
```


The result of the distance query has one row:

| value |
| --- |
| 12846.3 |

</details>

<details>

<summary>Bar chart</summary>

A bar chart reads the category from the first column and the height of the bar from the second. The following query counts the trips of each asset in the current month, with the name of the asset. Like every query of past data on this page, it doesn't filter out deleted assets, so the trips of an asset that was deleted since still count:

```sql
SELECT
    a.title AS category,
    count() AS value
FROM report.trips_stops AS t
INNER JOIN
(
    SELECT id, title
    FROM core_bdr.asset FINAL
) AS a ON a.id = toUUID(t.asset_id)
WHERE t.kind = 'trip'
  AND t.start_time >= toStartOfMonth(now())
GROUP BY category
ORDER BY value DESC
```

The result has one row per asset:

| category | value |
| --- | --- |
| Truck 12 | 64 |
| Van 3 | 51 |
| Truck 7 | 38 |

`ORDER BY` sets the order of the bars. Sort by `value` to rank the categories, or by `category` to show dates in time order.

To show one bar for each value of a third column, add the column to the query. The following query returns the distance of each asset on each of the last 7 days. In the chart, each day has one bar for each asset:

```sql
SELECT
    t.date AS category,
    round(sum(t.distance_m) / 1000, 1) AS value,
    a.title AS series
FROM report.trips_stops AS t
INNER JOIN
(
    SELECT id, title
    FROM core_bdr.asset FINAL
) AS a ON a.id = toUUID(t.asset_id)
WHERE t.kind = 'trip'
  AND t.date > today() - 7
GROUP BY category, series
ORDER BY category, series
```

The result has one row per day and asset:

| category | value | series |
| --- | --- | --- |
| 2026-09-27 | 212.4 | Truck 12 |
| 2026-09-27 | 158.9 | Van 3 |
| 2026-09-28 | 187.0 | Truck 12 |

The bars of one day stand side by side. To stack them into one bar per day, set **Stacking mode** to **Stacked** on the **Visualization settings** tab. **Percent** stacks them and shows each asset as a share of the day total.

If the chart reads the wrong column as the series, select the column in **Series column** on the **Visualization settings** tab.

</details>

<details>

<summary>Pie chart</summary>

A pie chart reads the label of each slice from the first column and its size from the second. The following query counts the devices in each motion state:

```sql
SELECT
    motion AS label,
    count() AS value
FROM report.device_status FINAL
GROUP BY label
ORDER BY value DESC
```

The result has one row per state:

| label | value |
| --- | --- |
| parked | 58 |
| moving | 27 |
| stopped | 9 |
| unknown | 2 |

A pie chart shows the 10 largest slices. Dashboard Studio adds the rest together into one slice named **Other**, so the chart still shows the whole total. Don't add `LIMIT` to a pie chart query: a limit leaves the other categories out of the total.

</details>

<details>

<summary>Line chart and time series</summary>

A line chart reads the time from the first column and the value from the second. A third column draws one line for each of its values. The following query returns the average fuel level of each asset for each hour of the last day. It reads sensor values that the `report` database already groups by hour:

```sql
SELECT
    r.bucket_time AS bucket_start,
    round(sum(r.val_sum) / sum(r.sample_count), 1) AS value,
    a.title AS series
FROM
(
    SELECT asset_id, bucket_time, val_sum, sample_count
    FROM report.sensor_readings_rollup FINAL
    WHERE interval_sec = 3600
      AND raw_key = 'lls_level_0'
      AND bucket_time >= now() - INTERVAL 1 DAY
) AS r
INNER JOIN
(
    SELECT id, title
    FROM core_bdr.asset FINAL
) AS a ON a.id = toUUID(r.asset_id)
GROUP BY bucket_start, series
ORDER BY bucket_start, series
```

The result has one row per hour and asset:

| bucket_start | value | series |
| --- | --- | --- |
| 2026-09-27 14:00:00.000 | 181.6 | Truck 12 |
| 2026-09-27 14:00:00.000 | 96.2 | Van 3 |
| 2026-09-27 15:00:00.000 | 176.2 | Truck 12 |

To add a line chart, add a **Bar chart** panel, then select **Line chart** or **Time series** in **Panel type** on the **Panel properties** tab.

</details>

<details>

<summary>Table</summary>

A table shows every column of the result. The following query lists the trips of the last 7 days, newest first. It converts the start time to the time zone `Europe/Madrid`: replace it with your own time zone, an IANA time zone name such as `America/New_York`. See [Group values by time](#id-5.-group-values-by-time) to learn why the time zone matters:

```sql
SELECT
    a.title AS "Asset",
    toTimeZone(t.start_time, 'Europe/Madrid') AS "Start",
    round(t.distance_m / 1000, 1) AS "Distance (km)",
    round(t.duration_s / 60) AS "Duration (min)",
    t.max_speed_kmh AS "Max speed (km/h)"
FROM report.trips_stops AS t
INNER JOIN
(
    SELECT id, title
    FROM core_bdr.asset FINAL
) AS a ON a.id = toUUID(t.asset_id)
WHERE t.kind = 'trip'
  AND t.start_time >= now() - INTERVAL 7 DAY
ORDER BY t.start_time DESC
LIMIT 100
```

The result has one row per trip:

| Asset | Start | Distance (km) | Duration (min) | Max speed (km/h) |
| --- | --- | --- | --- | --- |
| Truck 12 | 2026-09-28 13:05:12.000 | 96.3 | 97 | 88 |
| Van 3 | 2026-09-28 12:48:03.000 | 11.8 | 22 | 54 |

An alias in double quotes can have spaces and capital letters, so the table shows readable headings.

</details>

<details>

<summary>Map</summary>

A map shows one point for each row. The following query returns the current position of each asset:

```sql
SELECT
    s.latitude AS latitude,
    s.longitude AS longitude,
    a.title AS asset,
    s.motion AS motion
FROM
(
    SELECT asset_id, latitude, longitude, motion
    FROM report.device_status FINAL
) AS s
INNER JOIN
(
    SELECT id, title
    FROM core_bdr.asset FINAL
    WHERE deleted_at IS NULL
) AS a ON a.id = toUUID(s.asset_id)
```

The result has one row per asset:

| latitude | longitude | asset | motion |
| --- | --- | --- | --- |
| 52.370216 | 4.895168 | Truck 12 | moving |
| 52.090737 | 5.12142 | Van 3 | parked |

Dashboard Studio finds the coordinates by the column names. It takes the first column whose name contains `lat` as the latitude and the first column whose name contains `lon` as the longitude. It also accepts a name that contains `y` for latitude and `x` for longitude. As a result, Dashboard Studio can take a column such as `type` as the latitude if that column comes first. Name the columns `latitude` and `longitude`, and put them first in the `SELECT` list. The other columns appear in the popup of each point. A row at latitude 0 and longitude 0 isn't shown.

To show the path of one asset instead, read `telemetry.records` for a short period. A query over `telemetry` returns every message, so limit it to one device and a few hours.

</details>

## 3. Write one query for a report


A report runs one query and shows its result in three ways: as a data table, a chart, and a location map. The query must return the columns of all three views together:

| View | Columns it needs |
| --- | --- |
| Data table | Any columns. Every column of the result appears |
| Chart | A time or category column for the X-axis, and one or more numeric columns for the Y-axis |
| Location map | A latitude column and a longitude column in decimal degrees |


A panel query that returns only a category and a value has no data for the location map. For a report, return one row for each event, such as each stop, and add its coordinates. The following query returns the stops of the last day, with the position and the length of each stop:

```sql
SELECT
    a.title AS asset,
    toTimeZone(t.start_time, 'Europe/Madrid') AS stop_start,
    round(t.duration_s / 60) AS stop_minutes,
    t.latitude AS latitude,
    t.longitude AS longitude
FROM report.trips_stops AS t
INNER JOIN
(
    SELECT id, title
    FROM core_bdr.asset FINAL
) AS a ON a.id = toUUID(t.asset_id)
WHERE t.kind = 'stop'
  AND t.start_time >= now() - INTERVAL 1 DAY
ORDER BY t.start_time
LIMIT 1000
```

The result has one row per stop:

| asset | stop_start | stop_minutes | latitude | longitude |
| --- | --- | --- | --- | --- |
| Truck 12 | 2026-09-28 08:14:02.000 | 42 | 52.370216 | 4.895168 |
| Van 3 | 2026-09-28 09:31:47.000 | 17 | 52.090737 | 5.12142 |


In this report, the chart uses `stop_start` and `stop_minutes`, the location map uses `latitude` and `longitude`, and the data table shows every column. See [Create report](create-report.md) to build the report around this query.

## 4. Add parameters

A parameter is a value that the query receives when it runs, such as the start of the period that a reader chose. Write it in the query as `${name}`. Dashboard Studio sends the value separately from the query text, so a value can't change the query itself.

Three rules apply to every parameter:

* Write `${name}` outside quotes: `start_time >= ${date_from}`, not `start_time >= '${date_from}'`. A parameter inside quotes returns an error.
* A parameter in a comment is ignored.
* The name has only letters, digits, and underscores, and doesn't start with a digit.

Dashboard Studio fills parameters from four sources:

| Parameter | Where the value comes from |
| --- | --- |
| `${__from}`, `${__to}` | The **Time range** that the reader chooses under **Parameters** on the dashboard. Without a choice, the last 24 hours |
| `${<name>_from}`, `${<name>_to}` | A date filter of the dashboard, named `<name>`. You add it with **Dashboard filters** in the editing toolbar |
| `${<label>}` | A global variable with that label. An administrator defines global variables in **Settings**, on the **Configuration** tab |
| `${<name>}` in a report | The **Parameters** form of the report, where the reader enters the value. See [Create report](create-report.md) |

Date and time values arrive as UTC text in ISO 8601, for example `2026-09-01T00:00:00.000Z`. Convert them to a time with `parseDateTime64BestEffort`. The following query counts the trips of each local day in the time range that the reader chose:

```sql
SELECT
    toDate(toTimeZone(start_time, 'Europe/Madrid')) AS category,
    count() AS value
FROM report.trips_stops
WHERE kind = 'trip'
  AND start_time >= parseDateTime64BestEffort(${__from}, 3, 'UTC')
  AND start_time < parseDateTime64BestEffort(${__to}, 3, 'UTC')
GROUP BY category
ORDER BY category
```

For the last 7 days, the result has one row per day:

| category | value |
| --- | --- |
| 2026-09-22 | 39 |
| 2026-09-23 | 44 |
| 2026-09-24 | 41 |


A panel that uses a dashboard date filter references the two filter parameters in the same way as `${__from}` and `${__to}`. For a filter named `period`, write `${period_from}` and `${period_to}` in the query. Instead of writing the parameters, you can turn on the filter on the panel's **Filters** tab and select the result column that it filters.

A dashboard value filter needs no parameter in the query. Dashboard Studio applies it to the panels whose result has the filter's column when you add the filter. See [Create dashboard](create-dashboard.md#id-6.-add-filters-for-readers) to learn how to add date filters and value filters.

Global variables suit values that many panels share and that change rarely, such as a speed limit or a minimum trip length. Values of global variables are text. Convert a number with a function such as `toUInt32(${min_distance_m})`. A parameter of the panel or of the report replaces a global variable with the same name.

## 5. Group values by time

A chart over time needs one row per period, for example per hour, per day, or per week. ClickHouse has a function for each period:

| Period | Function |
| --- | --- |
| 15 minutes | `toStartOfInterval(t, INTERVAL 15 MINUTE)` |
| Hour | `toStartOfHour(t)` |
| Day | `toDate(t)` or `toStartOfDay(t)` |
| Week, starting on Monday | `toMonday(t)` |
| Month | `toStartOfMonth(t)` |

The times in the tables are UTC. Convert the time to your time zone before you group it, because a UTC day starts at a different local hour. The following query counts the trips that started in each local hour of the last 24 hours:

```sql
SELECT
    toStartOfHour(toTimeZone(start_time, 'Europe/Madrid')) AS bucket_start,
    count() AS value
FROM report.trips_stops
WHERE kind = 'trip'
  AND start_time >= now() - INTERVAL 24 HOUR
GROUP BY bucket_start
ORDER BY bucket_start
```

The result has one row per hour that has trips:

| bucket_start | value |
| --- | --- |
| 2026-09-28 06:00:00 | 7 |
| 2026-09-28 07:00:00 | 15 |
| 2026-09-28 08:00:00 | 12 |

An hour without trips has no row in the result.


For sensor values, don't group `telemetry` yourself. The sensor tables of the `report` database already group the values into periods of 1 minute, 10 minutes, 30 minutes, 1 hour, 4 hours, and 1 day. Select the period with `interval_sec`, as in the line chart query in [Match the result to the panel type](#id-2.-match-the-result-to-the-panel-type). Choose the longest period that still gives the chart enough points. The [report database](../data-access/data-schema/transformation-layer/report.md#sensor-readings) page lists the periods.

## 6. Convert units in the query

Dashboard Studio shows the values that the query returns, without converting them. The tables store distance in meters, speed in km/h, and durations in seconds. The column names say the unit: `distance_m`, `max_speed_kmh`, `duration_s`.

To show other units, convert them in the query and put the unit in the column name. The following query returns the distance of the last 7 days in miles:

```sql
SELECT round(sum(distance_m) / 1609.344, 1) AS "Distance (mi)"
FROM report.trips_stops
WHERE kind = 'trip'
  AND start_time >= now() - INTERVAL 7 DAY
```

The result has one row:

| Distance (mi) |
| --- |
| 7982.1 |

Divide meters by 1,609.344 for miles, km/h by 1.609344 for mph, and seconds by 3,600 for hours.

## Fix a query that fails



A panel that fails shows the error message of the database. Find the problem that matches what you see:

* **The panel shows nothing, or the wrong chart.** The result doesn't have the shape of the panel type. Compare the columns with **Dataset requirements** on the **SQL query** tab. For example, a pie chart needs a label column first and a value column second.
* **An error names an unknown table.** Write the table with its database, for example `report.trips_stops`, not `trips_stops`.
* **An error names an unknown column.** Check the spelling against the table on the [Data schema](../data-access/data-schema/) pages.
* **"Missing parameter value" when you test the query.** The query has a `${name}` that no source fills. Add a dashboard filter or a global variable with that name, or remove the parameter.
* **"No data available".** The query ran, but no row matched. Run it in the SQL editor without the `WHERE` conditions, then add them back one at a time to find the condition that removes every row.
* **The same row appears twice.** A `core_bdr` table was read without `FINAL`. See [Data schema](../data-access/data-schema/#read-the-current-version-of-each-row).
* **The query runs too long.** A panel query stops after 10 seconds, and a query in the SQL editor or in a report stops after 30 seconds. Add a condition on the time column so that the query reads a short period. Where a `report` table has the values, read that table instead of `telemetry`. An administrator can change both limits with a global variable named `sql_timeout_ms`, in milliseconds.

A table panel shows at most 10,000 rows, and every other panel shows at most 1,000. **Test query** reads up to 10,000 rows, so a test can show more rows than the panel. Dashboard Studio keeps each result for 5 minutes, so a panel can show a result up to 5 minutes older than the data in IoT Query Premium. Dashboard Studio doesn't keep the result of a failed query, so the query runs again the next time.

## Next steps

* [Data schema](../data-access/data-schema/): Learn the tables, how they join, and the rules for every ClickHouse query.
* [Create dashboard](create-dashboard.md): Put the queries on this page into panels and arrange them.
* [Create report](create-report.md): Build a report around one query with parameters that readers choose.
