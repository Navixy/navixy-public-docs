---
title: Creating reports
description: Generate exportable reports with coordinated data tables, charts, and location maps from a single SQL query. Export to HTML, Excel, or PDF
---

# Creating reports

Reports give you a structured way to analyze IoT Query data through three coordinated views from a single SQL query: a paginated data table, a chart, and an interactive map. Use reports when you need a fixed, exportable output rather than a free-form dashboard canvas.

Custom SQL reports adapt instantly to any business question by adjusting queries and parameters:

* **Automation** saves time by reusing queries and integrating with BI tools for quick updates.
* **Deep analysis** is possible by combining, aggregating, and segmenting data from multiple sources.
* **Accuracy and relevance** are ensured by working directly with raw, up-to-date database information.
* **Interactive visualizations** let users explore data dynamically by tweaking axes, filters, and metrics.

Watch this video to get familiar with report creation in the Dashboard Studio app interface:

{% embed url="https://youtu.be/o2j96_0gp-w?si=yBE9SrsET6gZa-EN" %}
Creating reports in Dashboard Studio step-by-step
{% endembed %}

## Reports vs. dashboards

Both reports and dashboards run SQL queries against your IoT Query database and render results as visualizations. The key difference is in how queries and output are structured:

|             | Reports                                | Dashboards                         |
| ----------- | -------------------------------------- | ---------------------------------- |
| SQL queries | One query powers all views             | Each tile has its own query        |
| Layout      | Fixed: data table, chart, location map | Drag-and-drop canvas               |
| Export      | HTML, Excel, PDF                       | JSON schema only                   |
| Use case    | Exportable, single-dataset analysis    | Multi-panel operational monitoring |

Use reports when a single dataset needs to be explored as a table, visualized as a chart, and mapped simultaneously, or when the output needs to be exported and shared outside Dashboard Studio. Use dashboards when you need multiple independent visualizations on a single canvas, for example: combining a trip summary stat tile, a fuel consumption chart, and a zone visit table in one view.

For details on creating dashboards, see [Creating dashboards](creating-dashboards.md).

## How to create a report

{% stepper %}
{% step %}
#### Step 1: Open the report editor

In the left sidebar, click **Tools**, then select **New report**.

Dashboard Studio prompts you to choose a section from your shared menu structure. Select an existing section or choose Root to place the report at the top level.
{% endstep %}

{% step %}
#### Step 2: Enter basic information

The editor opens with two fields at the top:

* **Title** (required): the name displayed in the menu and export headers.
* **Description** (optional): additional context visible in the editor.
{% endstep %}

{% step %}
#### Step 3: Write the SQL query

1. Click the **SQL Query** tab and enter a SELECT statement against your IoT Query tables. The query defines all three components (table, chart, and map), so include every column you intend to use across all views, for example:

{% code title="Basic report query example" expandable="true" %}
```sql
SELECT
    t.device_id,
    o.object_label,
    -- Convert scaled integer coordinates to decimal degrees
    t.latitude::float / 10000000 AS latitude,
    t.longitude::float / 10000000 AS longitude,
    -- Convert scaled integer speed to km/h
    t.speed::float / 100 AS speed,
    t.device_time
FROM raw_telematics_data.tracking_data_core t
JOIN raw_business_data.objects o ON t.device_id = o.device_id
LIMIT 1000
```
{% endcode %}

{% hint style="info" icon="lightbulb-exclamation" %}
#### Some tips for query building:

* Add a LIMIT clause while configuring the report. It reduces query execution time and speeds up component preview. Remove or increase the limit before saving if you need the full dataset available for download.
* After writing the query, you can click **Detect Columns** to check what columns are actually retrieved with this query. This helps to set up the column selectors in the **Components** tab. The step is optional. You can configure components manually, but running it first simplifies axis and field selection.
* Any SQL query used in a dashboard panel is a valid starting point for a report. Copy the query from a panel and adjust it to fit the report's goal. For example, adding columns needed for the location map, or removing aggregations that don't apply to a table view.
* The IoT Query's Recipe Book contains general and industry-specific query examples ready to use as a foundation for reports. See [SQL Recipe Book](../example-queries/) for the full list.
{% endhint %}
{% endstep %}

{% step %}
#### Step 4: Configure components

Click the **Components** tab. Three components are available: **Table**, **Chart**, and **Map**. Each has a toggle to include or exclude it from the report. You can remove any component you do not need.

{% tabs %}
{% tab title="Data table" %}
The data table displays query results in paginated rows.

