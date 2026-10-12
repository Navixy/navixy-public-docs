---
description: >-
  Get IoT Query Premium with your Console plan, receive your connection
  details, and run a first query to check that they work.
tags:
  - guide
  - preview
noIndex: true
---


# Getting started

{% hint style="info" %}
IoT Query Premium is coming soon. The steps on this page apply once it is available for your organization. To learn when that is, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

This guide shows how to get access to IoT Query Premium and run a first query on the data of your workspace. At the end, you have your connection details, you know that they work, and you know which part of this documentation to read next. This guide is for everyone who starts with IoT Query Premium. Its readers are administrators who arrange access, developers who connect their own tools, and analysts who build dashboards in Dashboard Studio.


Before you start, you need one of the following:

* Administrator access to your organization in [Navixy Console](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/).
* The contact details of your telematics service provider, if another company manages Navixy Console for you.

## 1. Get IoT Query Premium

IoT Query Premium is included in a paid plan of Navixy Console. Your organization gets it when its plan includes IoT Query Premium.

* If your organization manages its own plan in Navixy Console, choose a plan that includes IoT Query Premium. See [Getting started with Navixy Console](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/getting-started).
* If a telematics service provider manages Navixy for you, ask them to add IoT Query Premium to the plan of your organization.

## 2. Receive your connection details

After IoT Query Premium is added, the administrator of your organization receives the connection details. Each workspace of your organization has its own set:

* The host of your IoT Query Premium database
* The port of the HTTP interface, which most clients and BI tools use
* The native port, which `clickhouse-client` and some drivers use
* The user name
* The password

Share the details only with the people and the tools that need them. Anyone who has the password can read the data of the workspace. [Connection setup](data-access/connection-setup.md) describes each detail and the clients that can use it.

## 3. Run your first query

Run one query to confirm that the details work. IoT Query Premium is a ClickHouse database, so any ClickHouse client can run the query, for example `clickhouse-client` or DBeaver. If you work in Dashboard Studio, run the query in its SQL editor instead, as described in [Write SQL queries](dashboard-studio/write-sql-queries.md#id-1.-explore-the-data-in-the-sql-editor).

To count the assets of your workspace, connect with your details and run the following query:

```sql
SELECT count() AS assets
FROM core_bdr.asset FINAL
WHERE deleted_at IS NULL
```

`FINAL` makes the query count each asset once, and `deleted_at IS NULL` leaves out deleted assets. The result has one row with the number of your assets:

| assets |
| --- |
| 42 |

A number greater than 0 means that your connection works and that your business data is in IoT Query Premium. Navixy copies your business data from Business Data Repository into IoT Query Premium. A result of 0 without an error means that the connection works and that the first copy of your business data isn't finished. Run the query again later. If the query returns an error, see [Connection setup](data-access/connection-setup.md#connection-errors).

## 4. Choose your path


This documentation has one path for each way of working with the data:

* **Build dashboards in Dashboard Studio.** Dashboard Studio is the Navixy tool for dashboards and reports that use IoT Query Premium data. You write SQL queries for its panels or ask its assistant to build a dashboard for you. Start with [Dashboard Studio](dashboard-studio/).
* **Use your own tools.** Connect a BI tool, a data warehouse, or your own application to IoT Query Premium. Start with [Private Telematics Lakehouse](data-access/) and follow its pages in order: connect, learn the data schema, and connect a BI tool.

Both paths use the same tables. The [Data schema](data-access/data-schema/) page describes them for either path.

{% hint style="success" %}
You have IoT Query Premium, working connection details, and a first result from your data.
{% endhint %}

## Next steps

* [Dashboard Studio](dashboard-studio/): Build dashboards and reports from your own SQL queries, or with an AI assistant.
* [Private Telematics Lakehouse](data-access/): Learn where the data comes from and how it is updated, then connect your own tools.
* [Data schema](data-access/data-schema/): Find the tables and columns of the raw data layer and the transformation layer.
