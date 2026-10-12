---
description: >-
  Build fleet dashboards by describing them to an AI assistant or by writing
  SQL, then arrange the panels and export reports to Excel, CSV, or PDF.
tags:
  - overview
  - preview
noIndex: true
---

# Dashboard Studio

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

Dashboard Studio builds the fleet dashboards that no ready-made view has, such as the distance of each asset group per week, from IoT Query Premium data. You describe what you want to see in plain language and the AI assistant builds the dashboard, or you write the SQL queries yourself. Dashboards in Location Intelligence shows a fixed set of views, and Dashboard Studio shows the views that you define.

## What you can do with Dashboard Studio

**Describe a dashboard and let the assistant build it.** When a manager asks for a new view, describe what you want to monitor in plain language, and the AI assistant builds the panels and their queries. A question about your fleet, such as which vehicles are offline now, becomes a panel that shows the answer. You check the result against your own data and ask for changes in the same conversation. To save the dashboard, you need the Admin or Editor role. After you save it, you can move, resize, or edit its panels in the dashboard editor, as with any other dashboard. [Build dashboard with AI](build-dashboard-with-ai.md) takes you from the first request to a saved dashboard.

**Build dashboards from your own queries.** For a dashboard that your team opens every day, write the queries yourself. Each panel of a dashboard runs one SQL query and shows the result as a stat tile, a chart, a table, a map, or text, so you don't need a separate business intelligence (BI) tool. Each time that someone opens the dashboard, it runs its queries again. Dashboard Studio keeps each result for up to 5 minutes. See [Create dashboard](create-dashboard.md) and [Write SQL queries](write-sql-queries.md).

**Create reports to export.** When someone needs the numbers as a file, create a report. A report in Dashboard Studio is a page with one query. It is separate from Reports in Location Intelligence. A report runs its query and shows the result as a table, a chart, and a map together. Readers choose the values of its parameters, such as a period, and export the result to Excel, CSV, HTML, or PDF. See [Create report](create-report.md).

## Who it is for

Dashboard Studio is for analysts and for the fleet managers and operations teams who read their dashboards:

* **Analysts** write the queries and build dashboards and reports for the rest of the organization, instead of setting up a separate BI tool.
* **Fleet managers and operations teams** open the dashboards to monitor assets, trips, and sensor values, instead of waiting for exported reports. They choose the period or the values that a dashboard shows, and they don't need SQL to read a dashboard. They can also describe a dashboard to the AI assistant and preview the result.

To write or change a query, you need basic knowledge of SQL. What each person can do depends on their role in Dashboard Studio:

| Role | What the role can do |
| --- | --- |
| Viewer | Open dashboards and reports, choose parameter values, export results, and talk to the AI assistant and preview a result |
| Editor | Everything that a viewer can do, and also create and change dashboards, reports, and sections (folders in the sidebar), save dashboards that the AI assistant builds, and run queries in the SQL editor |
| Admin | Everything that an editor can do, and also manage global variables: values that every query can use |

## Core concepts

* **Dashboard:** A page of panels on a grid. You move and resize the panels, and group them in rows that readers can collapse.
* **Panel:** The result of one query. A Text panel is the exception: it shows text and runs no query. The type of the panel decides how the result looks, and each type needs its own shape of result.
* **Query:** A SQL `SELECT` statement over the tables of [Private Telematics Lakehouse (PTL)](../data-access/). A query can have parameters, such as the time range that a reader chooses. Dashboard Studio only reads data: a query can't change it.
* **Report:** A page with one query, shown as a data table, a chart, and a location map.
* **Section:** A folder of dashboards and reports in the sidebar. Sections can't contain other sections.

You can add these panel types to a dashboard:

| Panel type | What it shows | Example |
| --- | --- | --- |
| Stat | One number | Trips this month |
| Bar chart | Values by category or by date | Distance per asset |
| Pie chart | How a total splits into parts | Devices by motion state |
| Table | Rows with several columns | The last 100 trips |
| Text | Headings and explanations, without a query | Instructions for readers of the dashboard |
| Map | Points at their coordinates | The current position of each asset |

A panel can also show a line chart or a time series. You choose these types in **Panel type** on the **Panel properties** tab of the panel editor, after you add the panel.

Dashboards and reports suit different jobs:

| Property | Dashboard | Report |
| --- | --- | --- |
| Queries | One query for each panel | One query for all views |
| Layout | Panels that you arrange on a grid | A data table, a chart, and a location map |
| Export | PDF of the dashboard, CSV of a panel, Excel of a table panel, JSON schema | Excel, CSV, HTML, PDF |
| Best for | Monitoring several measures on one page | One set of data to explore and share |

## Open Dashboard Studio

Dashboard Studio is a separate web service with its own address, and it works with IoT Query Premium data only. You need IoT Query Premium for your workspace. The data on a dashboard is as current as the PTL tables. See [How often the data is updated](../data-access/#how-often-the-data-is-updated). Open it in a web browser. After you open it, the **Home** page offers the AI assistant, and the sidebar lists the dashboards and reports of your organization.

Dashboard Studio reads the [PTL](../data-access/) tables of your workspace, the same tables that BI tools and your own applications query. [Data schema](../data-access/data-schema/) describes these tables and the rules that every query needs.

## Where to start

Start with [Build dashboard with AI](build-dashboard-with-ai.md). It takes you from your first request to a saved dashboard. To build the panels yourself, start with [Create dashboard](create-dashboard.md).

## Section contents

* [Create dashboard](create-dashboard.md): Create a dashboard, add panels, write their queries, and arrange the layout.
  * [Build dashboard with AI](build-dashboard-with-ai.md): Describe what you want to monitor and let the assistant build the dashboard for you.
* [Write SQL queries](write-sql-queries.md): Write queries that return the result that each panel type needs, with parameters and time periods.
* [Create report](create-report.md): Create a report from one query, with parameters that readers choose.
* [Open-source Dashboard Studio](open-source-studio.md): Run your own copy of Dashboard Studio from its open-source code.
* [Data schema](../data-access/data-schema/): Learn which tables PTL has and how they join.
