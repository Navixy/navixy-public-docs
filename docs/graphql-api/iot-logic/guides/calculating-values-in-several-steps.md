---
description: >-
  Build an IoT Logic flow through Navixy GraphQL API that calculates values in
  a chain of nodes: fuel in litres, then percentage and change, then a check.
tags:
  - guide
  - beta
---

# Calculating values in several steps

A flow can calculate a value in one node and use it in the next one. This guide builds a flow for tanker trucks that reads a fuel sensor in three steps. The first node converts the sensor voltage to litres. The second node calculates the fill percentage and the change since the previous message. A logic node then marks a sudden drop as a possible fuel drain.

The guide is for developers who have built a first flow. If you haven't, start with [Creating and updating flows](creating-and-updating-flows.md), which explains the request format, node keys, and updates.

## Prerequisites

You need an access token for the workspace you work in, and the ID of a device that reports a fuel sensor value. See [Find the devices for the flow](creating-and-updating-flows.md#find-the-devices-for-the-flow).

You also need the name of the attribute that the fuel sensor reports. The name depends on the device model. The example uses `analog_1`, the voltage of the first analog input in millivolts. See [Find the attribute names of your devices](reading-flows-and-device-data.md#find-the-attribute-names-of-your-devices).

## Why a chain of nodes

A chain of Initiate Attribute nodes splits one long calculation into short steps. It has three advantages over one long expression:

* Each expression stays short. A condition can have at most 480 characters, and an attribute expression at most 960.
* Each intermediate value is an attribute of its own, such as `fuel_level_l`. Later nodes read it by name, and so do the other tools of the Navixy platform once the message reaches an Output Endpoint.
* A validation error points at one short expression, so it is easier to fix.

Build the chain as one straight path. Don't calculate two values on parallel paths and join them in a later node: paths in a flow never join, and a write with such a graph fails with `SIMULTANEOUS_PATHS`. To calculate several values from the same input, put them in one node or in consecutive nodes.

## Example scenario: Detecting a fuel drain on tanker trucks

To build and check the flow, follow these steps:

{% stepper %}
{% step %}
### Create the flow

Send the whole chain in one `flowCreate` mutation. The query requests the attributes and the condition, so the response confirms what Navixy stored:

```graphql
mutation CreateFlow($input: IotLogicFlowCreateInput!) {
  iotLogic {
    flowCreate(input: $input) {
      flow {
        id
        version
        isEnabled
        nodes {
          __typename
          id
          name
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
    "title": "Fuel level and drain detection",
    "description": "Converts the fuel sensor voltage to litres and flags sudden drops.",
    "nodes": [
      {
        "key": "tanks",
        "type": "DATA_SOURCE",
        "name": "Tanker trucks",
        "deviceIds": ["019dbec6-d381-8000-801f-44455643f7c4"],
        "position": { "x": 0, "y": 0 }
      },
      {
        "key": "litres",
        "type": "INITIATE_ATTRIBUTES",
        "name": "Fuel in litres",
        "attributes": [
          { "name": "fuel_level_l", "expression": "(analog_1 - 500) * 0.1" }
        ],
        "position": { "x": 240, "y": 0 }
      },
      {
        "key": "derived",
        "type": "INITIATE_ATTRIBUTES",
        "name": "Percentage and change",
        "attributes": [
          { "name": "fuel_level_pct", "expression": "fuel_level_l / 400 * 100" },
          {
            "name": "fuel_change_l",
            "expression": "fuel_level_l - value('fuel_level_l', 1, 'valid')"
          }
        ],
        "position": { "x": 480, "y": 0 }
      },
      {
        "key": "drain",
        "type": "LOGIC",
        "name": "Sudden drop",
        "condition": "fuel_change_l < -20",
        "expressionName": "is_fuel_drain",
        "position": { "x": 720, "y": 0 }
      },
      {
        "key": "out",
        "type": "OUTPUT_ENDPOINT",
        "name": "Default output",
        "position": { "x": 960, "y": 0 }
      }
    ],
    "transitions": [
      { "from": "tanks", "to": "litres", "type": "SIMPLE" },
      { "from": "litres", "to": "derived", "type": "SIMPLE" },
      { "from": "derived", "to": "drain", "type": "SIMPLE" },
      { "from": "drain", "to": "out", "type": "THEN" },
      { "from": "drain", "to": "out", "type": "ELSE" }
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
          "id": "01a0d413-9e6c-8000-801f-494c464c237e",
          "version": 1,
          "isEnabled": false,
          "nodes": [
            {
              "__typename": "IotLogicFlowDataSourceNode",
              "id": "01a0d413-9e6b-8000-801f-494c4e449820",
              "name": "Tanker trucks"
            },
            {
              "__typename": "IotLogicFlowInitiateAttributesNode",
              "id": "01a0d413-9e6b-8001-801f-494c4e44c174",
              "name": "Fuel in litres",
              "attributes": [
                { "name": "fuel_level_l", "expression": "(analog_1 - 500) * 0.1" }
              ]
            },
            {
              "__typename": "IotLogicFlowInitiateAttributesNode",
              "id": "01a0d413-9e6b-8002-801f-494c4e44f434",
              "name": "Percentage and change",
              "attributes": [
                { "name": "fuel_level_pct", "expression": "fuel_level_l / 400 * 100" },
                {
                  "name": "fuel_change_l",
                  "expression": "fuel_level_l - value('fuel_level_l', 1, 'valid')"
                }
              ]
            },
            {
              "__typename": "IotLogicFlowLogicNode",
              "id": "01a0d413-9e6b-8003-801f-494c4e444ad0",
              "name": "Sudden drop",
              "condition": "fuel_change_l < -20",
              "expressionName": "is_fuel_drain"
            },
            {
              "__typename": "IotLogicFlowOutputEndpointNode",
              "id": "01a0d413-9e6b-8004-801f-494c4e44cadf",
              "name": "Default output"
            }
          ],
          "transitions": [
            {
              "from": "01a0d413-9e6b-8000-801f-494c4e449820",
              "to": "01a0d413-9e6b-8001-801f-494c4e44c174",
              "type": "SIMPLE"
            },
            {
              "from": "01a0d413-9e6b-8001-801f-494c4e44c174",
              "to": "01a0d413-9e6b-8002-801f-494c4e44f434",
              "type": "SIMPLE"
            },
            {
              "from": "01a0d413-9e6b-8002-801f-494c4e44f434",
              "to": "01a0d413-9e6b-8003-801f-494c4e444ad0",
              "type": "SIMPLE"
            },
            {
              "from": "01a0d413-9e6b-8003-801f-494c4e444ad0",
              "to": "01a0d413-9e6b-8004-801f-494c4e44cadf",
              "type": "THEN"
            },
            {
              "from": "01a0d413-9e6b-8003-801f-494c4e444ad0",
              "to": "01a0d413-9e6b-8004-801f-494c4e44cadf",
              "type": "ELSE"
            }
          ]
        }
      }
    }
  }
}
```
{% endstep %}

{% step %}
### Check how each step reads the previous one

Each node of the chain reads what an earlier node calculated. This table follows one message through the flow:

| Node | Reads | Calculates |
| --- | --- | --- |
| Fuel in litres | `analog_1`, which the device sends | `fuel_level_l`: the voltage above 500 mV, at 0.1 litre per millivolt |
| Percentage and change | `fuel_level_l` from the previous node, and the previous valid `fuel_level_l` from the history of the device | `fuel_level_pct` for a 400-litre tank, and `fuel_change_l` since the previous message |
| Sudden drop | `fuel_change_l` from the previous node | `is_fuel_drain`: true when the tank lost more than 20 litres since the previous message |

Three details make the chain work:

* `value('fuel_level_l', 1, 'valid')` reads the last value of `fuel_level_l` before the current one, and skips messages that had no value. Navixy keeps the value in the history of the device only because the path ends at an Output Endpoint. On a path that ended anywhere else, the flow would have no previous value to read.
* The first message of a device has no previous value, so `fuel_change_l` has no value either. The condition `fuel_change_l < -20` then isn't true, and the message leaves by `ELSE`. `is_fuel_drain` gets no value for that message. That's the result you want: a first message is never a drain.
* Both outcomes of the logic node lead to the Output Endpoint. The flow keeps `fuel_level_l`, `fuel_level_pct`, and `fuel_change_l` on every message, not only on the messages that look like a drain.
{% endstep %}

{% step %}
### Enable the flow

The flow is created disabled. Enable it with `isEnabled: true` and the current `version`, as in [Enable the flow](creating-and-updating-flows.md#enable-the-flow). Enabling validates the whole graph again, including the check that no other flow already produces `fuel_level_l`, `fuel_level_pct`, `fuel_change_l`, or `is_fuel_drain` for the same devices.
{% endstep %}

{% step %}
### Check the calculated values

After the trucks send their next messages, read their values with `flowDebugValues`, as in [Checking what a flow calculated](reading-flows-and-device-data.md#checking-what-a-flow-calculated). Each message with `analog_1` has `fuel_level_l` and `fuel_level_pct`. From the second message on, it also has `fuel_change_l` and `is_fuel_drain`. Compare `fuel_level_l` with `analog_1` to confirm the conversion, and read `nonNullFields` to see the previous valid value that `fuel_change_l` was calculated from.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You now have a flow that turns a raw fuel sensor voltage into litres, a fill percentage, and the change since the previous message, and marks a sudden drop in the `is_fuel_drain` attribute.
{% endhint %}

## Extending the chain

To react to a drain, add nodes to the `THEN` outcome of the logic node, and end the new path at the Output Endpoint, so that the flow still keeps its attributes. For example, put an Initiate Attribute node between the `THEN` outcome and the Output Endpoint to calculate the size of the drop, `fuel_drain_l`, only for the messages that look like a drain. The `ELSE` outcome still leads straight to the Output Endpoint. The two paths can meet there because a message never takes both. See [IF/THEN Logic](../node-types.md#if-then-logic) for the rules of logic node outcomes.

To add a calculation, add an Initiate Attribute node to the chain with an update. The update sends the whole graph, so send the stored nodes with their IDs and the new node with a key only. See [Replace the graph](creating-and-updating-flows.md#replace-the-graph).

## Errors in this scenario

A chain of calculations most often fails validation for these reasons:

| Violation | Cause in a chain | How to fix |
| --- | --- | --- |
| `INVALID_EXPRESSION` | A formula doesn't parse, for example because of an unknown function | Correct the formula. The message names the logic node, or the attribute for an attribute formula, and the position. `nodeIds` has the node. |
| `SIMULTANEOUS_PATHS` | Two values are calculated on parallel paths that join in one node | Put the calculations in one straight chain. |
| `DUPLICATE_ATTRIBUTE_IN_FLOW` | Two nodes of the chain calculate an attribute with the same name | Rename one of them. |
| `ATTRIBUTE_ALREADY_PRODUCED` | Another flow already calculates the same attribute for one of the devices | Rename the attribute, or remove the device from one of the flows. |

For every violation code and the structure of the error, see [Flow validation errors](../flow-validation-errors.md).

## See also

* [IoT Logic node types](../node-types.md): See the fields and rules of each node in the chain.
* [Creating and updating flows](creating-and-updating-flows.md): Read, update, enable, and delete the flow.
