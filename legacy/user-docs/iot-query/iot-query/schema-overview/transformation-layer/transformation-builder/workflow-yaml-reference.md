---
description: The workflow YAML format captures Transformation Builder node graphs, parameters, and edges for export, import, and sharing between environments
---

# Workflow YAML reference

This page documents the YAML format that Transformation Builder uses to save and load\
workflow configurations. The YAML is the Builder's internal representation of a workflow\
design: it captures the node graph, parameters, and edges so a workflow can be exported,\
stored, shared, or re-imported into the Builder.

When you schedule a workflow, the Builder compiles the node graph into a single SQL query\
and registers it as a scheduled database function. That SQL query and its pg\_cron job are\
what execute on your database. The YAML is not involved at runtime. If you want to build\
or run transformations independently of the Builder, write and schedule the SQL directly\
on your database without using this format.

The format has two versions. **Version 2** is the current format that Transformation Builder produces on export. It organizes nodes as a flat array in topological order (`cte_nodes`) with a separate `edges` array for connections. **Version 1** is an older format that uses a `nodes` key with inline `depends_on` references for connections. Transformation Builder can import both versions, but always exports version 2.

{% columns %}
{% column %}
#### **Export**&#x20;

Produces a YAML file in version 2 format. You can trigger an export from the **Export** button in the Transformation Builder toolbar, or through the API. The exported file can be stored in a repository, shared with colleagues, or re-imported\
into Transformation Builder in another environment.
{% endcolumn %}

{% column %}
#### **Import**&#x20;

Accepts both version 2 (the current format) and version 1 (an earlier format with `depends_on`-based connections). Use the **Import** function in the Builder and select a `.yaml` or `.yml` file.
{% endcolumn %}
{% endcolumns %}

## How the export is formed

The export generator builds the YAML file through four steps:

