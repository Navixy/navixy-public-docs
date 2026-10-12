---
description: >-
  The IoT Logic node types in Navixy GraphQL API: the fields that each type
  takes in a write, what it does to a message, and the rules that apply to it.
tags:
  - reference
  - beta
---

# IoT Logic node types

This page describes each node type of a flow: the fields that you send in a write, what the node does with a message, and the rules that Navixy checks. It is for developers who build flows through the API. For how the nodes work together, see [How a message moves through a flow](how-flows-work.md#how-a-message-moves-through-a-flow). For the full field tables, see the [Flows](api-reference/flows.md) reference.

## Node types at a glance

A flow node has a type. The `type` field of a node in a write request takes the value in the first column. A read returns the object type in the second column, and each object type has the fields of its node.

| `type` in a write | Object type in a read | Node in the flow editor | What the node does |
| --- | --- | --- | --- |
| `DATA_SOURCE` | `IotLogicFlowDataSourceNode` | Data Source | Receives the messages of the devices that you list in `deviceIds` |
| `INITIATE_ATTRIBUTES` | `IotLogicFlowInitiateAttributesNode` | Initiate Attribute | Calculates the attributes in `attributes` and adds them to the message |
| `LOGIC` | `IotLogicFlowLogicNode` | IF/THEN Logic | Checks `condition` and sends the message to the `THEN` or the `ELSE` transition |
| `OUTPUT_ENDPOINT` | `IotLogicFlowOutputEndpointNode` | Output Endpoint | Ends a path and sends the message, with the attributes that the flow calculated, to the Navixy platform |

## Fields that every node has

Every node in the `nodes` list of a write has these fields, whatever its type:

| Field | Required | What it holds |
| --- | --- | --- |
| `key` | Yes | A name for the node, valid only inside this one request. Transitions refer to the node by this key. Keys must be unique in the request. |
| `id` | Only for a stored node | The ID of the stored node that this entry keeps. Leave it out for a new node, and Navixy creates the ID. |
| `type` | Yes | The node type, such as `DATA_SOURCE` or `LOGIC`. See [Node types at a glance](#node-types-at-a-glance). |
| `name` | Yes | The name that the flow editor shows. English letters, digits, underscores, dashes, and slashes, in words separated by spaces. No space at the start or end. |
| `description` | No | Notes about the node. Printable ASCII and line feeds (`\n`), at most 4,000 characters. A carriage return (`\r`) fails with `NON_LATIN_INPUT`. |
| `position` | No | The `x` and `y` coordinates where the flow editor draws the node. |

A node uses only the fields of its own type. Navixy ignores the fields of other types without an error, so don't send them. For example, a `condition` on a `DATA_SOURCE` node has no effect.

## Transition types

A transition connects two nodes by their keys. Its `type` says which outcome of the source node it follows:

| Type | Use it for |
| --- | --- |
| `SIMPLE` | Every transition that leaves a node other than a logic node |
| `THEN` | The path that a message takes when the condition of a logic node is true |
| `ELSE` | The path that a message takes in every other case |

Navixy checks the type of every transition when you save a flow. A `SIMPLE` transition that leaves a logic node, or a `THEN` or `ELSE` transition that leaves any other node, fails the write with `EDGE_TYPE_MISMATCH`.

## The smallest valid flow

A valid flow needs one data source, one Output Endpoint, and a transition between them. The following `flowCreate` variables create such a flow for one device. It keeps every message of the device as it is, and it is a starting point to add nodes to:

```json
{
  "input": {
    "title": "Minimal flow",
    "nodes": [
      {
        "key": "src",
        "type": "DATA_SOURCE",
        "name": "My devices",
        "deviceIds": ["019dbec6-d380-8000-801f-44455643e216"]
      },
      { "key": "out", "type": "OUTPUT_ENDPOINT", "name": "Default output" }
    ],
    "transitions": [{ "from": "src", "to": "out", "type": "SIMPLE" }]
  }
}
```

The response has the new flow with `version` 1 and `isEnabled: false`. For the mutation document and the full response, see [Create the flow](guides/creating-and-updating-flows.md#create-the-flow).

## Data Source

**`type`: `DATA_SOURCE`. Object type in a read: `IotLogicFlowDataSourceNode`.**

A data source is where the messages of your devices enter the flow. Each message of a device in `deviceIds` starts at this node and follows its outgoing transitions.

| Field | In a write | In a read |
| --- | --- | --- |
| `deviceIds` | The IDs of the devices whose messages enter here. The devices must belong to your workspace. | Not returned. Read `devices` instead. |
| `devices` | Not accepted | The `Device` records of the attached devices that your roles let you read, ordered by device ID, without pagination |

Rules:

* A flow needs at least one data source, and nothing can lead into one. A write that breaks these rules fails with `MISSING_DATA_SOURCE` or `DATA_SOURCE_HAS_INCOMING_EDGE`.
* A flow can have several data sources, for example one for each group of devices. The data sources of one flow can have at most 25,000 devices together. A device in two data sources counts twice.
* In an update, leave out `deviceIds` to keep the current devices of a stored data source. Send `deviceIds: []` to remove all of them. Any other list replaces the stored devices.
* `deviceIds` changes only the devices that your roles let you read. `[]` and a new list leave the other devices of the data source attached, and validation still counts them toward the device limit and toward attribute conflicts.
* A new data source without `deviceIds` starts with no devices.
* A device can't enter two data sources whose paths lead to the same node, because that node would process each message twice. A write with such devices fails with `OVERLAPPING_ENTRY_PATHS`.
* Navixy checks only the devices that a write adds. A device that doesn't exist, that belongs to another workspace, or that your roles don't let you read fails the write with `UNKNOWN_DEVICE`.

## Initiate Attribute

**`type`: `INITIATE_ATTRIBUTES`. Object type in a read: `IotLogicFlowInitiateAttributesNode`.**

An Initiate Attribute node calculates new attributes and adds them to the message. Every node after it on the path can read those attributes by name.

| Field | What it holds |
| --- | --- |
| `attributes` | A list of assignments. Each assignment has a `name` for the new attribute and an `expression` that calculates its value. |

Rules:

* A node can have at most 50 attributes. A flow can produce at most 200 attributes, counting the `expressionName` of every logic node.
* An attribute name uses lowercase letters and digits in groups joined by single underscores, such as `fuel_level_l`. A name has at most 48 characters, not counting the channel number. Navixy reserves these names for the position of a device: `position_latitude`, `position_longitude`, `position_altitude`, `position_speed`, `position_heading`, `position_accuracy`, `position_hdop`, `position_vdop`, `position_pdop`, `position_satellites`, `position_fix_type`, and `position_source`.
* An expression is printable ASCII on one line, at most 960 characters. It is written in the Navixy IoT Logic Expression Language.
* A message can pass only one node that produces a given attribute. Two nodes on one path can't produce the same attribute, and neither can two parallel paths that leave one node. The `THEN` and `ELSE` paths of one logic node can each produce it, because a message takes only one of them. Another flow can't produce the same attribute for the same device either, and a flow can't produce an attribute that its devices already report.

An expression can read the earlier values of an attribute with `value()`, for example `value('fuel_level_l', 1, 'valid')` for the previous valid value. Navixy stores the last 12 values of each attribute of a device.

## IF/THEN Logic

**`type`: `LOGIC`. Object type in a read: `IotLogicFlowLogicNode`.**

A logic node checks a condition and sends the message down one of two paths. A message for which the condition is true leaves by the `THEN` transition. Every other message leaves by `ELSE`.

| Field | Required | What it holds |
| --- | --- | --- |
| `condition` | Yes | The expression to check. Printable ASCII on one line, at most 480 characters. |
| `expressionName` | No | An attribute that stores the result of the check, such as `is_too_warm`. It follows the naming rule of attributes. Without it, the node only chooses the path. |

Rules:

* A logic node doesn't need an `ELSE` transition. Without one, a message for which the condition isn't true goes no further after this node.
* One outcome can have several transitions, and the message follows all of them. Only one of these paths keeps its attributes, so put the nodes of one outcome one after another. See [How a message moves through a flow](how-flows-work.md#how-a-message-moves-through-a-flow).
* `THEN` and `ELSE` can lead into the same node, because a message never takes both.
* A missing value doesn't always make the condition false. `temperature > 8` is false for a message without `temperature`, but `speed != 60` is true for a message without `speed`.

## Output Endpoint

**`type`: `OUTPUT_ENDPOINT`. Object type in a read: `IotLogicFlowOutputEndpointNode`.**

An Output Endpoint node ends a path and sends the message to the Navixy platform. The message has the values that the device sent and the attributes that the path calculated. The platform stores the calculated attributes in the history of the device, together with the values that the device sent.

Rules:

* A path that calculates attributes needs to end at an Output Endpoint node. On a path that ends at any other node, the calculated attributes are lost.
* A flow can have several Output Endpoint nodes, for example one after the `THEN` outcome and one after the `ELSE` outcome. When two paths are active for the same message, only one of their Output Endpoint nodes keeps its attributes. See [How a message moves through a flow](how-flows-work.md#how-a-message-moves-through-a-flow).

The node has no fields other than the fields that every node has.

## See also

* [How IoT Logic flows work](how-flows-work.md): Learn how a message moves through a flow and where calculated attributes appear.
* [Creating and updating flows](guides/creating-and-updating-flows.md): Build a flow from these node types, step by step.
* [Flow validation errors](flow-validation-errors.md): Fix the violations that the rules on this page produce.
* [Flows](api-reference/flows.md): See every field of every node type.
