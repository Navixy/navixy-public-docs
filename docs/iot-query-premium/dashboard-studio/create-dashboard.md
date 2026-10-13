---
description: >-
  Create a dashboard in Dashboard Studio: add panels, write their queries, and
  arrange the layout.
tags:
  - guide
  - preview
---

# Create dashboard

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

This guide takes you from an empty sidebar to a dashboard with panels that show your data. At the end, you have a saved dashboard with a panel, its query, and a layout that you arranged. It is for analysts who build dashboards in Dashboard Studio. To describe a dashboard in plain language instead, see [Build dashboard with AI](build-dashboard-with-ai.md).


Before you start, you need the following:

* The Admin or Editor role in Dashboard Studio. The Viewer role can open dashboards but can't create them.
* Basic knowledge of SQL. The tables are described in [Data schema](../data-access/data-schema/).

## 1. Create the dashboard

A dashboard belongs to a section of the sidebar or to the root of the sidebar. A section is a folder that groups dashboards and reports, for example one section for each team.

To create a dashboard, follow these steps:

1. In the sidebar, click **Tools**, then select **New dashboard**.
2. In **Title**, enter the name of the dashboard.
3. In **Section**, select the section for the dashboard, or leave **Root (no section)**.
4. Click **Create dashboard**.
   The dashboard opens with the message "This dashboard is empty. Start by creating a blank dashboard or uploading a schema file."
5. Click **Start with a blank dashboard**.

To start from a dashboard that someone exported from Dashboard Studio, click **Upload schema file** instead and select the JSON file.

To create a section first, click **Tools**, select **New section**, enter a name, and click **Create section**.

## 2. Add a panel


A panel shows the result of one query. The type of the panel decides how the result looks. **Stat** shows one number, and **Bar chart** and **Pie chart** compare values. **Table** lists rows, **Map** shows positions, and **Text** shows your own text without a query.

To add a panel, follow these steps:

1. If the editing toolbar isn't shown, click **Enter edit mode**.
2. In the editing toolbar, click **New panel**.
3. In **Panel type**, select the type of the panel, for example **Bar chart**.
4. In **Size preset**, select **Small**, **Medium**, or **Large**.
5. Click **Add panel**.
   The new panel appears on the dashboard, without a query.

A **Text** panel runs no query. Its content has three modes: **Markdown**, the default, **HTML**, and **Plain text**. Markdown and HTML are formatted, but before Dashboard Studio shows them, it removes scripts, event handlers, style tags and style attributes, and forms. So inline styles, scripts, and forms don't work. **Plain text** appears exactly as you typed it, without formatting.

## 3. Write the panel query


Each panel runs its own query when the dashboard opens. The result must have the shape that the panel type needs. For example, a bar chart reads the category from the first column and the value from the second column.


To write the query of a panel, follow these steps:

1. Point to the panel and click **Edit panel**.
2. On the **Panel properties** tab, enter the **Panel title**.
3. Go to the **SQL query** tab.
4. To see the result that the panel type needs, read **Dataset requirements**. Then enter the query in **SQL query**.
5. Click **Test query**.
   The first rows of the result appear under **Test results**.
6. Click **Save changes**.
   The panel shows the result of the query.

The following query counts the trips of each day over the last 30 days, for a **Bar chart** panel. It groups the trips by local day in Madrid. Replace `Europe/Madrid` with your own time zone:

```sql
SELECT
    toDate(toTimeZone(start_time, 'Europe/Madrid')) AS category,
    count() AS value
FROM report.trips_stops
WHERE kind = 'trip'
  AND start_time >= now() - INTERVAL 30 DAY
GROUP BY category
ORDER BY category
```

The result has one row per day, and the bar chart shows one bar for each row:

| category | value |
| --- | --- |
| 2026-09-26 | 41 |
| 2026-09-27 | 18 |
| 2026-09-28 | 37 |

