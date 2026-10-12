---
description: >-
  Query raw and calculated fleet data with SQL from your own tools: trips,
  stops, geo object visits, sensor readings, assets, and custom fields.
tags:
  - overview
  - fundamentals
  - preview
noIndex: true
---

# Private Telematics Lakehouse

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

Private Telematics Lakehouse (PTL) gives technical teams direct SQL access to the telematics and business data of their workspace. It is the data part of IoT Query Premium. Reports and Dashboards in Location Intelligence show fixed views of this kind of data. PTL gives you the data itself, to query with your own tools, combine with other data, or build an application on.

## What you can do with PTL

**Query the data with your own tools.** When your company already has a data warehouse or a business intelligence (BI) tool, connect it to PTL, or connect a SQL client or your own application, and read the tables directly. Dashboard Studio, BI tools, and your own applications read the same tables, so each of them calculates its results from the same data. See [Connection setup](connection-setup.md) and [Use third-party BI tools](third-party-bi-tools/).

**Use telematics and business data together.** For example, you can group trips by a custom field that your company keeps on each asset. The positions and sensor values of your GPS devices are in the same place as your assets, geo objects, and custom fields. One query can join them. [Data schema](data-schema/#how-tables-join) shows how the tables join.

**Start from tables that are ready for analysis.** Instead of building trips from positions yourself, start from the calculated tables. Navixy calculates tables of trips, stops, geo object visits, and sensor readings from the raw data, so you don't prepare these values yourself. The [report database](data-schema/transformation-layer/report.md) describes these tables.

## Who it is for

PTL is for the technical teams that use the data outside the platform's screens:

* Data engineers load the data into their data warehouses and combine it with other data, without collecting it through an API.
* Developers build applications and integrations on the tables.
* BI specialists connect the BI tools that their company already uses.
* Solution architects plan how the data fits into the company's other systems.

Analysts who write queries in Dashboard Studio read the same tables, so the facts on this page apply to them too.

## Core concepts

* **Lakehouse:** PTL is a lakehouse in this sense: it keeps the raw data as it arrives and the tables calculated from that data, and you query both with SQL. PTL stores the data in ClickHouse, a database built for analytical queries over large tables.
* **Raw data layer:** The `core_bdr` and `telemetry` databases. They hold business data and telemetry as they arrive, and nothing in them is calculated from other rows.
* **Transformation layer:** The `report` database. It holds tables that Navixy calculates from the raw data: trips and stops, geo object visits, sensor readings, and device status.

Query the transformation layer for common questions, such as how far an asset drove. Query the raw data layer when you need every data point or a value that no calculated table has. [Data schema](data-schema/) describes both layers table by table.

## Where the data comes from

PTL combines two sources:

* **Business data** comes from [Business Data Repository](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/bdr): your workspace and its users, devices, assets, asset groups, geo objects, schedules, catalogs, and custom fields and their values. Navixy copies this data into PTL automatically.
* **Telemetry** comes from your GPS devices: positions, speed, ignition, sensor values, and the state of digital inputs and outputs. It arrives as the devices send it.

A query can combine both sources. For example, one query can read the trips of an asset from telemetry-based tables and the name of the asset from business data.

## Whose data you can see

PTL is private to your workspace. Each workspace of your organization has its own IoT Query Premium database and its own connection details. A query returns only the data of the workspace that the details belong to. To combine the data of several workspaces, connect to each workspace and combine the results in your own tool.

## How often the data is updated

Each kind of data is updated in its own way:

* **Business data** is copied automatically. Changes that you make in Business Data Repository are copied to PTL without any action from you. For example, a new asset or a renamed geo object appears in PTL.
* **Telemetry** is written as the GPS devices send it.
* **Transformation layer tables** of trips, stops, geo object visits, and sensor readings are recalculated on a schedule. Each recalculation covers a recent period again, for trips the last 12 hours, so a trip or a visit that is still in progress can change until it ends. The device status tables are written as the messages arrive.

For a query that must include the latest positions, read `telemetry` or `report.device_status`. For a query about completed trips or visits, read the transformation layer.

## Where to start

You need IoT Query Premium for your workspace and its connection details: [Getting started](../getting-started.md) explains how to get them. Then start with [Connection setup](connection-setup.md). It gives you the connection details and a first query. Then read [Data schema](data-schema/) and [Use third-party BI tools](third-party-bi-tools/), in that order.

## Section contents

* [Connection setup](connection-setup.md): Find your connection details and run a first query.
* [Data schema](data-schema/): Learn which tables each layer contains and how they join.
* [Use third-party BI tools](third-party-bi-tools/): Choose a BI tool and connect it to PTL.
