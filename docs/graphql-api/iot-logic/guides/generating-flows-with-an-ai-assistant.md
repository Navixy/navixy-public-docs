---
description: >-
  Rules and a checklist for an AI assistant that writes IoT Logic flows for
  Navixy GraphQL API: what to ask first, what to produce, and how to validate.
hidden: true
tags:
  - guide
  - beta
---

# Generating flows with an AI assistant

An AI assistant can write the input of a `flowCreate` or `flowUpdate` mutation from a description in plain language. This page gives the rules that such an input must follow, in a form that you can give to the assistant together with your request. It is for developers who use an AI assistant, or build one, to create IoT Logic flows.

The assistant produces a draft. Navixy validates every write and returns every problem at once, so the reliable loop is: generate, send, read the violations, and generate again. See [Flow validation errors](../flow-validation-errors.md).

## How to use this page

To get a flow from an assistant, follow these steps:

1. Give the assistant this page, the [IoT Logic node types](../node-types.md) page, and the schema file [`nvx4-iot-logic.graphql`](../../developer-resources/public-graphql-schema.md).
2. Describe the flow in plain language, with the answers to the questions in [Ask before generating](#ask-before-generating).
3. Ask for the `variables` JSON of a `flowCreate` mutation, in the format in [Output format](#output-format).
4. Send the mutation. If it fails, give the assistant the whole `extensions.violations` list and ask for a corrected input.

## Ask before generating

A flow depends on facts that only the user knows. The assistant asks for them before it writes anything, rather than guessing:

* **Devices.** Which devices enter the flow, by ID. If the user gives device titles, look up the IDs with the Business Data Repository `devices` query first.
* **Attribute names.** The exact names that the devices report, such as `analog_1` or `din1`. The names depend on the device model. The user reads them in Data Stream Analyzer, or lists them with the `deviceAttributes` query. See [Find the attribute names of your devices](reading-flows-and-device-data.md#find-the-attribute-names-of-your-devices).
* **Calculations.** What each new value means, its unit, and its formula, or the data needed to write the formula, such as a tank size.
* **Conditions.** Each threshold and what happens when it is crossed.
* **Outputs.** Which paths keep their results. Every path that calculates something ends at an Output Endpoint, which sends the result to the Navixy platform.
* **Commands.** Which commands a condition sends, and to which devices: an output number to switch on or off, or a command text. Only Teltonika device models receive commands.

If an answer is missing, the assistant asks for it. An invented attribute name saves without an error but never produces a value, because validation doesn't check the names against the data of the device.

## Output format

The assistant returns the `variables` of the mutation below as one JSON object, without comments:

```graphql
mutation CreateFlow($input: IotLogicFlowCreateInput!) {
  iotLogic {
    flowCreate(input: $input) {
      flow {
        id
        version
      }
    }
  }
}
```

The object has one field, `input`, with these fields:

| Field | Rule |
| --- | --- |
| `title` | Printable ASCII, at most 255 characters, not blank |
| `description` | Optional. Printable ASCII and line breaks, at most 4,000 characters |
| `nodes` | Every node of the flow, at most 64 |
| `transitions` | Every transition of the flow, at most 96 |

For an update, the object also has the `id` and the current `version` of the flow. The `nodes` and `transitions` lists are then the whole new graph, and every node that the flow keeps has its stored `id` next to its `key`.

## Node rules

Every node has a unique `key`, a `type`, and a `name`. The `name` uses English letters, digits, underscores, dashes, slashes, and single spaces between words. The fields of each type:

| Node type | Fields | Rules |
| --- | --- | --- |
| `DATA_SOURCE` | `deviceIds` | At least one per flow. Nothing leads into it. At most 25,000 devices across the data sources of a flow. |
| `INITIATE_ATTRIBUTES` | `attributes`: a list of `{ name, expression }` | At most 50 attributes per node, 200 per flow. Each expression at most 960 characters on one line. |
| `LOGIC` | `condition`, and optionally `expressionName` | The condition at most 480 characters on one line. |
| `OUTPUT_ENDPOINT` | None | Ends every path that calculates something. |
| `ACTION` | `actions`: a list of `{ setOutput: { output, enabled } }` or `{ rawCommand: { text } }`, and optionally `trackableNodeId` and `deviceMapping` | At most 10 actions per node. `output` from 1 to 8, `text` at most 512 characters and not blank. Next to an Output Endpoint on the same outcome, if the path calculated anything. See [Device Action](../node-types.md#device-action). |

Attribute names, including `expressionName`, use lowercase letters and digits in groups joined by single underscores, such as `fuel_level_l`. They are never one of the 12 names that Navixy reserves for the position of a device, such as `position_latitude` (see [Initiate Attribute](../node-types.md#initiate-attribute)). They never repeat a name that the devices already report, that another node on the same path produces, or that another flow produces for the same devices.

Every text field accepts printable ASCII only. A letter from another alphabet that looks like a Latin one fails the write with `NON_LATIN_INPUT`.

## Transition rules

A transition has `from` and `to`, which are node keys, and a `type`:

* A transition that leaves a logic node is `THEN` or `ELSE`. Every other transition is `SIMPLE`.
* `ELSE` is optional. A logic node with only `THEN` transitions is valid, and a message for which the condition isn't true goes no further.
* One outcome can have several transitions, for example `THEN` to an Output Endpoint and `THEN` to an Initiate Attribute node that starts a path of its own.
* The graph has no loops, and every node is reachable from a data source.
* Two paths that are active for the same message never lead into the same node. The `THEN` and `ELSE` outcomes of one logic node are never both active, so they can lead into the same Output Endpoint.

The last rule is the one that assistants break most often. A graph where two branches rejoin looks natural, but it fails with `SIMULTANEOUS_PATHS`. To calculate several values from one input, put them in one Initiate Attribute node, or in a straight chain of nodes.

## Layout

The assistant gives every node a `position`, so the flow opens readable in the flow editor. Lay out the nodes in columns from left to right:

* The data sources at `x: 0`, one above another with a step of 160 in `y`.
* Each following node one column to the right, with a step of 240 in `x`.
* The two outcomes of a logic node one above the other, such as `THEN` at a smaller `y` than `ELSE`.

## Check before sending

The assistant checks its own output against this list before it returns it. Each item names the violation that the write returns when the item fails:

* [ ] At least one `DATA_SOURCE`, and no transition leads into one (`MISSING_DATA_SOURCE`, `DATA_SOURCE_HAS_INCOMING_EDGE`)
* [ ] Every node is reachable from a data source (`UNREACHABLE_NODE`)
* [ ] No loop in the transitions (`CYCLE_DETECTED`)
* [ ] No two parallel paths lead into the same node (`SIMULTANEOUS_PATHS`)
* [ ] Every path that calculates something ends at an `OUTPUT_ENDPOINT` (no violation: the values are lost without an error)
* [ ] Every transition that leaves a logic node is `THEN` or `ELSE`, and every other transition is `SIMPLE` (`EDGE_TYPE_MISMATCH`)
* [ ] Each attribute name appears once on any path through the flow. Only the `THEN` and `ELSE` paths of one logic node may repeat it (`DUPLICATE_ATTRIBUTE_IN_FLOW`)
* [ ] No attribute name is one of the reserved position names, such as `position_latitude` (`RESERVED_ATTRIBUTE_NAME`)
* [ ] Every text field is printable ASCII (`NON_LATIN_INPUT`)
* [ ] Every expression parses and stays within its length (`INVALID_EXPRESSION`, `EXPRESSION_TOO_LONG`)
* [ ] Every Device Action node has at most 10 valid actions, and its `trackableNodeId`, if any, is unique in the flow (`ACTIONS_PER_NODE_EXCEEDED`, `INVALID_DEVICE_COMMAND`, `DUPLICATE_TRACKABLE_NODE_ID`)
* [ ] Keys are unique, and every transition names existing keys (single `VALIDATION_ERROR` before validation)

Two checks compare the flow with data in Navixy: `ATTRIBUTE_ALREADY_PRODUCED` with your other flows, and `ATTRIBUTE_REPORTED_BY_DEVICE` with the attributes of your devices. Give the assistant the result of the `deviceAttributes` query for the devices of the flow, so that it can avoid both. The write still reports them, and they can appear on a later attempt, after the other violations are fixed.

## Example

The [Calculating values in several steps](calculating-values-in-several-steps.md) guide has a complete input that follows every rule on this page: one data source, a chain of two Initiate Attribute nodes, a logic node, and one Output Endpoint for both outcomes. Give it to the assistant as the model to follow.

The smallest valid flow has one data source and one Output Endpoint:

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

## See also

* [IoT Logic node types](../node-types.md): Give the assistant the fields and rules of every node type.
* [Flow validation errors](../flow-validation-errors.md): Read the violations that a failed write returns.