To show a line instead of bars, select **Line chart** or **Time series** in **Panel type** on the **Panel properties** tab. [Write SQL queries](write-sql-queries.md) gives a query for every panel type.

## 4. Set how the panel looks

The **Visualization settings** tab of the panel editor changes how a result looks, not which data it shows. The settings depend on the panel type: a bar chart has **Stacking mode** and **Sort order**, and a table has **Rows per page** and **Visible columns**.

To change how a panel looks, open the panel with **Edit panel**, change the settings on the **Visualization settings** tab, and click **Save changes**.

To hide a table column, clear its checkbox in **Visible columns**, in the **Table configuration** section. To show the column again, select the checkbox. A hidden column stays in the query, so dashboard filters can still use it. Readers don't see the column in the table, and the files from **Export CSV**, **Export Excel**, and **Export as PDF** don't include it. At least one column stays visible.

**Visible columns** lists the columns from the panel's last result. For a new panel, or after you change the query, click **Test query** on the **SQL query** tab first.

## 5. Arrange the layout

The dashboard is a grid of 24 columns, and panels snap to it. Dashboard Studio saves each change to the layout as you make it.

To arrange the panels, stay in edit mode and do any of the following:

* To move a panel, drag it to its new place.
* To resize a panel, click the panel to select it, then drag its edge or its corner.
* To group panels, click **New row** in the editing toolbar, then drag panels into the row. Readers can collapse a row to hide its panels.
* To rename, pack, or delete a row, click **More options** in the row header and select **Rename**, **Pack row**, or **Delete row**. **Pack row** removes the gaps between the panels of the row.
* To remove the empty spaces of the whole dashboard, click **Tidy up layout**.
* To copy a panel with its query, click the panel to select it, then click **Duplicate panel**.

To reverse a change, click **Undo**. To repeat a change that you reversed, click **Redo**. When the layout is ready, click **Exit edit mode**.

**Undo** covers changes to the layout: moving, resizing, adding, duplicating, and deleting panels, and adding, moving, renaming, collapsing, and deleting rows. It keeps up to 50 changes, and a new change clears **Redo**. Opening or reloading the dashboard starts an empty history. Saving in the panel editor or in the **Dashboard filters** window can't be undone, and it clears the **Undo** and **Redo** history.

## 6. Add filters for readers

A dashboard filter lets readers narrow the panels without editing the dashboard. A date filter selects a period, for example, the last 7 days. A value filter selects values in one column, for example, a few assets. Readers find the filters in **Parameters**, change them, and click **Apply**.

You add, change, and delete filters in the **Dashboard filters** window. If the editing toolbar isn't shown, click **Enter edit mode** first.

### Add a date filter

A date filter changes only the panels that use it. A filter with the variable name `period` gives the panel queries two parameters: `${period_from}` and `${period_to}`.

To add a date filter, follow these steps:

1. In the editing toolbar, click **Dashboard filters**.
2. Click **Add date filter**.
3. Enter a **Label** and a **Variable name**.
4. In **Default range**, select the period that the dashboard opens with.
   The options are **Today**, **Yesterday**, **Last 7 days**, **Last 30 days**, **Last 90 days**, and **This month**.
5. Click **Add filter**.
6. Click **Save changes**.

To make a panel use the filter, do one of the following:

* Write the filter's parameters in the panel query, as [Write SQL queries](write-sql-queries.md#id-4.-add-parameters) shows.
* Open the panel with **Edit panel**, go to the **Filters** tab, turn on the filter, and select the result column that it filters.

The variable name starts with a letter and has only letters, digits, and underscores. Each filter needs its own variable name. The label can have up to 120 characters, and the variable name can have up to 63 characters.

### Add a value filter

A value filter applies to the panels whose result has the filter's column when you add the filter. Panels that show one number don't get value filters. Readers select one or more of the values that the panels return when the dashboard opens. **All**, the default, shows every row.