* **Page Size**: sets how many rows appear per page in the interactive view. This does not affect the export, which always contains the full result set.
* **Show Totals Row**: adds a summary row at the bottom of the table when enabled.
{% endtab %}

{% tab title="Chart" %}
Two chart types are available:

* **Time series (line)**: suited for data plotted over time.
* **Bar chart**: suited for categorical comparisons (also referred to as a column chart).

Configure the axes:

* **X-axis column**: typically a time column such as `device_time`.
* **Y-axis columns**: one or more numeric columns, such as speed or `fuel_level`.
* **Group by**: optionally split series by a categorical column, such as `object_label`, to display a separate line or bar group per device.
{% endtab %}

{% tab title="Location map" %}
The location map plots data points using GPS coordinates.

Dashboard Studio can auto-detect latitude and longitude columns from your query result. When **Auto-detect GPS columns** toggle is enabled, the columns needed to define location (usually `latitude` and `longitude`) are selected automatically if your query returns them.

You can also disable the **Auto-detect** option and select the needed columns manually.
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
#### Step 5: Save the report

Click Save. The report appears in the menu section you selected in step 1 and is accessible to all users who have access to that section.
{% endstep %}
{% endstepper %}

## Viewing a report

After you create a report it opens immediately. You will see all configured components rendered with live data from your IoT Query database. The report header displays the last updated timestamp, total row count, and query execution time.

The report page also exposes the SQL query directly, so you can update it whenever you need to.

1. Expand the **SQL Query** section to view or edit the statement inline.
2. Apply the changes:
   1. Click **Save** to store changes without running. You will need to click **Refresh** to re-execute the query and update all views with current data.
   2. **Save & Run** to apply the updated query immediately.

### Data table

The **Data Table** section shows query results with the section header displaying the total row count returned by the query. The interactive view displays only a limited number of rows, the footer confirms the exact number. Use the **Download** dropdown to export the complete result set as Excel (.xlsx) or CSV (.csv) regardless of how many rows the interactive view displays.

If you need locations instead of raw coordinates, check the **Geocode to address** box in the top-right of the **Data Table** header. When enabled, it resolves coordinate columns into readable street addresses.

{% hint style="warning" %}
Address resolution places a significant load on the server. Use this option only with aggregated or filtered datasets. For raw telematics data with tens of thousands of rows, keep coordinates in numeric form.
{% endhint %}

### Chart

The Chart section renders below the data table. It inherits the settings you applied in the report editor, but you can also adjust axis configuration directly in the view:

* **X-axis**: select the column to plot on the horizontal axis.
* **Y-axis**: select the column to plot on the vertical axis.
* **Group by**: optionally split series by a categorical column, such as `object_label`, to display a separate line or bar group per device.

The chart displays an auto-generated title describing the current axis and grouping selection, for example: `speed over device_time (grouped by object_label)`. When you modify axis settings without saving, an **Unsaved changes** indicator appears next to the **Save Chart Settings** button. Click it to keep the configuration changes in the report.

### Location map

The **Location Map** section displays each query row as a point at its GPS coordinates. The section header shows the number of points plotted. GPS columns are defined upon report creation (automatically or manually), it's not possible to edit them afterwards. If the query returns no recognized coordinate columns, the section shows "No GPS coordinates detected in query results."

For timestamped telematics data, the plotted points represent the full path of an object over the queried period.

{% hint style="info" %}
The map is interactive: zoom and pan to inspect individual points. Click **Show all** to zoom out and fit all plotted points within the visible map area.
{% endhint %}

## Exporting a report

Dashboard Studio provides two levels of export.

### Data table export

Use the **Download** dropdown within the **Data Table** section to export table data only. The export always contains the complete result set returned by the query, regardless of the display limit in the interactive view.

### Full report export

Use the buttons in the report header to export all components together.

| Button                | Output                                                        |
| --------------------- | ------------------------------------------------------------- |
| Excel → Excel (.xlsx) | Spreadsheet with the complete data table                      |
| Excel → CSV (.csv)    | Comma-separated values file with the complete data table      |
| HTML                  | Interactive file with the data table, chart, and location map |
| PDF                   | Static document with the data table, chart, and location map  |

{% hint style="info" %}
The exported HTML file preserves interactivity: the chart supports hover tooltips and the location map supports zoom and pan.
{% endhint %}

## Editing and deleting a report

To edit a saved report, you don't need a separate editor dialog anymore. Open the report from the menu and modify the SQL query or component settings directly in it. The only exception is location columns, they are defined once upon report creation.

To delete a report, click **Tools** in the left sidebar, then select **Edit menu**. Locate the report in the menu tree and click the three-dot menu next to its name, then confirm deletion.
