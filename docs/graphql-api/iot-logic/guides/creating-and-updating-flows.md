---
description: >-
  Create an IoT Logic flow through Navixy GraphQL API, read it back, replace
  its graph, enable it, and delete it.
tags:
  - guide
  - beta
---

# Creating and updating flows

A flow is a graph of nodes that processes the messages of the devices attached to it. This guide builds a flow for refrigerated trucks. The flow converts each temperature reading to Fahrenheit, checks whether the cargo is too warm, and sends the message to one of two outputs. You create the flow, read it back, change its graph, enable it, and delete it.

The guide is for developers who are comfortable with GraphQL. For the rules that it uses, such as node keys and whole-graph updates, see [How IoT Logic flows work](../how-flows-work.md).

## Prerequisites

You need an access token for the workspace you work in. The API takes the workspace from the token, so no operation on this page asks for a workspace ID. See [Authentication](../../authentication.md) for how to get a token and how the API picks the workspace.

Every request on this page is a GraphQL document with variables. Send each one as an HTTP POST request to `https://nvx4.eu.navixy.com/api/graphql`, with the document in the `query` field of a JSON body and the variables in the `variables` field. The following request reads one flow:

```bash
curl -L \
  --request POST \
  --url 'https://nvx4.eu.navixy.com/api/graphql' \
  --header 'Authorization: Bearer <ACCESS_TOKEN>' \
  --header 'Content-Type: application/json' \
  --data '{
    "query": "query GetFlow($id: ID!) { iotLogic { flow(id: $id) { id title isEnabled } } }",
    "variables": { "id": "01a0d2f0-485e-8000-801f-494c464c54be" }
  }'
```

Response:

```json
{
  "data": {
    "iotLogic": {
      "flow": {
        "id": "01a0d2f0-485e-8000-801f-494c464c54be",
        "title": "Cold chain monitoring",
        "isEnabled": false
      }
    }
  }
}
```

The steps below show only the document and its variables. In your GraphQL client, send the document as the query and the variables as its variables.

### Find the devices for the flow

A data source node lists the IDs of the devices that the flow processes. The devices must belong to your workspace. Read their IDs from Business Data Repository:

```graphql
query ListDevices {
  bdr {
    devices(first: 10) {
      nodes {
        id
        title
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "devices": {
        "nodes": [
          {
            "id": "019dbec6-d380-8000-801f-44455643e216",
            "title": "Reefer truck 12"
          },
          {
            "id": "00000000-0000-8000-801f-444556430003",
            "title": "Reefer truck 17"
          }
        ]
      }
    }
  }
}
```

Save the IDs of the devices that you want the flow to process.

### Find the attribute names of your devices

