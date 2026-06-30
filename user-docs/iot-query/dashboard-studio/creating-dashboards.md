---
title: Creating dashboards
description: Add visualization panels to a canvas, configure SQL queries, and arrange layouts for real-time fleet monitoring and operational decision-making
---

# Creating dashboards

Dashboard Studio transforms IoT Query data into interactive visual reports. You build reports by adding panels to a canvas, writing SQL queries, and organizing the results into a navigable menu structure.

Custom Dashboards provide real-time operational visibility by consolidating key metrics, KPIs, and live data streams into a single, unified workspace.<br>

* **Instant situational awareness** is achieved through live widgets that automatically refresh, helping teams monitor fleet performance, assets, and operations without running manual reports.
* **Action-oriented decision-making** is supported by interactive filters, drill-down capabilities, and dynamic components that allow users to quickly identify trends, anomalies, and bottlenecks.
* **Personalized workspace configuration** enables teams to tailor layouts, widgets, and metrics to specific roles, departments, or business objectives.
* **Continuous performance tracking** ensures stakeholders always have up-to-date insights, turning data into a proactive management tool rather than a retrospective analysis.

Watch this video to get familiar with the Dashboard Studio app interface and learn how to create your own custom fleet dashboard:

{% embed url="https://youtu.be/dyBk7XAZsWs?si=RIaMyZBXO1Gdl817" %}
Dashboard Studio app interface, built-in dashboard templates, custom dashboard creation.
{% endembed %}

## What are dashboards

Dashboards contain panels that display data visualizations. Each panel runs a SQL query against your IoT Query database, the same Raw data and Transformation layers you access through SQL clients. You arrange panels on a canvas, configure their appearance, and save the complete report to your menu. Dashboard Studio updates panels continuously to reflect current IoT Query data.

{% hint style="info" %}
#### Before you start

Dashboard Studio requires IoT Query access to function. Enable IoT Query before building reports. If you don't have access yet, contact us for activation at [iotquery@navixy.com](mailto:iotquery@navixy.com).
{% endhint %}

### Dashboards vs. reports

Dashboard Studio provides two ways to build analytics from IoT Query data. Choosing the right one depends on how your query results are structured and how you intend to share the output.

|             | Dashboards                         | Reports                                |
| ----------- | ---------------------------------- | -------------------------------------- |
| SQL queries | Each panel has its own query       | One query powers all views             |
| Layout      | Drag-and-drop canvas               | Fixed: data table, chart, location map |
| Export      | JSON schema only                   | HTML, Excel, PDF                       |
| Use case    | Multi-panel operational monitoring | Exportable, single-dataset analysis    |

Use dashboards when you need multiple independent visualizations on a single canvas, for example: combining a trip summary stat tile, a fuel consumption chart, and a zone visit table in one view. Use reports when a single dataset needs to be explored as a table, visualized as a chart, and mapped simultaneously, or when the output needs to be exported and shared outside Dashboard Studio.

