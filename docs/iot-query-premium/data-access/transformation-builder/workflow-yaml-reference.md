---
hidden: true
noIndex: true
description: >-
  Every key of the Transformation Builder workflow file, with its type,
  default value, and an example.
tags:
  - reference
  - preview
---

# Workflow YAML reference

A workflow file is a YAML file that describes one Transformation Builder workflow: its nodes, the edges between them, and its schedule. This page lists every key of the file, with its type, its default value, and an example, and ends with a complete workflow. It is for developers who keep workflows in version control, share them, or write them by hand. Read [Transformation Builder](./) first to learn what each node does.

## How the file is used

**Export** in the Transformation Builder toolbar saves the current workflow as a file in version 2 of the format. **Import** reads a file in version 2 or in version 1, an older format. Transformation Builder doesn't run the file itself: it turns the workflow into one SQL query and runs that query.

An import creates a workflow from the file. If the file has the `workflow_id` of a workflow that already exists, the import replaces that workflow with the contents of the file. Remove `workflow_id` from a file to import it as a new workflow.

Import reads the nodes from `cte_nodes` and the connections between them from `edges`. The `sources` list of each node and the top-level `output_node_id` are there for people who read the file. Import doesn't use them.

## File structure

The following file shows the top-level keys and the keys of each node, in a workflow of two nodes:

```yaml
version: 2
workflow_id: 7d1e2f30-4a5b-4c6d-8e9f-0a1b2c3d4e5f
name: speeding_by_device_hour
description: Messages above 90 km/h per device and hour
cte_nodes:
  - id: positions
    type: telematics
    label: Positions
    description: ""
    position: { x: 100, y: 100 }
    sources: []
    params:
      table_name: records
      columns: [device_id, device_time, speed]
  - id: result
    type: output
    label: Output
    description: ""
    position: { x: 400, y: 100 }
    sources: [positions]
    params:
      table_name: speeding_by_device_hour
      write_mode: append
edges:
  - { source: positions, target: result }
output_node_id: result
viewport: { x: 0, y: 0, zoom: 1 }
schedule:
  cron: "15 * * * *"
  timezone: UTC
```

## Top-level keys

