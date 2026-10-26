---
description: >-
  Why an IoT Logic flow write fails validation, how the violations list names
  every problem at once, and how to fix each violation code.
tags:
  - reference
  - beta
---

# Flow validation errors

Navixy validates the whole flow when you create it, when you enable it, and when an update changes anything other than `isEnabled`. A write that breaks a rule changes nothing and fails with one `VALIDATION_ERROR`. The error lists every violation that Navixy found, so you can fix all of them before you send the write again.

This page is for developers who build or edit flows through the API. It explains the structure of the error, the rules that Navixy checks, and what to do for each violation code. For how to write a flow, see [Creating and updating flows](guides/creating-and-updating-flows.md).

## When Navixy validates a flow

Navixy validates the graph at three moments:

* `flowCreate` validates the new flow.
* `flowUpdate` validates the flow after the change. An update that only sets `isEnabled: true` validates the stored graph again.
* An update that only sets `isEnabled: false` validates nothing, and so does `flowDelete`. You can always stop and remove a flow, even one that no longer passes validation.

Enabling a flow can fail even if the flow saved without errors. Some rules compare the flow with your other flows and with the attributes that your devices report, and those can change after the last save.

### Order of the checks

Navixy checks a create or update request in this order, and stops at the first check that fails:

1. The request itself. A malformed input fails with a single `VALIDATION_ERROR`. See [Errors outside the violations list](#errors-outside-the-violations-list).
2. Your roles. Without the `create`, `update`, or `delete` action on IoT Logic flows, the mutation fails with `PERMISSION_DENIED`.
3. The flow. A graph that breaks a rule fails with a `VALIDATION_ERROR` that lists every violation.

So a request with a malformed input returns `VALIDATION_ERROR` even when your roles don't allow the mutation. `flowCreate`, `flowUpdate`, and `flowDelete` can't return `null`, so a `PERMISSION_DENIED` error sets the whole `data` to `null`. See [What your roles and products allow](../authentication.md#what-your-roles-and-products-allow).

## Structure of a flow validation error

A flow validation error is a standard [validation error](../error-handling.md#validation-error-400) with one extra field, `extensions.violations`. The following error is the response to an update that has a cycle, a node that no data source reaches, and a condition with a Cyrillic letter in it:

```json
{
  "errors": [
    {
      "message": "Flow is invalid: 4 problems",
      "path": ["iotLogic", "flowUpdate"],
      "extensions": {
        "type": "https://api.navixy.com/errors/validation",
        "title": "Validation Error",
        "status": 400,
        "detail": "Flow is invalid: 4 problems",
        "instance": "/graphql",
        "code": "VALIDATION_ERROR",
        "traceId": "1d6d70b64b4d593b4d7d63f4b5ac4bd6",
        "timestamp": "2026-09-24T10:23:06Z",
        "violations": [
          {
            "constraint": "NON_LATIN_INPUT",
            "message": "Field 'nodes[2].condition' must contain printable ASCII only; found U+0435 at index 10",
            "nodeIds": ["01a0d2f0-485d-8002-801f-494c4e44c534"],
            "fields": ["input.nodes[2].condition"],
            "relatedFlowIds": []
          },
          {
            "constraint": "UNREACHABLE_NODE",
            "message": "Nodes are reachable from no data source: Notify dispatcher",
            "nodeIds": ["01a0d2f0-b7c8-8000-801f-494c4e4404a1"],
            "fields": ["input.nodes"],
            "relatedFlowIds": []
          },
          {
            "constraint": "CYCLE_DETECTED",
            "message": "The graph contains a cycle through: 01a0d2f0-485d-8001-801f-494c4e44d810",
            "nodeIds": ["01a0d2f0-485d-8001-801f-494c4e44d810"],
            "fields": ["input.transitions"],
            "relatedFlowIds": []
          },
          {
            "constraint": "INVALID_EXPRESSION",
            "message": "Logic node 'Too warm' has an invalid condition: 1:11 tokenization error in ''",
            "nodeIds": ["01a0d2f0-485d-8002-801f-494c4e44c534"],
            "fields": ["input.nodes[2].condition"],
            "relatedFlowIds": []
          }
        ],
        "classification": "BAD_REQUEST"
      }
    }
  ],
  "data": null
}
```

The HTTP status of this response is 200, like every business logic error of Navixy GraphQL API. The status 400 is in `extensions.status`. See [Error handling](../error-handling.md#http-status-codes).

Each entry of `violations` has these fields:

| Field | What it holds |
| --- | --- |
| `constraint` | The code of the rule that the write breaks. The [violation codes](#violation-codes) below list every code. |
| `message` | A description of the problem, with the names or the IDs that it concerns. The text is for people, so don't parse it in code. |
| `nodeIds` | The IDs of the nodes that the problem concerns. Empty when the problem concerns the whole flow. |
| `fields` | The input paths to fix, such as `input.nodes[2].condition`. The number is the position of the node in the `nodes` list of your request, counted from 0. |
| `relatedFlowIds` | The IDs of the flows that already produce the attribute. When two data sources of this flow clash, the list has the ID of this flow. Only `ATTRIBUTE_ALREADY_PRODUCED` fills this list. |

A path into `transitions` is counted the same way: `input.transitions[3]` is the fourth entry of your `transitions` list.

### How to handle flow validation errors

Match each violation to your request by `fields`, and use `constraint` to decide what to change. The [violation codes](#violation-codes) below give the fix for each code. After you fix every violation, send the write again. Nothing was saved, so the `version` that you sent is still current.

To show the problems in your app, use `fields` to find the node in your request. Use `nodeIds` only for nodes that the flow already had. A node that the request adds gets a new ID during validation, and Navixy doesn't store that ID when the write fails. In the example above, the unreachable node was added by the request, so its ID in `nodeIds` doesn't exist anywhere else.

Fixing every violation in the list doesn't always make the next write succeed. Navixy compares your flow with your other flows and with the attributes that your devices report only after every other rule passes. So `ATTRIBUTE_ALREADY_PRODUCED` and `ATTRIBUTE_REPORTED_BY_DEVICE` can appear on the next attempt. If a flow has more than 64 nodes or more than 96 transitions, Navixy also skips the graph, expression, and attribute name checks until the flow is within the limits. Of the Device Action checks, only `ACTIONS_PER_NODE_EXCEEDED` and `DEVICE_MAPPINGS_PER_NODE_EXCEEDED` run.

## Errors outside the violations list

Some mistakes stop a write before Navixy validates the graph. They return a single `VALIDATION_ERROR` without `violations`. Most of them name the input in `field`:

| Mistake | Input named in the error |
| --- | --- |
| An attribute name that doesn't follow the [naming rule](#names-and-text), such as `TempF` | `input.nodes[0].attributes[0].name` |
| A logic node without `condition` | `input.nodes[0].condition` |
| A blank node key, or two nodes with the same key in one request | `input.nodes[0].key`, or `input.nodes` |
| A transition that names a key that no node in the request has | `input.transitions[0].from` or `input.transitions[0].to` |
| A node name that doesn't follow the [naming rule](#names-and-text) | None. The message quotes the name. |
| A `trackableNodeId` that doesn't follow the [naming rule](#names-and-text) | `input.nodes[0].trackableNodeId` |
| `deviceIds: null` on a data source, or `deviceMapping: null` on a Device Action node | `input.nodes[0].deviceIds` or `input.nodes[0].deviceMapping` |
| One source device in two entries of the `deviceMapping` of one node | `input.nodes[0].deviceMapping` |
| `title: null`, `nodes: null`, or `isEnabled: null` in `flowUpdate`. Leave the field out to keep its stored value. | `input.title`, `input.nodes`, or `input.isEnabled` |
| `transitions` without `nodes` in `flowUpdate`. Send both lists together. | `input.transitions` |

The following error is the response to an Initiate Attribute node with the attribute name `TempF`:

```json
{
  "errors": [
    {
      "message": "Cannot parse AttributeKey 'TempF': AttributeKey name must be lower_snake_case ASCII ([a-z0-9] segments joined by single underscores), got 'TempF'",
      "path": ["iotLogic", "flowUpdate"],
      "extensions": {
        "type": "https://api.navixy.com/errors/validation",
        "title": "Validation Error",
        "status": 400,
        "detail": "Cannot parse AttributeKey 'TempF': AttributeKey name must be lower_snake_case ASCII ([a-z0-9] segments joined by single underscores), got 'TempF'",
        "instance": "/graphql",
        "code": "VALIDATION_ERROR",
        "field": "input.nodes[0].attributes[0].name",
        "traceId": "0af7651916cd43dd8448eb211c80319c",
        "timestamp": "2026-09-24T10:25:41Z"
      }
    }
  ]
}
```

### How to handle malformed flow input

Fix the input at the path in `field` and send the write again. The error names only the first problem that Navixy found, so the next attempt can return another one, or a list of violations.

## Violation codes

The codes are grouped by the part of the flow that they check.

### Text

| Code | Cause | How to fix |
| --- | --- | --- |
| `NON_LATIN_INPUT` | A title, a description, a condition, or an attribute expression has a character outside printable ASCII (U+0020 to U+007E). The message names the character and its position, because Cyrillic and Latin letters can look the same. | Replace the character. Descriptions can also have line feeds (`\n`), but not carriage returns (`\r`). No field accepts a tab, except the flow title: Navixy removes tabs and line breaks from it and replaces several spaces with one. |
| `DESCRIPTION_TOO_LONG` | The description of the flow or of a node has more than 4,000 characters. | Shorten the description. |
| `EXPRESSION_TOO_LONG` | A condition has more than 480 characters, or an attribute expression has more than 960 characters. | Shorten the expression, or split the calculation into several attributes. |

### Size of the flow

| Code | Cause | How to fix |
| --- | --- | --- |
| `NODES_PER_FLOW_EXCEEDED` | The flow has more than 64 nodes. | Split the flow into several flows. |
| `EDGES_PER_FLOW_EXCEEDED` | The flow has more than 96 transitions. | Split the flow into several flows. |
| `ATTRIBUTES_PER_NODE_EXCEEDED` | An Initiate Attribute node has more than 50 attributes. | Move some attributes to another Initiate Attribute node. |
| `ATTRIBUTES_PER_FLOW_EXCEEDED` | The flow produces more than 200 attributes. Every attribute of an Initiate Attribute node counts, and so does every `expressionName` of a logic node. | Remove attributes, or move some of them to another flow. |
| `DEVICES_PER_FLOW_EXCEEDED` | The data sources of the flow have more than 25,000 devices together. A device in two data sources counts twice. | Move some devices to another flow. |

### Graph structure

| Code | Cause | How to fix |
| --- | --- | --- |
| `MISSING_DATA_SOURCE` | The flow has no data source node. | Add a `DATA_SOURCE` node. |
| `NO_EDGES` | The flow has nodes and no transitions. | Connect the nodes with transitions. |
| `DATA_SOURCE_HAS_INCOMING_EDGE` | A transition leads into a data source node. A data source is where messages enter the flow, so nothing can lead into it. | Remove the transition, or point it at another node. |
| `UNREACHABLE_NODE` | No path from any data source leads to the node. The message names the node. | Add a transition to the node, or remove it. |
| `CYCLE_DETECTED` | A path of transitions returns to a node that it already passed. | Remove one transition of the loop. |
| `SIMULTANEOUS_PATHS` | Two paths that are active for the same message lead to one node. Paths in a flow never join, so the result of one path would be lost. | Make the paths exclusive, with the `THEN` and `ELSE` transitions of one logic node, or give each path its own nodes. |
| `DUPLICATE_NODE_ID` | Two nodes of the request have the same `id`. | Send each stored node once. Give a new node a key and no `id`. |
| `UNKNOWN_EDGE_ENDPOINT` | A transition connects a node that the flow doesn't have. | Send the transitions together with the nodes that they connect. |
| `EDGE_TYPE_MISMATCH` | A transition that leaves a logic node is `SIMPLE`, or a transition that leaves any other node is `THEN` or `ELSE`. The message lists each wrong transition by its position in the list and its type. | Use `THEN` or `ELSE` for a transition that leaves a logic node, and `SIMPLE` for every other transition. |

### Expressions and attributes

| Code | Cause | How to fix |
| --- | --- | --- |
| `INVALID_EXPRESSION` | A condition, or an attribute expression of at most 480 characters, isn't a valid formula. The message names the logic node, or the attribute for an attribute expression, with the position and the reason. A longer attribute expression isn't checked when you save it, and if it has an error, the attribute gets no value. | Correct the formula. |
| `DUPLICATE_ATTRIBUTE_IN_FLOW` | A message from one data source would pass two nodes that produce the same attribute, or one node produces it twice. The `THEN` and `ELSE` paths of one logic node can each produce it, because a message takes only one of them. | Rename one of the attributes, or remove it. |
| `RESERVED_ATTRIBUTE_NAME` | The flow produces an attribute with one of the names that Navixy reserves for the position of a device, such as `position_latitude`. See [Initiate Attribute](node-types.md#initiate-attribute) for the full list. | Rename the attribute. |
| `ATTRIBUTE_ALREADY_PRODUCED` | Another flow already produces the attribute for a device of this flow, or another data source of this flow does. `relatedFlowIds` lists the flows that produce it. | Rename the attribute, or remove the device from one of the flows. |
| `ATTRIBUTE_REPORTED_BY_DEVICE` | A device of the flow reports an attribute with the same name as a calculated attribute or as the `trackableNodeId` of a Device Action node. The value from the flow would replace the value from the device. | Rename the attribute or the `trackableNodeId`. |

### Devices

| Code | Cause | How to fix |
| --- | --- | --- |
| `UNKNOWN_DEVICE` | A device that the write adds to a data source or to the `deviceMapping` of a Device Action node doesn't exist, belongs to another workspace, or isn't one that your roles let you read. The message lists the device IDs. | Remove the device ID, or correct it. Navixy checks only the devices that the write adds, so a device that the flow already had never fails the write. |
| `OVERLAPPING_ENTRY_PATHS` | One device is in two data sources whose paths reach the same node. That node would process each message of the device twice. | Keep the device in one of the data sources, or separate the paths. |

### Device Action nodes

| Code | Cause | How to fix |
| --- | --- | --- |
| `ACTIONS_PER_NODE_EXCEEDED` | A Device Action node has more than 10 actions. `fields` names `input.nodes[0].actions`. | Remove actions, or move some of them to another Device Action node. |
| `INVALID_DEVICE_COMMAND` | An action isn't valid: a `setOutput` output number is outside 1 to 8, or a `rawCommand` text is blank or longer than 512 characters. `fields` names the action, such as `input.nodes[0].actions[1]`. | Correct the output number, or the command text. |
| `DUPLICATE_TRACKABLE_NODE_ID` | Two Device Action nodes of the flow have the same `trackableNodeId`. `nodeIds` has both nodes. | Give each Device Action node its own `trackableNodeId`. |
| `DEVICE_MAPPINGS_PER_NODE_EXCEEDED` | The `deviceMapping` of a Device Action node has more than 1,000 targets, counted across its entries. | Remove targets, or split the mapping between several Device Action nodes. |
| `EMPTY_DEVICE_MAPPING_ENTRY` | An entry of the `deviceMapping` of a Device Action node has no targets. The message lists the source devices of these entries. | Add at least one target to the entry, or remove the entry. A source device without an entry receives its own commands. |

## Names and text

These rules apply to the names and text of a flow. Text with a character outside printable ASCII fails with `NON_LATIN_INPUT`. An attribute name that breaks its rule fails with a single `VALIDATION_ERROR`.

| Field | Rule |
| --- | --- |
| Flow `title` | Printable ASCII, at most 255 characters, and not blank |
| `description` of a flow or a node | Printable ASCII and line feeds (`\n`), at most 4,000 characters |
| Node `name` | English letters, digits, underscores, dashes, and slashes, in words separated by spaces. No space at the start or end |
| Attribute `name` and `expressionName` | Lowercase letters and digits in groups joined by single underscores, such as `temperature_f`, at most 48 characters. The name can end with `_` and a channel number, such as `temperature_2`, which doesn't count toward the limit |
| `condition` of a logic node | Printable ASCII on one line, at most 480 characters |
| `expression` of an attribute | Printable ASCII on one line, at most 960 characters |
| `trackableNodeId` of a Device Action node | The rule of attribute names |
| `text` of a `rawCommand` action | At most 512 characters, not blank. Navixy doesn't check its characters, but sends the text as ASCII, so use printable ASCII |

## Best practices

* Validate in your app before you send a write, with the limits and names on this page. A local check gives the user feedback without a request.
* Show every violation at once. The API returns all of them in one response, so the user can fix the whole flow in one pass.
* Keep the `nodes` order of your request until the response arrives. The paths in `fields` refer to positions in that list.

## See also

* [Creating and updating flows](guides/creating-and-updating-flows.md): Build a flow, replace its graph, and enable it.
* [Error handling](../error-handling.md): See the structure of every error and the codes that all products share.
* [Flows](api-reference/flows.md): Look up the limits and rules on each input field.
