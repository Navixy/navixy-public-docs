---
description: >-
  Create a report in Dashboard Studio from a SQL query, with parameters that
  the reader chooses.
tags:
  - guide
  - preview
---

# Create report

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

A report shows the result of one SQL query in three views: a data table, a chart, and a location map. In this guide, you create a report of stops. The reader of the report runs it for any period, asset, and minimum stop length that they choose. At the end, you have a saved report with parameters that anyone can run and export. This guide is for analysts who build reports in Dashboard Studio.

Before you start, you need:

* The Admin or Editor role in Dashboard Studio.
* A query that returns detailed rows with a time or category column, a numeric column, and coordinates. [Write SQL queries](write-sql-queries.md#id-3.-write-one-query-for-a-report) explains which columns each view needs.

## 1. Create the report

Like a dashboard, a report belongs to a section of the sidebar or to the root of the sidebar.

To create a report, follow these steps:

1. Click **Tools** on the sidebar, then select **New report**.
2. In **Title**, enter the name of the report. You can also enter it on the **New report** page.
3. In **Section**, select the section for the report, or leave **Root (no section)**.
4. Click **Continue**.
   The **New report** page opens, with **Basic information** and the **SQL query** and **Components** tabs.
5. Under **Basic information**, make sure that the **Title** is correct. Optionally, enter a **Description**.

## 2. Enter the query with fixed values

Enter the query with fixed values first, and add the parameters later in [Add the parameters](#id-4.-add-the-parameters). The report finds its columns by running the query once, and at that point the query runs without parameter values.

To enter the query, follow these steps:

1. On the **SQL query** tab, enter the query.
2. Click **Detect columns**.
   **Detected columns** lists the columns of the result. If the result has coordinates, **GPS columns detected** names them.

The following query returns the stops of the last 7 days that lasted at least 15 minutes. For each stop, it returns the asset, the start, the length, and the position:

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
    WHERE deleted_at IS NULL
) AS a ON a.id = toUUID(t.asset_id)
WHERE t.kind = 'stop'
  AND t.start_time >= now() - INTERVAL 7 DAY
  AND t.duration_s >= 15 * 60
ORDER BY t.start_time
LIMIT 1000
```

The result has one row per stop:

| asset | stop_start | stop_minutes | latitude | longitude |
| --- | --- | --- | --- | --- |
| Truck 12 | 2026-09-22 08:14:02.000 | 42 | 52.370216 | 4.895168 |
| Van 3 | 2026-09-22 09:31:47.000 | 17 | 52.090737 | 5.12142 |
| Truck 12 | 2026-09-22 12:05:30.000 | 64 | 52.379189 | 4.899431 |

`LIMIT 1000` keeps the report fast while you set it up. Raise it or remove it before you save if the report needs more rows.

## 3. Set up the table, the chart, and the map

The **Components** tab has one section for each view: **Table**, **Chart**, and **Map**. Each section has a switch that adds the view to the report or removes it.

To set up the views, follow these steps:

1. Go to the **Components** tab.
2. Under **Table**, set **Page size**, the number of rows on one page of the table. **Max rows** is the highest number of rows that the report reads when its query has no `LIMIT`. A `LIMIT` in the query replaces **Max rows**, up to 250,000 rows. To add a total at the bottom, turn on **Show the "Total" row**.
3. Under **Chart**, select the **Chart type**: **Time series (line)** or **Bar chart**.
4. In **X-axis column**, select `stop_start`.
5. In **Y-axis columns**, select `stop_minutes`. You can select more than one column.
6. If the switch under **Map** is off, turn it on. With **Auto-detect GPS columns** on, the map uses the detected `latitude` and `longitude`. To choose other columns, turn off **Auto-detect GPS columns** and select the **Latitude column** and the **Longitude column**.

The report page doesn't let you change the map columns later, so check them now.

## 4. Add the parameters

A parameter is a value that the reader of the report enters before the query runs. Write it in the query as `${name}`, outside quotes. For every parameter, the report page shows a field under **Parameters**.

The name decides the kind of field:

* A name that looks like a date or a time, such as `date_from`, `date_to`, `from`, `to`, `start_date`, or `created_at`, gets a date and time picker. The reader picks the time in the time zone of their Navixy account, and the query receives it in UTC, for example `2026-09-01T00:00:00.000Z`.
* Every other name gets a text field. The query receives the text as the reader entered it.

To add the parameters, follow these steps:

1. Go to the **SQL query** tab.
2. Replace each fixed value with a parameter, as in the query that follows.
3. Click **Save**.
   The report opens, with the **Parameters** form above the views.

The following query is the query from [Enter the query with fixed values](#id-2.-enter-the-query-with-fixed-values), with four parameters. `date_from` and `date_to` get date pickers, and `asset_name` and `min_stop_minutes` get text fields:

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
    WHERE deleted_at IS NULL
) AS a ON a.id = toUUID(t.asset_id)
WHERE t.kind = 'stop'
  AND t.start_time >= parseDateTime64BestEffort(${date_from}, 3, 'UTC')
  AND t.start_time < parseDateTime64BestEffort(${date_to}, 3, 'UTC')
  AND a.title = ${asset_name}
  AND t.duration_s >= toUInt32(${min_stop_minutes}) * 60
ORDER BY t.start_time
LIMIT 1000
```

For `Truck 12`, a period of one day, and at least 30 minutes, the result has one row per stop:

| asset | stop_start | stop_minutes | latitude | longitude |
| --- | --- | --- | --- | --- |
| Truck 12 | 2026-09-22 08:14:02.000 | 42 | 52.370216 | 4.895168 |
| Truck 12 | 2026-09-22 12:05:30.000 | 64 | 52.379189 | 4.899431 |

`parseDateTime64BestEffort` turns the UTC text of a date picker into a time, and `toUInt32` turns the text of `min_stop_minutes` into a number.

{% hint style="warning" %}
The report runs its query as soon as it opens. If a parameter has no value, this first run returns an error. The report shows the error under **Query error**. Enter the values under **Parameters** and click **Apply and run query**.

A user with the Admin role can give a parameter a starting value. To do this, add a global variable with the same name in **Settings**, on the **Configuration** tab. The field then starts with the value of the variable.
{% endhint %}

## 5. Run the report

To run the report with your own values, follow these steps:

1. Under **Parameters**, select the times for **Date From** and **Date To**.
2. Enter the values of the other parameters, such as **Asset Name** and **Min Stop Minutes**.
3. Click **Apply and run query**.
   The views show the new result, and the header shows **Last updated** with the time of the run.

The views show the result like this:

* **Data table** shows the rows, one page at a time. **Rows loaded** gives the number of rows in the result.
* **Chart** plots the Y-axis columns over the X-axis column. To change the chart, select other columns in **X-axis**, **Y-axis**, and **Group by**, and click **Save chart settings**. **Group by** draws one line or one group of bars for each value of a column, such as `asset`.
* **Location map** shows one point for each row. **Locations on the map** gives the number of points. For stops, each point is where an asset stood.

When **Group by** splits the chart into groups, the chart plots the first 10 groups. To choose other groups, click **Groups**, which shows how many groups are plotted out of the total. In the list, click a group to plot it or to remove it from the chart. To find a group, search for it by name.

Rows with no value in the **Group by** column belong to no group, and the chart leaves them out. **Select all** and **Clear** apply to the groups that match the search. **Select all** isn't available when it would plot more than 1,000 groups in total. The list shows up to 100 groups that aren't plotted, so search to find the others. To go back to the first 10 groups, click **Reset to default**. Dashboard Studio doesn't save the group selection. The selection resets when you open the report again or select another **Group by** column.

To run the report again with the same values, click **Refresh**.

## 6. Export the report

A report exports the complete result, not only the rows on the current page of the table.

To export, click one of these buttons in the report header:

* **Excel**, then **Excel (.xlsx)** or **CSV (.csv)**, for a file with the rows of the data table. In the **Export Excel** or **Export CSV** window, click **Export as .xlsx** or **Export as .csv**.
* **HTML** for a file with the data table, the chart, and the map. The chart and the map in the file stay interactive.
* **PDF** for a document with the data table, the chart, and the map.

The chart in an HTML or PDF file plots the same groups as the chart on screen. When the chart plots only some of the groups, the file gives the number of plotted groups and the total number of groups.

An Excel file can start with a header above the column names, with the title in the first row and the description in the second. To add it, turn on **Add header** in the **Export Excel** window. **Title** and **Description** start with the title and the description of the report, and **Start column** sets the column where the header starts. A CSV file has no header. To use the same settings for every Excel export of the report, select **Save as default for this item** before you export. Saving the settings needs the Admin or Editor role.

To export only the data table, click **Download** in the **Data table** section and select **Excel (.xlsx)** or **CSV (.csv)**. Then click **Export as .xlsx** or **Export as .csv** in the window that opens.

{% hint style="success" %}
You have a saved report with parameters, which anyone in Dashboard Studio can run with their own values and export.
{% endhint %}

## Change or delete the report later

To change the query, open the report, expand **SQL query**, and edit the statement. Click **Save and run** to save the query and run it immediately. Click **Save** to save the query and run it later with **Refresh**. To turn the map on or off, use the **Map** switch in the report header.

To delete the report, follow these steps:

1. Click **Tools** on the sidebar, then select **Edit menu**.
2. Click the more icon next to the report.
3. Select **Delete**.

## Next steps

* [Write SQL queries](write-sql-queries.md): Write queries with parameters, time periods, and the columns that each view needs.
* [Create dashboard](create-dashboard.md): Build a dashboard with several panels, each with its own query.
* [Data schema](../data-access/data-schema/): Find the tables and columns that a report query can read.