Expressions read the attributes of a device message by name, and the names depend on the device model. Before you write expressions, list the names that your devices report with the `deviceAttributes` query. See [Find the attribute names of your devices](reading-flows-and-device-data.md#find-the-attribute-names-of-your-devices).

## How a write describes a graph

A create or update request describes the graph in two lists:

* `nodes`: Every node of the flow. Each node has a `key`, a `type`, a `name`, and the fields of its type.
* `transitions`: Every connection between two nodes. `from` and `to` are node keys, and `type` is `SIMPLE`, `THEN`, or `ELSE`.

Each node type uses only its own fields. The API ignores the fields of other types without an error, so don't send them:

| Node type | Fields of the type |
| --- | --- |
| `DATA_SOURCE` | `deviceIds`: The devices whose messages enter the flow at this node |
| `INITIATE_ATTRIBUTES` | `attributes`: A list of assignments. Each one has a `name` for the new attribute and an `expression` that calculates it |
| `LOGIC` | `condition`: The expression to check. `expressionName` (optional): An attribute that stores the result of the check |
| `OUTPUT_ENDPOINT` | None |
| `ACTION` | `actions`: The commands that the node sends. `trackableNodeId` (optional): The name of a flow value that shows whether a message reached the node. `deviceMapping` (optional): The devices that receive the commands instead of the device that sent the message. See [Device Action](../node-types.md#device-action) |

Every node can also have a `description` and a `position`. `position` stores the `x` and `y` coordinates where the flow editor draws the node. A node without `position` has no stored coordinates, and a read returns `null` for it.

## Designing a flow

A flow is easier to fix and to change when you plan it before the first write. These practices keep a graph valid and readable:

* Draw the graph first. Decide which devices enter, which values the flow calculates, which checks it makes, and where each path ends. Every path that calculates something needs to end at an Output Endpoint node, or the attributes that it calculated are lost. See [How a message moves through a flow](../how-flows-work.md#how-a-message-moves-through-a-flow).
* Split long formulas. Calculate an intermediate value in one Initiate Attribute node and read it in the next one. Each formula stays short, and a validation error points at a smaller expression.
* Name attributes by one pattern. For example, put the measured value first and the unit last: `temperature_f`, `fuel_level_l`. Consistent names are easier to read in expressions and in Data Stream Analyzer.
* Lay out the nodes from left to right. Give the data sources the smallest `x`, then the calculations and the checks, then the outputs. Keep a fixed step between columns, such as 240. The flow is then easy to read when it opens in the flow editor.
* Build in small steps. Create the flow with a few nodes, read it back, and add the next nodes with an update. A small write that fails is easier to fix.
* Disable flows that you don't use. A disabled flow keeps its graph and processes nothing, so you can enable it again later. A disabled flow still keeps its devices from being deleted. See [Devices in a flow](../how-flows-work.md#devices-in-a-flow).

## Example scenario: Monitoring the cargo temperature of refrigerated trucks

To create, read, update, and enable the flow, follow these steps:

{% stepper %}
{% step %}
### Create the flow

Send the whole graph in one `flowCreate` mutation. The graph below has four nodes in a line: the trucks, the conversion, the check, and one output that receives both outcomes of the check.

```graphql
mutation CreateFlow($input: IotLogicFlowCreateInput!) {
  iotLogic {
    flowCreate(input: $input) {
      flow {
        id
        version
        title
        isEnabled
        deviceCount
        nodes {
          __typename
          id
          name
        }
        transitions {
          from
          to
          type
        }
      }
    }
  }
}
```

Variables:

```json
{
  "input": {
    "title": "Cold chain monitoring",
    "description": "Flags refrigerated trucks whose cargo is too warm.",
    "nodes": [
      {
        "key": "trucks",
        "type": "DATA_SOURCE",
        "name": "Refrigerated trucks",
        "deviceIds": [
          "019dbec6-d380-8000-801f-44455643e216",
          "00000000-0000-8000-801f-444556430003"
        ],
        "position": { "x": 0, "y": 0 }
      },
      {
        "key": "convert",
        "type": "INITIATE_ATTRIBUTES",
        "name": "Convert to Fahrenheit",
        "attributes": [
          { "name": "temperature_f", "expression": "temperature * 1.8 + 32" }
        ],
        "position": { "x": 240, "y": 0 }
      },
      {
        "key": "check",
        "type": "LOGIC",
        "name": "Too warm",
        "condition": "temperature > 8",
        "expressionName": "is_too_warm",
        "position": { "x": 480, "y": 0 }
      },
      {
        "key": "out",
        "type": "OUTPUT_ENDPOINT",
        "name": "Default output",
        "position": { "x": 720, "y": 0 }
      }
    ],
    "transitions": [
      { "from": "trucks", "to": "convert", "type": "SIMPLE" },
      { "from": "convert", "to": "check", "type": "SIMPLE" },
      { "from": "check", "to": "out", "type": "THEN" },
      { "from": "check", "to": "out", "type": "ELSE" }
    ]
  }
}
```

Response:

```json
{
  "data": {
    "iotLogic": {
      "flowCreate": {
        "flow": {
          "id": "01a0d2f0-485e-8000-801f-494c464c54be",
          "version": 1,
          "title": "Cold chain monitoring",
          "isEnabled": false,
          "deviceCount": 2,
          "nodes": [
            {
              "__typename": "IotLogicFlowDataSourceNode",
              "id": "01a0d2f0-485d-8000-801f-494c4e44be88",
              "name": "Refrigerated trucks"
            },
            {
              "__typename": "IotLogicFlowInitiateAttributesNode",
              "id": "01a0d2f0-485d-8001-801f-494c4e44d810",
              "name": "Convert to Fahrenheit"
            },
            {
              "__typename": "IotLogicFlowLogicNode",
              "id": "01a0d2f0-485d-8002-801f-494c4e44c534",
              "name": "Too warm"
            },
            {
              "__typename": "IotLogicFlowOutputEndpointNode",
              "id": "01a0d2f0-485d-8003-801f-494c4e44b89f",
              "name": "Default output"
            }
          ],
          "transitions": [
            {
              "from": "01a0d2f0-485d-8000-801f-494c4e44be88",
              "to": "01a0d2f0-485d-8001-801f-494c4e44d810",
              "type": "SIMPLE"
            },
            {
              "from": "01a0d2f0-485d-8001-801f-494c4e44d810",
              "to": "01a0d2f0-485d-8002-801f-494c4e44c534",
              "type": "SIMPLE"
            },
            {
              "from": "01a0d2f0-485d-8002-801f-494c4e44c534",
              "to": "01a0d2f0-485d-8003-801f-494c4e44b89f",
              "type": "THEN"
            },
            {
              "from": "01a0d2f0-485d-8002-801f-494c4e44c534",
              "to": "01a0d2f0-485d-8003-801f-494c4e44b89f",
              "type": "ELSE"
            }
          ]
        }
      }
    }
  }
}
```

The response has the IDs that Navixy created for the flow and for each node. In the response, transitions refer to nodes by these IDs, not by the keys of the request. Save the flow `id`, the `version`, and the node IDs. The update in a later step needs all of them.

Notes on the response:

* `isEnabled` is `false`. A new flow is always disabled, so it doesn't process messages until you enable it.
* `deviceCount` is `2`: one for each device of the data source.
* The logic node stores the result of its check in the `is_too_warm` attribute, because the request sets `expressionName`. Without `expressionName`, the node only chooses the transition.
* A message without a `temperature` value leaves the logic node by `ELSE`, because `>` gives no result for a missing value. The node then stores no `is_too_warm` value for that message. Other operators behave differently: `!=` returns true for a missing value, so the message leaves by `THEN`.
* Both outcomes lead to an Output Endpoint node, so the flow keeps the calculated `temperature_f` on every message.
{% endstep %}

{% step %}
### Read the flow

Read the flow with its node settings. `nodes` returns the `IotLogicFlowNode` interface, which has only the fields that every node has. To read the fields of a node type, add an inline fragment for that type, the `... on TypeName { }` blocks in the query below:

```graphql
query GetFlow($id: ID!) {
  iotLogic {
    flow(id: $id) {
      id
      version
      title
      isEnabled
      deviceCount
      nodes {
        id
        name
        position {
          x
          y
        }
        ... on IotLogicFlowDataSourceNode {
          devices {
            id
            title
          }
        }
        ... on IotLogicFlowInitiateAttributesNode {
          attributes {
            name
            expression
          }
        }
        ... on IotLogicFlowLogicNode {
          condition
          expressionName
        }
      }
    }
  }
}
```

Variables:

```json
{ "id": "01a0d2f0-485e-8000-801f-494c464c54be" }
```

Response:

```json
{
  "data": {
    "iotLogic": {
      "flow": {
        "id": "01a0d2f0-485e-8000-801f-494c464c54be",
        "version": 1,
        "title": "Cold chain monitoring",
        "isEnabled": false,
        "deviceCount": 2,
        "nodes": [
          {
            "id": "01a0d2f0-485d-8000-801f-494c4e44be88",
            "name": "Refrigerated trucks",
            "position": { "x": 0, "y": 0 },
            "devices": [
              {
                "id": "00000000-0000-8000-801f-444556430003",
                "title": "Reefer truck 17"
              },
              {
                "id": "019dbec6-d380-8000-801f-44455643e216",
                "title": "Reefer truck 12"
              }
            ]
          },
          {
            "id": "01a0d2f0-485d-8001-801f-494c4e44d810",
            "name": "Convert to Fahrenheit",
            "position": { "x": 240, "y": 0 },
            "attributes": [
              {
                "name": "temperature_f",
                "expression": "temperature * 1.8 + 32"
              }
            ]
          },
          {
            "id": "01a0d2f0-485d-8002-801f-494c4e44c534",
            "name": "Too warm",
            "position": { "x": 480, "y": 0 },
            "condition": "temperature > 8",
            "expressionName": "is_too_warm"
          },
          {
            "id": "01a0d2f0-485d-8003-801f-494c4e44b89f",
            "name": "Default output",
            "position": { "x": 720, "y": 0 }
          }
        ]
      }
    }
  }
}
```

`devices` returns the devices of a data source ordered by device ID, not in the order of the request. The list has no pagination. If you only need the number of devices, read `deviceCount` instead, because it doesn't load the device records.

If no flow of your workspace has the ID, `flow` returns `null` instead of an error. The common `node(id:)` query also returns flows, but it doesn't return flow nodes.
{% endstep %}

{% step %}
### Replace the graph

The cargo limit changes to 5 °C, and warm cargo needs its own output. Send the new graph in a `flowUpdate` mutation. Send the `nodes` and `transitions` lists together. They replace the stored graph, so send every node that the flow keeps, not only the changed ones.

Each node that the flow keeps has its stored `id` next to its `key`. The new output node has a key and no `id`, so Navixy creates it. The data source leaves out `deviceIds`, so it keeps its two devices.

```graphql
mutation UpdateFlow($input: IotLogicFlowUpdateInput!) {
  iotLogic {
    flowUpdate(input: $input) {
      flow {
        id
        version
        isEnabled
        deviceCount
        nodes {
          __typename
          id
          name
        }
        transitions {
          from
          to
          type
        }
      }
    }
  }
}
```

Variables:

```json
{
  "input": {
    "id": "01a0d2f0-485e-8000-801f-494c464c54be",
    "version": 1,
    "nodes": [
      {
        "key": "trucks",
        "id": "01a0d2f0-485d-8000-801f-494c4e44be88",
        "type": "DATA_SOURCE",
        "name": "Refrigerated trucks",
        "position": { "x": 0, "y": 0 }
      },
      {
        "key": "convert",
        "id": "01a0d2f0-485d-8001-801f-494c4e44d810",
        "type": "INITIATE_ATTRIBUTES",
        "name": "Convert to Fahrenheit",
        "attributes": [
          { "name": "temperature_f", "expression": "temperature * 1.8 + 32" }
        ],
        "position": { "x": 240, "y": 0 }
      },
      {
        "key": "check",
        "id": "01a0d2f0-485d-8002-801f-494c4e44c534",
        "type": "LOGIC",
        "name": "Too warm",
        "condition": "temperature > 5",
        "expressionName": "is_too_warm",
        "position": { "x": 480, "y": 0 }
      },
      {
        "key": "out",
        "id": "01a0d2f0-485d-8003-801f-494c4e44b89f",
        "type": "OUTPUT_ENDPOINT",
        "name": "Default output",
        "position": { "x": 720, "y": 120 }
      },
      {
        "key": "alerts",
        "type": "OUTPUT_ENDPOINT",
        "name": "Temperature alerts",
        "position": { "x": 720, "y": -120 }
      }
    ],
    "transitions": [
      { "from": "trucks", "to": "convert", "type": "SIMPLE" },
      { "from": "convert", "to": "check", "type": "SIMPLE" },
      { "from": "check", "to": "alerts", "type": "THEN" },
      { "from": "check", "to": "out", "type": "ELSE" }
    ]
  }
}
```

Response:

```json
{
  "data": {
    "iotLogic": {
      "flowUpdate": {
        "flow": {
          "id": "01a0d2f0-485e-8000-801f-494c464c54be",
          "version": 2,
          "isEnabled": false,
          "deviceCount": 2,
          "nodes": [
            {
              "__typename": "IotLogicFlowDataSourceNode",
              "id": "01a0d2f0-485d-8000-801f-494c4e44be88",
              "name": "Refrigerated trucks"
            },
            {
              "__typename": "IotLogicFlowInitiateAttributesNode",
              "id": "01a0d2f0-485d-8001-801f-494c4e44d810",
              "name": "Convert to Fahrenheit"
            },
            {
              "__typename": "IotLogicFlowLogicNode",
              "id": "01a0d2f0-485d-8002-801f-494c4e44c534",
              "name": "Too warm"
            },
            {
              "__typename": "IotLogicFlowOutputEndpointNode",
              "id": "01a0d2f0-485d-8003-801f-494c4e44b89f",
              "name": "Default output"
            },
            {
              "__typename": "IotLogicFlowOutputEndpointNode",
              "id": "01a0d2f0-90ee-8000-801f-494c4e44626a",
              "name": "Temperature alerts"
            }
          ],
          "transitions": [
            {
              "from": "01a0d2f0-485d-8000-801f-494c4e44be88",
              "to": "01a0d2f0-485d-8001-801f-494c4e44d810",
              "type": "SIMPLE"
            },
            {
              "from": "01a0d2f0-485d-8001-801f-494c4e44d810",
              "to": "01a0d2f0-485d-8002-801f-494c4e44c534",
              "type": "SIMPLE"
            },
            {
              "from": "01a0d2f0-485d-8002-801f-494c4e44c534",
              "to": "01a0d2f0-90ee-8000-801f-494c4e44626a",
              "type": "THEN"
            },
            {
              "from": "01a0d2f0-485d-8002-801f-494c4e44c534",
              "to": "01a0d2f0-485d-8003-801f-494c4e44b89f",
              "type": "ELSE"
            }
          ]
        }
      }
    }
  }
}
```

The four kept nodes have the same IDs as before, and the new output has an ID of its own. `deviceCount` is still `2`, because the data source left out `deviceIds`. `version` is now `2`.

Keep these rules in mind when you update a graph:

* A node that you leave out of `nodes` is removed, and a data source that you leave out is removed together with its devices.
* If you send the `key` of a stored node without its `id`, Navixy creates a new node with a new ID and removes the stored node. For a data source, that also removes its devices.
* Send `nodes` and `transitions` together. They are the two halves of one graph, and each one refers to the other.
* `title`, `description`, and `isEnabled` are optional in an update. A field that you leave out keeps its stored value.

To change the devices of a data source, send `deviceIds` with the full new list. The list replaces the stored devices. `deviceIds: []` removes every device from the data source.
{% endstep %}

{% step %}
### Enable the flow

Enable the flow when its graph is ready. Send `isEnabled` without the graph:

```graphql
mutation EnableFlow($input: IotLogicFlowUpdateInput!) {
  iotLogic {
    flowUpdate(input: $input) {
      flow {
        id
        version
        isEnabled
      }
    }
  }
}
```

Variables:

```json
{
  "input": {
    "id": "01a0d2f0-485e-8000-801f-494c464c54be",
    "version": 2,
    "isEnabled": true
  }
}
```

Response:

```json
{
  "data": {
    "iotLogic": {
      "flowUpdate": {
        "flow": {
          "id": "01a0d2f0-485e-8000-801f-494c464c54be",
          "version": 3,
          "isEnabled": true
        }
      }
    }
  }
}
```

Enabling a flow validates its whole graph again, including the checks against your other flows and against the attributes that your devices report. Those can change after the last save, so a flow that saved cleanly can still fail to enable. The failure is a validation error, described in [Flow validation errors](../flow-validation-errors.md).

An update that only disables a flow validates nothing, so a flow that no longer passes validation can always be disabled and deleted. To disable the flow, send `isEnabled: false` in the same way. You can also enable a flow in the same update that replaces its graph.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You now have an enabled flow that converts the cargo temperature of two refrigerated trucks to Fahrenheit, marks warm cargo in the `is_too_warm` attribute, and sends warm cargo to its own output.
{% endhint %}

After the trucks send their next messages, check what the flow calculated with `flowDebugValues`. See [Checking what a flow calculated](reading-flows-and-device-data.md#checking-what-a-flow-calculated).

## Handling version conflicts

Every update and delete sends the `version` that you last read. The version is required for flows. If someone changed the flow after you read it, the API returns a [conflict error](../../error-handling.md#version-conflict-409) and changes nothing:

```json
{
  "errors": [
    {
      "message": "Version conflict: expected 1, found 2",
      "path": ["iotLogic", "flowUpdate"],
      "extensions": {
        "type": "https://api.navixy.com/errors/conflict",
        "title": "Concurrent Modification Conflict",
        "status": 409,
        "detail": "Version conflict: expected 1, found 2",
        "instance": "/graphql",
        "code": "CONFLICT",
        "field": "input.version",
        "expectedVersion": 1,
        "currentVersion": 2,
        "traceId": "0af7651916cd43dd8448eb211c80319c",
        "timestamp": "2026-09-24T10:30:00Z"
      }
    }
  ]
}
```

To resolve the conflict, read the flow again to get its current graph and `version`. Apply your change to the current graph, and send the update again. Don't resend the graph that you read before: an update replaces the whole graph, so an outdated graph would undo the other change.

For a full explanation of how versions work, see [Optimistic locking](../../optimistic-locking.md).

## Deleting a flow

When you no longer need a flow, delete it with its current `version`:

```graphql
mutation DeleteFlow($input: IotLogicFlowDeleteInput!) {
  iotLogic {
    flowDelete(input: $input) {
      id
    }
  }
}
```

Variables:

```json
{
  "input": {
    "id": "01a0d2f0-485e-8000-801f-494c464c54be",
    "version": 3
  }
}
```

Response:

```json
{
  "data": {
    "iotLogic": {
      "flowDelete": {
        "id": "01a0d2f0-485e-8000-801f-494c464c54be"
      }
    }
  }
}
```

A deleted flow no longer processes messages, and its devices are removed from it. After the flow is deleted, it no longer blocks `deviceDelete` for those devices. `flowDelete` succeeds even while the flow lists devices. The API no longer returns the flow: `flow` returns `null` for its ID, and `flows` leaves it out. A second delete of the same flow returns `NOT_FOUND`.

{% hint style="warning" %}
Deletion can't be reversed through the API. To use the same graph later, read the flow and save its nodes and transitions before you delete it.
{% endhint %}

## Errors in this guide

The steps on this page most often fail for these reasons:

| Error | Cause | How to fix |
| --- | --- | --- |
| `VALIDATION_ERROR` without `violations` | A transition names a key that no node in the request has, two nodes have the same key, or an attribute name breaks the naming rule | Correct the input that `field` names. See [Errors outside the violations list](../flow-validation-errors.md#errors-outside-the-violations-list). |
| `ATTRIBUTE_REPORTED_BY_DEVICE` | A calculated attribute has the name of an attribute that a device of the flow reports | Rename the attribute. Check the names with `deviceAttributes` first, see [Find the attribute names of your devices](reading-flows-and-device-data.md#find-the-attribute-names-of-your-devices). |
| `EDGE_TYPE_MISMATCH` | A transition that leaves a logic node is `SIMPLE`, or a transition that leaves another node is `THEN` or `ELSE` | Use `THEN` or `ELSE` after a logic node, and `SIMPLE` after every other node. |
| `UNKNOWN_DEVICE` | A device in `deviceIds` doesn't exist, belongs to another workspace, or isn't one that your roles let you read | Correct the device ID, or remove it. |
| `CONFLICT` | Someone changed the flow after you read it | Read the flow again and apply your change to the current graph. See [Handling version conflicts](#handling-version-conflicts). |
| `PERMISSION_DENIED` | Your roles don't have the `create`, `update`, or `delete` action on IoT Logic flows | Ask an administrator for a role with the action. See [Authentication](../../authentication.md). |
| `NOT_FOUND` | The flow was deleted, or isn't in your workspace | Check the flow ID with `flows`. |

For every violation code and the structure of the error, see [Flow validation errors](../flow-validation-errors.md).

## See also

* [Reading flows and device data](reading-flows-and-device-data.md): Find the attribute names of your devices, check what a flow calculated, list flows, and find the flows that a device is in.
* [Flows](../api-reference/flows.md): See every field of the flow, node, and transition types, and every input of the flow mutations.
* [Flow validation errors](../flow-validation-errors.md): Find out why a write failed, and fix each violation.
