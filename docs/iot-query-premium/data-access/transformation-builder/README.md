---
hidden: true
noIndex: true
description: >-
  Transformation Builder turns the queries you repeat into tables of your own
  that are updated on a schedule.
tags:
  - overview
  - preview
---

# Transformation Builder

Transformation Builder lets you describe a query once, as a workflow, and writes its result to a table of your own on a schedule. Use it when a question needs a rule of your own, such as your own speed limit or minimum trip length. The tables that Navixy calculates don't apply such rules.

This page explains what a workflow is, which nodes it is built from, and where its result is stored. It is step 3 of the developer path through [Private Telematics Lakehouse](../). This step is optional: skip it if the tables of the [report database](../data-schema/transformation-layer/report.md) give the results that you need. It is for developers and analysts who write SQL. Before you start, connect to IoT Query Premium as described in [Connection setup](../connection-setup.md), and learn the tables in [Data schema](../data-schema/).

## What you can do with Transformation Builder

**Apply your own rules.** Detect trips with your own minimum distance, or mark the positions above a speed limit that your fleet sets.

**Prepare tables in the shape that your reports need.** A table of hourly or daily totals per device is small and fast to read from Dashboard Studio or from a BI tool. The table can already contain the names joined from business data.

**Keep the result up to date.** Transformation Builder runs the workflow on the schedule that you set and writes each result to your table. You don't need a scheduler or a data pipeline of your own.

## How a workflow works

A workflow is a set of nodes connected by edges. Each node is one processing step, such as reading a table, filtering rows, or joining two inputs. An edge connects two nodes: the result of the first node is the input of the second. Most nodes have one input. **SQL Transform** joins two inputs, and a **Custom** node can read several inputs. The result of one node can be the input of several nodes.

A workflow starts with source nodes, continues with transformation nodes, and ends with one **Output** node. The edges can't form a cycle, so no node can receive its own result, directly or through other nodes. Transformation Builder checks the workflow for errors. It returns an error for a cycle, for a node without its input, or for a missing required setting, such as the join condition of SQL Transform.

Transformation Builder turns the whole workflow into one SQL query. Each node becomes one common table expression (CTE): a named subquery in the `WITH` clause of the query. The name of each CTE is the ID of its node, so the SQL of a later node can read the result of an earlier node by that ID.

Conditions, expressions, and your own SQL in a workflow use ClickHouse SQL, like every other query in IoT Query Premium. The rules in [Data schema](../data-schema/#rules-for-every-query) apply to them too.

### Source nodes

A source node reads one table:

| Source | Type in the workflow file | What it reads |
| --- | --- | --- |
| Telematics | `telematics` | One table of the [telemetry database](../data-schema/raw-data-layer/telemetry.md), such as `telemetry.records`. You can limit the rows to the last minutes or hours and add a condition |
| Business data | `business` | One table of the [core_bdr database](../data-schema/raw-data-layer/core-bdr.md), such as `core_bdr.device` |

To start a workflow from a table of the `report` database, or from any other table, use a **Custom** node without inputs. The SQL of the **Custom** node can read any table if you add the database name before the table name, for example `report.trips_stops`.

### Transformation nodes

A transformation node changes the result of the nodes connected to it:

| Node | Type in the workflow file | What it does |
| --- | --- | --- |
| **Filter** | `filter` | Keeps the rows that are within a time range and match your conditions |
| **Resampling** | `resample` | Groups rows into periods of a fixed length, such as 5 minutes or 1 day, and calculates the average, sum, lowest value, highest value, first value, last value, or count of each column |
| **SQL Transform** | `sql` | Joins two inputs on a condition that you set |
| **Arithmetic** | `arithmetic` | Adds a column that an expression calculates, or replaces a column with the result of an expression |
| **Custom** | `custom` | Runs a `SELECT` query that you write. It can read the results of earlier nodes and any table |

### Output node

The **Output** node names the table that receives the result of the workflow. It also sets how each run writes to the table:

* `append`: Each run adds its rows to the table.
* `replace`: Each run deletes the rows of the table and writes its own rows.
* `upsert`: Each run updates the rows that have the same primary key as a new row, and adds the other rows.

## Where the result is stored

Transformation Builder creates the output table at the first scheduled run, with the columns of the result. It doesn't delete the table when you change the workflow or stop its schedule. It doesn't delete rows either, except in `replace` mode, where each run replaces the contents of the table.

Query an output table like any other table of IoT Query Premium, from Dashboard Studio or from your BI tool. Your output tables belong to the [transformation layer](../data-schema/transformation-layer/), beside the tables that Navixy calculates.

A workflow has two ways to run:

* To start a preview, click **Preview**. A preview runs the query of the workflow and shows up to 100 rows of the result, with the SQL that ran. It doesn't write anything. Use it to check the result while you build the workflow.
* A scheduled run takes place at the times that you set with a cron expression, such as `0 6 * * *` for every day at 06:00 UTC. It writes the result to the table named in the **Output** node.

Only one run of a workflow takes place at a time. If a run starts while the previous run is still in progress, Transformation Builder skips the new run. Each run has a time limit, 5 minutes by default, and a run that takes longer fails. Transformation Builder records the status of each run: success, failed, or skipped.

## Your own tables and the report tables

Navixy calculates a set of tables for every workspace in the `report` database: trips and stops, geo object visits, sensor readings, and device status. They are ready without any setup. Start from the `report` tables and build a workflow only when the `report` tables don't give the result that you need. For example, filter `report.trips_stops` in a query to count long stops, but build a workflow from `telemetry` to detect trips with a different rule.

A workflow can also start from a `report` table and add data to its result. For example, a workflow can join `report.trips_stops` with the names of assets and write a daily summary per asset. [Transformation layer](../data-schema/transformation-layer/#when-to-use-the-transformation-layer) compares the two kinds of tables.

## Workflow files and templates

You can export a workflow as a YAML file and import the file into Transformation Builder again. Keep the file in version control, share it with colleagues, or write a workflow in a text editor. Navixy also provides templates: ready-made workflow files for common transformations, such as trips with your own detection rules.

## Section contents

* [Workflow YAML reference](workflow-yaml-reference.md): Look up every key of the workflow file, with its type, default value, and an example.
* [Templates](templates.md): Start from a ready-made workflow for trips or sensor values, and find the Output node settings for each one.

## Next steps

* [Use third-party BI tools](../third-party-bi-tools/): Connect a BI tool to IoT Query Premium and read your tables from it.