1. **Topological sort of nodes.** The graph of nodes and edges is sorted so that source nodes come first, followed by transformation nodes in dependency order, and finally the Output node. If the graph contains a cycle, a fallback order (by node list position) is used.
2. **Source list per node.** For each node, the generator collects an ordered list of predecessor node IDs from the edges where `target` equals the current node's ID. For example, an SQL Transform node with two inputs will have a `sources` list containing exactly two IDs in the correct order.
3. **cte\_nodes array.** Each node is written as a record in the `cte_nodes` array, in topological order. See [cte\_nodes structure](workflow-yaml-reference.md#cte_nodes-structure) below for field details.
4. **Top-level YAML assembly.** The generator combines the `cte_nodes` array with the edges list, workflow metadata, and optional fields (viewport, schedule) into the final document.

### Top-level keys

The root of a version 2 YAML file contains the following keys:

| Key              | Presence                   | Description                                                                                                             |
| ---------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `version`        | Always                     | Format version number. Always `2` for current exports.                                                                  |
| `name`           | Always                     | Workflow name.                                                                                                          |
| `description`    | Always                     | Workflow description. May be an empty string.                                                                           |
| `cte_nodes`      | Always                     | Array of node records in topological order. See [cte\_nodes structure](workflow-yaml-reference.md#cte_nodes-structure). |
| `edges`          | Always                     | Array of edge objects. See [edges structure](workflow-yaml-reference.md#edges-structure).                               |
| `output_node_id` | When an Output node exists | The ID of the Output node in the workflow.                                                                              |
| `viewport`       | When set in the workflow   | Canvas position and zoom level: `{ x, y, zoom }`. Used to restore the visual layout on import.                          |
| `schedule`       | When schedule is enabled   | Execution schedule: `{ cron, timezone }`. Default cron is `0 0 * * *`, default timezone is `UTC`.                       |

### cte\_nodes structure

Each entry in the `cte_nodes` array represents one node in the workflow graph.

| Field             | Description                                                                                                                                                         |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`              | Unique node identifier (string).                                                                                                                                    |
| `type`            | Node type. One of: `telematics`, `business`, `filter`, `resample`, `sql`, `arithmetic`, `custom`, `output`.                                                         |
| `label`           | Display name shown on the canvas. Falls back to the node ID if not set.                                                                                             |
| `description`     | Node description. May be an empty string.                                                                                                                           |
| `position`        | Canvas coordinates as `{ x, y }`.                                                                                                                                   |
| `sources`         | Ordered list of predecessor node IDs, derived from the graph edges. Empty for source nodes.                                                                         |
| `params`          | Node configuration parameters. The specific fields depend on the node type. See the [Transformation Builder](./) documentation for parameter details per node type. |
| `width`, `height` | Optional. Canvas dimensions for the node, included only when explicitly set.                                                                                        |

**Params cleaning.** The `available_tables` and `available_columns` fields are removed from `params` during export. These fields are populated at runtime when the Builder connects to the database and should not be stored in YAML.

**SQL type with multiple sources.** When a node of type `sql` has two or more sources, the export adds a `join_spec` field to the record. This is an array with one element containing the join configuration:

```yaml
join_spec:
  - type: "left"       # join type (lowercase): left, inner, right, full
    on_condition: "a_node_telematics_1.device_id = a_node_business_1.device_id"
```

The `type` value is taken from the node's `join_type` parameter (converted to lowercase), and `on_condition` is taken from `join_condition`. For SQL nodes with two sources, the join information appears in both `params` and `join_spec`.

### edges structure

The `edges` array defines connections between nodes in the workflow graph.

```yaml
edges:
  - source: "node-telematics-1"
    target: "node-sql-1"
  - source: "node-business-1"
    target: "node-sql-1"
```

Each edge is an object with two fields:

| Field    | Description                                   |
| -------- | --------------------------------------------- |
| `source` | The ID of the node where the edge originates. |
| `target` | The ID of the node where the edge terminates. |

Edge IDs from the Builder interface are not preserved in the export. On import, new edge IDs are generated automatically.

## Import

### Version detection

The Builder determines the YAML format version using the following logic:

* If the root contains `version: 2` or the key `cte_nodes`, the file is processed as **version 2**.
* Otherwise, **version 1** is expected.

### Version 2 import

The Builder iterates the `cte_nodes` array in order. For each record:

* The `id` and `type` are read. The type is converted to lowercase. Nodes with type `python` are skipped without raising an error.
* Parameters are read from the `params` key (or `config` as a fallback). For `sql`-type nodes, if a `join_spec` field is present in the record, it is assigned to the node's join configuration.
* The `edges` array is parsed into source-target pairs, and new edge IDs are generated.
* The `viewport` and `schedule` fields are preserved if present in the YAML.

### Version 1 import (backward compatibility)

Version 1 files use a `nodes` key (array or dictionary) and optionally an `edges` array or `depends_on` fields within each node.

The Builder processes version 1 files as follows:

* Supported node types are the same as version 2: `telematics`, `business`, `filter`, `resample`, `sql`, `arithmetic`, `custom`, `output`.
* Connections between nodes can be defined in two ways: a top-level `edges` array, or a `depends_on` list within each node.
* The `inputs` and `outputs` fields on each node are normalized to objects with `{ name, type }` structure.
* If edges include `sourceHandle` or `targetHandle` port identifiers, the import adjusts node ports accordingly for correct display in the Builder interface.

## YAML structure template

The following template shows the complete structure of a version 2 YAML file with annotations:

{% code expandable="true" %}
```yaml
version: 2
name: "workflow_name"
description: "Workflow description"

cte_nodes:
  - id: "node-id-1"
    type: telematics    # telematics | business | filter | resample |
                        # sql | arithmetic | custom | output
    label: "Node display name"
    description: ""
    position: { x: 100, y: 100 }
    sources: []         # empty for source nodes
    params:
      table_name: "tracking_data_core"
      time_column: "device_time"
      columns: [device_id, device_time, speed]
      filter_condition: ""
      time_window_minutes: ""

  - id: "node-id-2"
    type: sql
    label: "SQL Transform"
    description: ""
    position: { x: 400, y: 160 }
    sources: ["node-id-1", "node-id-3"]   # ordered predecessor IDs
    params:
      join_type: LEFT
      join_condition: "a_node_id_1.device_id = a_node_id_3.device_id"
      select_columns: ["a_node_id_1.*", "a_node_id_3.sensor_label"]
    join_spec:                             # added for sql nodes with 2+ sources
      - type: "left"
        on_condition: "a_node_id_1.device_id = a_node_id_3.device_id"

    # optional per-node fields:
    # width: 250
    # height: 400

edges:
  - source: "node-id-1"
    target: "node-id-2"
  - source: "node-id-3"
    target: "node-id-2"

# optional top-level fields:
output_node_id: "node-output-1"           # if an Output node exists
viewport: { x: 0, y: 0, zoom: 1 }        # canvas position on import
schedule: { cron: "0 0 * * *", timezone: "UTC" }  # if scheduling is enabled
```
{% endcode %}

### Example

The following example shows a complete version 2 workflow that reads telematics sensor data, joins it with sensor descriptions from the business schema, applies an arithmetic transformation to convert a column type, and writes the results to an output table.

{% code expandable="true" %}
```yaml
version: 2
name: enriched_vehicle_metrics
description: "Enriched vehicle metrics from telematics and sensor description"

cte_nodes:
  - id: node-telematics-1
    type: telematics
    label: "Raw data: Telematics"
    description: ""
    position: { x: 100, y: 100 }
    sources: []
    params:
      table_name: inputs
      time_column: device_time
      columns: [device_id, device_time, value]
      filter_condition: ""
      time_window_minutes: ""

  - id: node-business-1
    type: business
    label: "Raw data: Business"
    description: ""
    position: { x: 100, y: 220 }
    sources: []
    params:
      table_name: sensor_description
      key_column: sensor_id
      columns: [sensor_id, device_id, sensor_label]

  - id: node-sql-1
    type: sql
    label: "SQL Transform"
    description: ""
    position: { x: 400, y: 160 }
    sources: [node-telematics-1, node-business-1]
    params:
      join_type: LEFT
      join_condition: "a_node_telematics_1.device_id = a_node_business_1.device_id"
      select_columns: ["a_node_telematics_1.*", "a_node_business_1.sensor_label"]
    join_spec:
      - type: "left"
        on_condition: "a_node_telematics_1.device_id = a_node_business_1.device_id"

  - id: node-arithmetic-1
    type: arithmetic
    label: "Arithmetic"
    description: ""
    position: { x: 700, y: 160 }
    sources: [node-sql-1]
    params:
      expressions:
        - column: value
          expression: "value::numeric"
          expression_alias: value_num

  - id: node-output-1
    type: output
    label: "Output"
    description: ""
    position: { x: 1000, y: 160 }
    sources: [node-arithmetic-1]
    params:
      table_name: enriched_vehicle_metrics
      time_column: device_time
      primary_key: [device_id, device_time]
      write_mode: append

edges:
  - { source: node-telematics-1, target: node-sql-1 }
  - { source: node-business-1, target: node-sql-1 }
  - { source: node-sql-1, target: node-arithmetic-1 }
  - { source: node-arithmetic-1, target: node-output-1 }

output_node_id: node-output-1
viewport: { x: 0, y: 0, zoom: 1 }
schedule: { cron: "0 0 * * *", timezone: "UTC" }
```
{% endcode %}

This workflow performs the following steps:

1. **node-telematics-1** reads `device_id`, `device_time`, and `value` columns from the `inputs` table in the `raw_telematics_data` schema.
2. **node-business-1** reads `sensor_id`, `device_id`, and `sensor_label` from the `sensor_description` table in the `raw_business_data` schema.
3. **node-sql-1** joins the two sources on `device_id` using a `LEFT JOIN`, selecting all telematics columns plus the `sensor_label` from the business source.
4. **node-arithmetic-1** adds a computed column `value_num` by casting the text `value` column to a numeric type.
5. **node-output-1** configures the result to be written to the `enriched_vehicle_metrics` table with `append` mode, using `device_id` and `device_time` as the primary key.

{% hint style="info" %}
The export does not include `available_tables` or `available_columns` in `params`. These fields are populated dynamically when the Builder connects to the database. For SQL nodes with two sources, join information appears in both `params` and `join_spec`.
{% endhint %}

## Next steps

* [**Transformation Builder**](./): Learn how to design workflows using the visual interface.
* [**Templates**](templates.md): Pre-built workflow configurations you can import and adapt in Transformation Builder.
* [**Transformation layer**](../): Understand how processed data is organized into schemas and how to query it.
