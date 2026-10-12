---
description: >-
  Use IoT Query Premium data in third-party BI tools: which tools work, how
  Power BI, Apache Superset, Grafana, and Streamlit compare, and what you need
  before you connect one.
tags:
  - guide
  - preview
---

# Use third-party BI tools

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

IoT Query Premium is a ClickHouse database, so any BI tool with a ClickHouse connector can read it. This page compares four tools that connect directly: Power BI, Apache Superset, Grafana, and Streamlit. It lists what you need before you connect a tool and links to a guide for each tool. It is step 3 of the developer path through [Private Telematics Lakehouse](../). It is for developers and data engineers who set up a BI tool for their team. Before you start, test your connection as described in [Connection setup](../connection-setup.md).

## Compare the BI tools

Choose the tool by who uses the result and where it runs. Every tool in the table reads the same databases and tables.

| Tool | Who builds with it | Where it runs | Good for | How it connects |
| --- | --- | --- | --- | --- |
| Power BI | Analysts who work in Microsoft 365 | Power BI Desktop on Windows, and Power BI Service for sharing | Business reports that combine fleet data with other company data | The ClickHouse connector, with the ClickHouse ODBC driver |
| Apache Superset | Data teams that run their own servers | A Superset server that your team installs and maintains | Shared dashboards and SQL exploration for many users | The `clickhouse-connect` Python driver |
| Grafana | Operations and engineering teams | Grafana Cloud, or a Grafana server that your team runs | Time series of positions and sensor values, and alerts on them | The ClickHouse data source plugin for Grafana |
| Streamlit | Python developers | A Python app that you write and host | Custom apps, calculations in Python, and results that no BI tool shows | The `clickhouse-connect` Python driver |

## Choose a tool for your use case

Choose the tool that matches your job:

* **Reports for managers who already use Microsoft 365.** Choose Power BI. Reports are shared through Power BI Service, and your Microsoft accounts control who can open them.
* **One dashboard server for many teams.** Choose Apache Superset. It has a SQL editor, charts, and roles, and your team controls the server.
* **Monitoring of sensor values over time.** Choose Grafana. Its panels show the time range that the viewer selects and update when the viewer changes it. Grafana can also send an alert when a value crosses a limit.
* **An app with its own logic.** Choose Streamlit. The app is a Python script, so you can combine a query result with any Python library before you show it.

You can also build dashboards without a BI tool, in [Dashboard Studio](../../dashboard-studio/). Other tools that support ClickHouse connect in the same way as Power BI, Apache Superset, Grafana, and Streamlit. They use the connection details and a ClickHouse driver or connector.

## Prepare before you connect

Before you follow a guide in this section, prepare these items:

* **Connection details.** The host, port, user name, and password of IoT Query Premium. See [Connection setup](../connection-setup.md#connection-details).
* **A tested connection.** Run the test query in [Connection setup](../connection-setup.md#test-the-connection) from the computer or server where the BI tool runs. A BI tool reports connection errors with less detail than `curl` or `clickhouse-client`, so test the connection first.
* **TLS enabled.** Connections to IoT Query Premium are encrypted with TLS, and a connection without TLS fails. Each guide shows where to enable TLS in the tool.
* **The tables that you need.** Most dashboards read a `report` table and join a `core_bdr` table to get names. See [Data schema](../data-schema/) to learn which tables each database has, and read the [rules for every query](../data-schema/#rules-for-every-query).

Store the password in the credential store of the tool, not in a query, a dashboard definition, or code. Anyone who has the password can read the data of the workspace.

## Connect a BI tool

* [Connect Power BI](connect-power-bi.md): Install the ClickHouse ODBC driver, connect Power BI Desktop, and chart the distance driven per day.
* [Connect Apache Superset](connect-apache-superset.md): Add IoT Query Premium as a database and chart the distance of each asset.
* [Connect Grafana](connect-grafana.md): Install the ClickHouse plugin, add a data source, and show a sensor value over time.
* [Connect Streamlit](connect-streamlit.md): Write a Python app that reads IoT Query Premium and shows the distance of each asset per day.
