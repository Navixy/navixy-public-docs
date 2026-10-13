---
description: >-
  Query your telematics and business data with SQL, build dashboards with an AI
  assistant, and connect BI tools and your own applications to the same tables.
tags:
  - overview
  - preview
noIndex: true
---

# IoT Query Premium

{% hint style="info" %}
IoT Query Premium is coming soon. This documentation describes the product as it works at release, so you can plan your dashboards and integrations now. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

IoT Query Premium answers the questions about your fleet that no ready-made view does, such as how long each asset group spends in each geo object per week. It gives you your fleet's data as SQL tables, and Dashboard Studio, with an AI assistant that builds dashboards from a request in plain language. Reports and Dashboards in Location Intelligence show fixed views of the same kind of data.

## What you can do with IoT Query Premium

**Explore the data with an AI assistant.** When a question comes up that no ready-made view answers, describe the dashboard that you need in [Dashboard Studio](dashboard-studio/), and the AI assistant builds its panels and queries. You can also write a SQL query, choose a chart, and place it on a dashboard yourself.

**Combine business and telemetry data.** For example, to compare a sensor reading with a value that you store on each asset, one query can join your assets, geo objects, and custom fields with the positions and sensor values of their GPS devices. [Data schema](data-access/data-schema/#how-tables-join) shows how the tables join.

**Use one set of tables in each tool that you connect.** When your company already uses a business intelligence (BI) tool, connect it to the tables that Dashboard Studio reads. Dashboard Studio, BI tools, and your own applications then calculate their results from the same data. The tables are stored in ClickHouse, a database built for analytical queries, so any BI tool with a ClickHouse connector can connect. [Use third-party BI tools](data-access/third-party-bi-tools/) has guides for Power BI, Apache Superset, Grafana, and Streamlit.

## Who it is for

Analysts, fleet managers and operations teams, and developers each use IoT Query Premium in their own way:

* **Analysts** who build dashboards and reports, instead of exporting data to spreadsheets. Start with [Dashboard Studio](dashboard-studio/). Open it, create a dashboard, and write the queries for its panels, or let the AI assistant write them. You need basic SQL to check and change the queries.
* **Fleet managers and operations teams** who open these dashboards, instead of waiting for one-off reports. They don't need SQL.
* **Developers, data engineers, and BI specialists** who use the data in their own BI tools, data warehouses, or applications, with SQL instead of an API. Follow [Private Telematics Lakehouse](data-access/) in order: [connect](data-access/connection-setup.md), learn the [data schema](data-access/data-schema/), and [use a third-party BI tool](data-access/third-party-bi-tools/).

## Core concepts

* **Private Telematics Lakehouse (PTL):** The data part of IoT Query Premium: your workspace's data as SQL tables, stored in ClickHouse. PTL is a lakehouse in this sense: it keeps raw data as it arrives, together with tables calculated from it.
* **Dashboard Studio:** The tool for analysis and dashboards, with an AI assistant. It reads the PTL tables.
* **Raw data layer:** Your business data from Business Data Repository and the positions and sensor values that your GPS devices send, stored as they arrive. Query it when you need every data point.
* **Transformation layer:** Tables that Navixy calculates from the raw data, such as trips, stops, sensor values per minute, and positions in geo objects. Query it for common questions. Telemetry arrives as the GPS devices send it, and Navixy recalculates these tables on a schedule. See [How often the data is updated](data-access/#how-often-the-data-is-updated).
* **Workspace:** A part of your organization with its own data. Each workspace has its own connection details, and a connection reads the data of that workspace only.

[Data schema](data-access/data-schema/) lists the tables and columns of each layer.

## How IoT Query Premium differs from Reports and Dashboards

[Reports](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/reports) and [Dashboards](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/dashboards) in Location Intelligence use the same kind of data and need no SQL, but you can't change what they calculate. In IoT Query Premium, you decide what to calculate and how to show the result.

## Before you start

IoT Query Premium is included in a paid plan in Navixy Console. After your organization has the plan, your organization's administrator receives the connection details.

## Where to start

Start with [Getting started](getting-started.md). It takes you from getting IoT Query Premium to your connection details and your first query.

## Section contents

* [Getting started](getting-started.md): Get IoT Query Premium, receive your connection details, and run your first query.
* [Private Telematics Lakehouse](data-access/): Learn where the data comes from, whose data you can see, and how often it is updated.
  * [Connection setup](data-access/connection-setup.md): Find the connection details and run a first query.
  * [Data schema](data-access/data-schema/): Find the tables and columns of the raw data layer and the transformation layer.
  * [Use third-party BI tools](data-access/third-party-bi-tools/): Choose a BI tool and connect Power BI, Apache Superset, Grafana, or Streamlit.
* [Dashboard Studio](dashboard-studio/): Build dashboards and reports from your own SQL queries, or with an AI assistant.
  * [Create dashboard](dashboard-studio/create-dashboard.md): Create a dashboard, add panels, write their queries, and arrange the layout.
    * [Build dashboard with AI](dashboard-studio/build-dashboard-with-ai.md): Describe what you want to monitor and let the assistant build the dashboard for you.
  * [Write SQL queries](dashboard-studio/write-sql-queries.md): Write queries that return the result that each panel type needs, with parameters and time periods.
  * [Create report](dashboard-studio/create-report.md): Create a report from one query, with parameters that readers choose.
  * [Open-source Dashboard Studio](dashboard-studio/open-source-studio.md): Run your own copy of Dashboard Studio from its open-source code.