To add a value filter, follow these steps:

1. In the editing toolbar, click **Dashboard filters**.
2. Click **Add value filter**.
3. In **Column**, select the column that readers filter on.
   Dashboard Studio fills in **Label** and **Variable name** from the column name. You can change them.
4. Click **Add filter**.
5. Click **Save changes**.

To stop a panel from using the filter, open the panel with **Edit panel**, go to the **Filters** tab, and turn off the filter.

A panel that you add later doesn't use an existing value filter. To include the panel, open the filter with the edit icon in **Dashboard filters**, click **Update filter**, and click **Save changes**. When you update a value filter this way or change its column, the filter applies again to every panel that has the column, including panels where you turned it off.

### Change or delete a filter

To change a filter, follow these steps:

1. In the editing toolbar, click **Dashboard filters**.
2. Click the edit icon next to the filter.
3. Make the change and click **Update filter**.
4. Click **Save changes**.

To delete a filter, click the delete icon next to it. The row then says that the filter was deleted and shows **Undo**, which brings the filter back to its place. The row's **Undo** stays until you save or close the window.

Changes in the **Dashboard filters** window reach the dashboard only when you click **Save changes**. Dashboard Studio saves them immediately, and **Undo** in the editing toolbar can't reverse them. To leave without saving, click **Cancel** and confirm with **Discard**. **Discard** removes every change that you made in the window since the last save. Filters that you deleted come back.

{% hint style="warning" %}
When you delete a filter, the panels stop using it. Dashboard Studio doesn't warn about a panel query that still contains the deleted filter's parameters, such as `${period_from}`. Readers then see that parameter in **Parameters** as a text field, and the panel shows an error until someone enters a value. Remove the parameter from each query that contains it.
{% endhint %}

## 7. Check and share the dashboard

Open the dashboard from the sidebar and check each panel against what you expect.

To find a dashboard later, use the search box in the sidebar, "Search data, dashboards, reports, SQL…". It searches the titles of your own dashboards and reports, the descriptions of dashboards, and panel titles. With the Admin or Editor role, it also searches SQL: the queries of reports, panels, and dashboard variables. It skips deleted items. Every word that you enter must appear, in any letter case. The list shows up to 8 results, with title matches first. A panel that shows "No data available" ran its query without error, but the query returned no rows. [Write SQL queries](write-sql-queries.md#fix-a-query-that-fails) explains what to check.

Dashboard Studio runs the queries each time that someone opens the dashboard. A result can be up to 5 minutes old, because Dashboard Studio keeps results for 5 minutes. A query that failed runs again the next time that someone opens the dashboard.


To share the dashboard, do one of the following:

* To send a static copy, exit edit mode, wait until the panels load, and click **Export as PDF**.
* To give the dashboard to another Dashboard Studio user, or to keep a copy, enter edit mode, click **Full schema**, and click **Export**. Dashboard Studio downloads the dashboard as a JSON file, with its panels, queries, and settings. To load such a file into a dashboard, click **Import** in the same window, select the file, and click **Save changes**. **Import** replaces the whole dashboard, every panel included, with the contents of the file. To keep the dashboard as it was, click **Cancel**.
* To export the rows of one panel, point to the panel, click the download icon, and select **Export CSV**. Table panels also have **Export Excel**. In the window that opens, click **Export as .csv** or **Export as .xlsx**. An Excel file can have a header with the title and the description of the panel, as described in [Create report](create-report.md#id-6.-export-the-report).

{% hint style="success" %}
You have a dashboard with a panel, its query, and a layout, saved in the sidebar.
{% endhint %}

## Next steps

* [Write SQL queries](write-sql-queries.md): Write queries for every panel type, with parameters and time periods.
* [Build dashboard with AI](build-dashboard-with-ai.md): Describe what you want to monitor and let the assistant build the panels.
* [Create report](create-report.md): Create a report with a table, a chart, and a map from one query.