For details on how to configure reports, see [Creating reports](https://claude.ai/docs/analytics/dashboard-studio/creating-reports).

## What can you build?

Dashboard Studio provides five visualization types. Each works best for specific analytical questions.

* **Stat tiles** display single key metrics like total distance or vehicle count. Use them to highlight critical numbers at the top of your report.
* **Bar charts** compare values across categories or show trends over time. They work well for comparing fleet utilization across regions or tracking trips per day.
* **Pie charts** show how a total breaks into parts. Use them to display fuel consumption by vehicle type or alert distribution by severity.
* **Tables** present detailed records with multiple attributes. They suit scenarios where users need to see individual records, such as recent alerts with timestamps, vehicle IDs, and messages.
* **Text panels** add explanatory content without querying data. Use them for instructions, section headers, or context using Markdown formatting.

Each visualization type serves specific analytical purposes. Stat tiles work well for KPIs like total fleet size or average utilization. Bar charts help you compare performance across regions or track metrics over time. Pie charts show how totals divide into categories, such as fuel consumption by vehicle type. Tables present detailed records when users need to see multiple attributes together. Text panels add context, instructions, or section headers to your reports.

<figure><img src="../.gitbook/assets/dashboard_example.webp" alt=""><figcaption></figcaption></figure>

Query requirements vary by visualization type. Stat tiles need one numeric value. Bar and pie charts require exactly two columns: categories and values. Tables accept any number of columns. Text panels don't query data at all. For complete query requirements and examples, see [Writing SQL queries.](writing-sql-queries.md#how-to-write-sql-for-visualizations)

## Creating dashboards

Let's walk through the steps from an empty canvas to a structured and informative dashboard.

### How to create your first dashboard

{% stepper %}
{% step %}
**Create a new dashboard**

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

* Click <img src="../.gitbook/assets/image (1).png" alt="" data-size="line"> in the lower-left corner of the app and select **New dashboard**.
* Add **Title**.
* Click **Create dashboard**, a **Get Started** message appears.
* Select **Start with Blank Dashboard** to open an empty canvas.
{% endstep %}

{% step %}
**Add a panel**

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Now you can add your first panel by selecting a visualization type.

1. Select <img src="../.gitbook/assets/image (2).png" alt="" data-size="line"> on the editing toolbar.
2. Choose **Panel Type** and click **Add panel**. The new panel appears on the canvas.
{% endstep %}

{% step %}
**Configure the visual**

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Now it's time to add the diagram settings:

1. Hover your mouse over the tile and click <img src="../.gitbook/assets/image (3).png" alt="" data-size="line">.
2. Dashboard Studio opens a panel editor with three tabs:
   1. **Panel Properties** controls the title and type
   2. **SQL Query** defines what data to display
   3. **Visualization Settings** adjust how that data appears
3. Name your panel in **Panel Properties**.
4. Write a **SQL query** that fetches data from IoT Query tables, and test it to verify the results.
5. Adjust the **Visualization Settings**.
6. When satisfied, save the panel to add it to your canvas.
{% endstep %}
{% endstepper %}

### How to write effective SQL queries

Now that you understand the basic workflow, let's explore SQL queries in detail. Write PostgreSQL queries that reference IoT Query tables in the SQL Query tab. All Raw data and Transformation layer tables are available. Test queries before applying them to verify results match your visualization requirements.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Query results must match visualization requirements. The SQL Query tab includes a "Dataset Requirements" section explaining what your chosen visualization expects. Use Common Table Expressions (CTEs) to define parameters at the query start for flexibility. This approach keeps your queries maintainable and allows you to adjust parameters without rewriting complex logic.

For detailed query patterns, examples, and best practices, see [Writing SQL queries](writing-sql-queries.md).

### How to arrange panels on the canvas

Drag panels to reposition them on the canvas. Panels snap to a grid that keeps everything aligned. Resize panels by dragging their corners. The canvas uses a 24-column grid, so panels can occupy 1 to 24 columns in width.

Group related panels into rows for organization. Drag a panel to the canvas edge until a blue line appears, then release to create a new row. Add more panels to the row by dragging them beside existing ones. Collapse rows using the arrow icon at the row's left edge.

### How to customize visualization appearance

Configure visualization-specific options in the **Visualization Settings** tab:

* **Bar charts:** horizontal or vertical orientation
* **Pie charts:** standard or donut modes
* **Tables:** sortable columns, pagination, row highlighting

These settings control how data appears visually but don't change what data is displayed. The available options depend on your panel type.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption><p>Table Visualization Settings example</p></figcaption></figure>

### How to organize dashboards in sections

Dashboard Studio uses a hierarchical menu in the left sidebar. Create sections to group related dashboards/reports. For example, create a "Fleet Management" section containing reports for vehicle status, utilization, and maintenance.

{% columns %}
{% column width="41.66666666666667%" %}
Use the menu editing mode to create sections, drag reports between them, and reorder items. Sections cannot be nested. You have one organizational level: sections contain reports, reports contain panels.
{% endcolumn %}

{% column width="58.33333333333333%" %}
<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

### How to save and share dashboards

Save your dashboard to store it in your menu. Dashboard Studio prompts for a name and section location. Reports save automatically as you work.

Export reports to share them with other users or create backups.

To do it:

1. Click <img src="../.gitbook/assets/image (11).png" alt="" data-size="line"> to enter edit mode.
2. Click <img src="../.gitbook/assets/image (12).png" alt="" data-size="line"> to open the Full Schema window.
3. Select **Export**.

Dashboard Studio downloads a JSON file containing the complete report structure, including panels, queries, and visualization settings. You can import an existing schema as well.

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

## How data refresh works

Dashboards display current data from IoT Query. Dashboard Studio queries the database each time you open a report. Data freshness matches your IoT Query updates, which happen continuously as devices send readings.

You can refresh data manually using the **Refresh** button in the top toolbar.

{% hint style="info" %}
Dashboard Studio does not cache results. Each panel runs its query independently when the report loads or refreshes.
{% endhint %}