| Key | Type | Default | What it contains |
| --- | --- | --- | --- |
| `version` | Integer | Required | The version of the format. Export writes `2`. Import reads a file as version 2 when it has `version: 2` or `cte_nodes`, and as version 1 otherwise |
| `workflow_id` | String, a UUID | A new ID | The ID of the workflow, for example `7d1e2f30-4a5b-4c6d-8e9f-0a1b2c3d4e5f`. Import replaces the workflow that has this ID, or creates a workflow with this ID if none exists |
| `name` | String | `Imported Workflow` | The name of the workflow, for example `speeding_by_device_hour` |
| `description` | String | `""` | What the workflow does, for example `Messages above 90 km/h per device and hour` |
| `owner` | String | `User` | The person responsible for the workflow, for example `analyst`. Import reads it, and export doesn't write it |
| `cte_nodes` | List of objects | Required | The nodes of the workflow. See [Node keys](#node-keys) |
| `edges` | List of objects | `[]` | The connections between nodes. See [Edge keys](#edge-keys) |
| `output_node_id` | String | Not set | The ID of the node whose result the workflow writes, for example `result`. Export writes the last Output node, or the last node if there is no Output node. Import ignores it |
| `viewport` | Object | `{ x: 0, y: 0, zoom: 1 }` | The position and zoom of the canvas, so that an import shows the workflow as it was exported |
| `schedule` | Object | No schedule | When the workflow runs. See [Schedule keys](#schedule-keys) |

## Node keys

Each entry of `cte_nodes` is one node:

| Key | Type | Default | What it contains |
| --- | --- | --- | --- |
| `id` | String | `node-<n>`, where `<n>` is the position of the node in the list, starting at 1 | The ID of the node, unique in the workflow, for example `speeding_hourly`. It is also the name of the CTE of the node |
| `type` | String | `sql` | The node type: `telematics`, `business`, `filter`, `resample`, `sql`, `arithmetic`, `custom`, or `output`. Import reads the type in lower case, so `Filter` and `filter` are the same |
| `label` | String | The name of the node type, such as `Filter` | The name shown on the canvas, for example `Hourly speeding` |
| `description` | String | `""` | What the node does |
| `position` | Object | `{ x: 100 + 300 × (<n> - 1), y: 100 }` | The place of the node on the canvas, for example `{ x: 400, y: 100 }` |
| `sources` | List of strings | `[]` | The IDs of the input nodes, in order, for example `[speeding_hourly, devices]`. Export writes them from `edges`. Import ignores them |
| `params` | Object | `{}` | The settings of the node. They depend on the node type. See [Node settings by type](#node-settings-by-type). Import also accepts the older name `config` |
| `join_spec` | List of one object | Not set | The join of an `sql` node with two inputs: `type`, the join type in lower case, and `on_condition`, the join condition. Export writes it from `join_type` and `join_condition`, which remain in `params` |
| `width`, `height` | Numbers | Not set | The size of the node on the canvas, if you resized the node |
| `color` | String | The color of the node type | The color of the node on the canvas, for example `"#f97316"`. Import reads it, and export doesn't write it |

Use only letters, digits, and underscores in a node `id`. Transformation Builder replaces every other character with `_` in the CTE name and adds `n_` before an ID that starts with a digit. The IDs `speeding-hourly` and `speeding_hourly`, for example, both become the CTE `speeding_hourly`, and the two CTEs then have the same name.

Import skips a node of type `python` and returns a warning. It also returns a warning for a node type that it doesn't know, and imports the node anyway.

Export leaves out two settings, `available_tables` and `available_columns`. Transformation Builder fills them from the database when it connects, to show the lists in the node settings.

## Node settings by type

The keys of `params` depend on the node type. A condition or an expression is ClickHouse SQL. Transformation Builder adds it to the query exactly as you write it.

### telematics

A `telematics` node reads one table of the [telemetry database](../data-schema/raw-data-layer/telemetry.md).

| Key | Type | Default | What it contains |
| --- | --- | --- | --- |
| `table_name` | String | Required | The table, without the database name, for example `records` |
| `columns` | List of strings | `["*"]` | The columns to read, for example `[device_id, device_time, speed]`. `"*"` reads every column |
| `time_column` | String | `device_time` | The time column that `time_window_minutes` applies to |
| `time_window_minutes` | Integer, or `""` | `""` | Reads only the rows whose time is within this number of minutes before the run, for example `180`. `""` reads every row |
| `filter_condition` | String | `""` | A condition that each row must match, for example `speed > 90` |

```yaml
params:
  table_name: records
  columns: [device_id, device_time, speed]
  time_column: device_time
  time_window_minutes: 180
  filter_condition: speed > 90
```

### business

A `business` node reads one table of the [core_bdr database](../data-schema/raw-data-layer/core-bdr.md).

| Key | Type | Default | What it contains |
| --- | --- | --- | --- |
| `table_name` | String | Required | The table, without the database name, for example `device` |
| `columns` | List of strings | `["*"]` | The columns to read, for example `[id, title]` |
| `key_column` | String | Not set | The ID column of the table, for example `id`. The node settings show it, and it doesn't change the query |

A `business` node reads every row of the table. `core_bdr` tables keep older versions of a row and the rows of deleted items, as described in [Data schema](../data-schema/#read-the-current-version-of-each-row). To read only the current rows of items that exist, use a `custom` node with `FINAL` and `deleted_at IS NULL`. The `devices` node of the [Complete example](#complete-example) is such a node.

### filter

A `filter` node keeps the rows of its input that match all its conditions.

| Key | Type | Default | What it contains |
| --- | --- | --- | --- |
| `time_column` | String | `device_time` | The time column that the time range applies to |
| `time_range_start` | String | `""` | Keeps the rows at or after this time, in UTC, for example `2026-09-01 00:00:00`. `""` sets no start |
| `time_range_end` | String | `""` | Keeps the rows at or before this time, in UTC. `""` sets no end |
| `dynamic_conditions` | List of strings | `[]` | Conditions that each row must match, for example `["speed > 5", "hw_mileage_m IS NOT NULL"]` |
| `filter_condition` | String | `""` | One more condition. It is the older form of `dynamic_conditions` |

```yaml
params:
  time_column: device_time
  time_range_start: "2026-09-01 00:00:00"
  time_range_end: ""
  dynamic_conditions:
    - speed > 5
```

### resample

A `resample` node groups the rows of its input into periods of a fixed length and aggregates each group.

| Key | Type | Default | What it contains |
| --- | --- | --- | --- |
| `time_column` | String | The first column of the input | The time column that sets the period of each row |
| `interval` | String | No periods | The length of a period: `1min`, `5min`, `15min`, `1hour`, or `1day`. `none` groups by the `group_by` columns only |
| `group_by` | List of strings | `[]` | The columns to group by besides the period, for example `[device_id]`. `["*"]` groups by every column of the input |
| `aggregations` | List of objects | `[]` | The columns to aggregate. Each object has `column` and `method` |
| `aggregations[].method` | String | `avg` | `avg`, `sum`, `min`, `max`, `first`, `last`, or `count` |

The result has the column `time_bucket`, the start of each period in UTC, then the `group_by` columns, then one column per aggregation. The name of an aggregation column is the column name and the method, joined by `_`: `speed` with `max` becomes `speed_max`. Columns of the input that aren't grouped or aggregated aren't in the result.

```yaml
params:
  time_column: device_time
  interval: 1hour
  group_by: [device_id]
  aggregations:
    - { column: speed, method: max }
    - { column: speed, method: count }
```

### sql

An `sql` node joins two inputs. The first edge that ends at the node sets its left input, and the second edge sets its right input.

| Key | Type | Default | What it contains |
| --- | --- | --- | --- |
| `join_type` | String | `LEFT` | `INNER`, `LEFT`, `RIGHT`, or `FULL`, the full outer join. `FULL` can be set in the file only |
| `join_condition` | String | Required | The condition that matches rows of the two inputs, for example `speeding_hourly.device_id = devices.device_id` |
| `select_columns` | List of strings | `["*"]` | The columns of the result, for example `[speeding_hourly.time_bucket, devices.device]`. `"*"` returns every column of both inputs |
| `join_spec` | List of one object | Not set | The join as `type` and `on_condition`. Transformation Builder uses `on_condition` when `join_condition` is empty |

In `join_condition` and `select_columns`, write each column as `<input node ID>.<column>`. Write `<input node ID>.*` for every column of one input. Transformation Builder uses a column without a node ID exactly as it is written.

An `sql` node with one input returns the `select_columns` of that input, without a join.

```yaml
params:
  join_type: LEFT
  join_condition: speeding_hourly.device_id = devices.device_id
  select_columns:
    - speeding_hourly.time_bucket
    - speeding_hourly.device_id
    - devices.device
```

### arithmetic

An `arithmetic` node adds columns calculated from expressions, or replaces columns with the results of expressions.

| Key | Type | Default | What it contains |
| --- | --- | --- | --- |
| `expressions` | List of objects | Required | The expressions. Each object has `column`, `expression`, and `expression_alias` |
| `expressions[].column` | String | Not set | The input column that the expression starts from, for example `speed`. The node settings show it, and validation checks that the input has this column |
| `expressions[].expression` | String | Required | The expression, for example `speed / 1.60934`. Transformation Builder adds the name of the input before each input column in the expression |
| `expressions[].expression_alias` | String | Required | The name of the result column, for example `speed_mph`. An alias that is the name of an input column replaces that column |

The result has the columns of the input that no alias replaces, then the columns of the expressions. Import also reads the older form with one expression: `column`, `expression`, and `expression_alias` directly in `params`.

```yaml
params:
  expressions:
    - column: speed
      expression: speed / 1.60934
      expression_alias: speed_mph
```

### custom

A `custom` node runs a `SELECT` query that you write.

| Key | Type | Default | What it contains |
| --- | --- | --- | --- |
| `custom_sql` | String | `""` | The query. With no query, the node returns the rows of its first input |
| Any other key | Text or number | Not set | A value for a placeholder in `custom_sql`. See the placeholder rules after the table |

Read the result of an input node by its ID, as a table: `FROM speeding_hourly`. To read any other table, add the database name before the table name: `FROM report.trips_stops`. A `custom` node can have no inputs, one input, or several inputs.

The query becomes the body of one CTE, so it can't start with `WITH`. Use a subquery instead.

A placeholder is a key name in double braces, such as `{{p_days}}`. Before the query runs, Transformation Builder replaces the placeholder with the value of that key in `params`, as text and without quotes. A placeholder without a matching key stays in the query unchanged.

```yaml
params:
  p_days: "7"
  custom_sql: |-
    SELECT asset_id, count() AS long_stops
    FROM report.trips_stops
    WHERE kind = 'stop'
      AND duration_s >= 3600
      AND start_time >= now() - INTERVAL {{p_days}} DAY
    GROUP BY asset_id
```

The node returns one row per asset:

| asset_id | long_stops |
| --- | --- |
| 1c7a0e52-3b9d-4f18-a6e2-7d40c95b1f83 | 4 |

### output

An `output` node writes the result of its input to a table of your own.

| Key | Type | Default | What it contains |
| --- | --- | --- | --- |
| `table_name` | String | Required | The name of the output table, for example `speeding_by_device_hour`. Transformation Builder creates the table at the first scheduled run |
| `write_mode` | String | `upsert` | How each run writes: `append` adds the rows, `replace` deletes the rows of the table and writes the new ones, and `upsert` updates the rows with the same `primary_key` and adds the others. A new Output node starts with `append` |
| `primary_key` | List of strings | `[]` | The columns that identify a row, for example `[device_id, time_bucket]`. `upsert` needs them |
| `time_column` | String | Not set | The time column of the result, for example `time_bucket` |
| `partition_by` | String | Not set | An expression that divides the table into parts, for example `toDate(time_bucket)` |

## Edge keys

Each entry of `edges` connects two nodes:

| Key | Type | Default | What it contains |
| --- | --- | --- | --- |
| `source` | String | Required | The ID of the node whose result is the input, for example `positions` |
| `target` | String | Required | The ID of the node that receives the input, for example `speeding_hourly` |
| `id` | String | `e<n>`, where `<n>` is the position of the edge in the list | The ID of the edge. Export doesn't write it |

An edge without `source` or `target` is left out on import. The order of the edges that end at one `sql` node sets its left and right inputs.

## Schedule keys

`schedule` sets when the workflow runs. Without it, the workflow runs only when you preview it, and nothing is written.

| Key | Type | Default | What it contains |
| --- | --- | --- | --- |
| `cron` | String | Required | A cron expression of five fields: minute, hour, day of month, month, day of week. `15 * * * *` runs at 15 minutes past every hour |
| `timezone` | String | `UTC` | The time zone of the cron expression, as an IANA name such as `Europe/Madrid` |
| `enabled` | Boolean | `true` | `false` keeps the schedule in the workflow and stops the runs |
| `overlap_policy` | String | `skip` | What happens when a run starts while the previous run is still in progress. `skip` is the only value: the new run is skipped |
| `timeout_minutes` | Integer | `5` | The longest time that a run can take, in minutes. A run that takes longer stops and fails |

Export writes `cron` and `timezone` only, and only when the schedule is enabled. To set values other than the defaults for `enabled`, `overlap_policy`, or `timeout_minutes`, add these keys to the file before you import it.

```yaml
schedule:
  cron: "15 * * * *"
  timezone: UTC
  enabled: true
  overlap_policy: skip
  timeout_minutes: 10
```

## Version 1 files

Version 1 is an older format that Transformation Builder still imports. Export always writes version 2, so write new files in version 2.

A version 1 file has these differences from version 2:

* The nodes are in `nodes`, as a list or as an object with the node IDs as keys, instead of `cte_nodes`.
* A node names its inputs in `depends_on`, a list of node IDs. Import uses `depends_on` when the file has no `edges`. If a file has neither `edges` nor `depends_on`, Import connects the nodes in the order of the list.
* A node can have `name` instead of `label`, `node_type` instead of `type`, and `inputs` and `outputs`, the names of its ports.
* An edge can have `sourceHandle` and `targetHandle`, the ports that it connects.

## Complete example

For each device and hour, the following workflow counts the messages with a speed above 90 km/h and finds the highest speed. It adds the name of each device from business data and writes the result to the table `speeding_by_device_hour` at 15 minutes past every hour:

{% code expandable="true" %}
```yaml
version: 2
name: speeding_by_device_hour
description: Messages above 90 km/h per device and hour, with device names
cte_nodes:
  - id: positions
    type: telematics
    label: Positions above 90 km/h
    description: ""
    position: { x: 100, y: 100 }
    sources: []
    params:
      table_name: records
      columns: [device_id, device_time, speed]
      time_column: device_time
      time_window_minutes: 180
      filter_condition: speed > 90

  - id: speeding_hourly
    type: resample
    label: Hourly speeding
    description: ""
    position: { x: 400, y: 100 }
    sources: [positions]
    params:
      time_column: device_time
      interval: 1hour
      group_by: [device_id]
      aggregations:
        - { column: speed, method: max }
        - { column: speed, method: count }

  - id: devices
    type: custom
    label: Device names
    description: Current devices from core_bdr
    position: { x: 400, y: 300 }
    sources: []
    params:
      custom_sql: |-
        SELECT id AS device_id, title AS device
        FROM core_bdr.device FINAL
        WHERE deleted_at IS NULL

  - id: named
    type: sql
    label: Add device names
    description: ""
    position: { x: 700, y: 200 }
    sources: [speeding_hourly, devices]
    params:
      join_type: LEFT
      join_condition: speeding_hourly.device_id = devices.device_id
      select_columns:
        - speeding_hourly.time_bucket
        - speeding_hourly.device_id
        - devices.device
        - speeding_hourly.speed_max
        - speeding_hourly.speed_count
    join_spec:
      - type: left
        on_condition: speeding_hourly.device_id = devices.device_id

  - id: result
    type: output
    label: Output
    description: ""
    position: { x: 1000, y: 200 }
    sources: [named]
    params:
      table_name: speeding_by_device_hour
      write_mode: upsert
      primary_key: [device_id, time_bucket]
      time_column: time_bucket
      partition_by: toDate(time_bucket)

edges:
  - { source: positions, target: speeding_hourly }
  - { source: speeding_hourly, target: named }
  - { source: devices, target: named }
  - { source: named, target: result }

output_node_id: result
viewport: { x: 0, y: 0, zoom: 1 }
schedule:
  cron: "15 * * * *"
  timezone: UTC
  enabled: true
  overlap_policy: skip
  timeout_minutes: 10
```
{% endcode %}

The workflow has these steps:

1. From `telemetry.records`, `positions` reads the device, time, and speed of every message in the last 3 hours that has a speed above 90 km/h.
2. `speeding_hourly` groups the messages by device and hour, and calculates the highest speed, `speed_max`, and the number of messages, `speed_count`.
3. `devices` reads the ID and name of each device that exists, from `core_bdr.device`. `telemetry` and `core_bdr` store device IDs with the same type, `UUID`, so you can join the IDs without conversion.
4. `named` joins the name of each device to its hourly rows. The edge from `speeding_hourly` comes first, so `speeding_hourly` is the left input.
5. `result` writes the rows to `speeding_by_device_hour`. Each run reads the last 3 hours again, and `upsert` updates the hours that already have a row. The hour in progress is updated at the next run.

The output table has one row per device and hour with speeding:

| time_bucket | device_id | device | speed_max | speed_count |
| --- | --- | --- | --- | --- |
| 2026-09-27 14:00:00.000 | 5f0c2b8e-1d3a-4c7e-9a41-2e6b7d8f9a10 | FMB920 truck 12 | 104.5 | 37 |
| 2026-09-27 15:00:00.000 | 5f0c2b8e-1d3a-4c7e-9a41-2e6b7d8f9a10 | FMB920 truck 12 | 96.2 | 8 |
| 2026-09-27 15:00:00.000 | 9b7e1c4d-6a2f-4e8b-b3c5-0d1f2a3b4c5d | FMB920 van 3 | 93.8 | 5 |

`time_bucket` is in UTC. To group the table by local hours, convert it with `toTimeZone`, as described in [Data schema](../data-schema/#convert-times-to-your-time-zone).

## See also

* [Transformation Builder](./): Learn what a workflow is, what each node does, and where the result is stored.
* [Templates](templates.md): Start from a ready-made workflow file for trips or sensor values.
* [Data schema](../data-schema/): Find the tables that source nodes and custom SQL can read.
