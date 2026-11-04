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

1. Give the assistant this page, the [IoT Logic node types](../node-types.md) page, the [Expression syntax reference](https://app.gitbook.com/s/8Ti8OItwJGZAFzK8jsG6/navixy-iot-logic-expression-language/expression-syntax-reference), and the schema file [`nvx4-iot-logic.graphql`](../../developer-resources/public-graphql-schema.md).
2. Describe the flow in plain language, with the answers to the questions in [Ask before generating](#ask-before-generating).
3. Ask for the `variables` JSON of a `flowCreate` mutation, in the format in [Output format](#output-format).
4. Send the mutation. If it fails, give the assistant the whole `extensions.violations` list and ask for a corrected input.

## Ask before generating

A flow depends on facts that only the user knows. The assistant asks for them before it writes anything, rather than guessing:

* **Devices.** Which devices enter the flow, by ID. If the user gives device titles, look up the IDs with the Business Data Repository `devices` query first. The devices must belong to the workspace and be ones that the user's roles let them read.
* **Attribute names.** The exact names that the devices report, such as `analog_1` or `din1`. The names depend on the device model. The user reads them in Data Stream Analyzer, or lists them with the `deviceAttributes` query. See [Find the attribute names of your devices](reading-flows-and-device-data.md#find-the-attribute-names-of-your-devices).
* **Calculations.** What each new value means, its unit, and its formula, or the data needed to write the formula, such as a tank size.
* **Conditions.** Each threshold, what happens when it is crossed, and what happens when a message doesn't have the value. See [Missing values](#missing-values).
* **Outputs.** Which paths keep their results. Every path that calculates something ends at an Output Endpoint, which sends the result to the Navixy platform.
* **Commands.** Which commands a condition sends, and to which devices: an output number to switch on or off, or a command text. A Device Action node sends its commands on every message that reaches it, so the assistant asks for the condition that sends them. If the commands go to another device than the one that reported, such as the tractor unit of a trailer, the assistant asks for the pairs of source and target devices. Only Teltonika device models receive commands.
* **Notifications.** Which system outside Navixy a condition sends a request to, at which URL, with which headers, and what the request body contains. The receiving system defines the URL, the headers, and the format of the body. The assistant also asks for the condition that sends the request, because a Webhook node sends one request for every message that reaches it, and for the values of the message that the body carries.
* **Secrets.** Which header the receiving system expects for authentication, such as `Authorization` or `X-Api-Key`, and its value. A system that needs none gets no header. If the user doesn't give the value, the assistant writes a visible stand-in, such as `Bearer <your token>`, and tells the user to replace it before sending the write. Navixy sends a header value as it is, so a stand-in that stays in the flow reaches the receiving system.

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

For an update, the object also has the `id` and the current `version` of the flow. The `nodes` and `transitions` lists are then the whole new graph, and every node that the flow keeps has its stored `id` next to its `key`. Send both lists together.

An update replaces some fields of a stored node and keeps others, depending on the field. Before it changes a flow, the assistant reads the flow with every field of every node type, and then follows these rules:

| Field | When the update leaves it out |
| --- | --- |
| `deviceIds` of a data source | The stored devices stay. `[]` removes all of them, and `null` fails |
| `actions` and `trackableNodeId` of a Device Action node | They are removed, so send them every time |
| `deviceMapping` of a Device Action node | The stored mapping stays. `[]` removes it, and `null` fails |
| `url`, `body`, and `trackableNodeId` of a Webhook node | The write fails, because they are required |
| `headers` of a Webhook node | They are removed, so send them every time |
| `title`, `description`, or `isEnabled` of the flow | The stored value stays. `null` fails for `title` and `isEnabled` |
| `name` of any node, `condition` of a logic node | The write fails, because they are required |
| `description` or `position` of any node | They are removed |
| `expressionName` of a logic node, `attributes` of an Initiate Attribute node | They are removed |

A read returns nodes with their IDs, and transitions with the IDs of the nodes that they connect. Neither has a key. To build the update, use the stored `id` of each node as its `key`, and write each transition with those keys. The following query reads every field of every node type. `trackableNodeId` has an alias on the Webhook node, because it is `String!` there and `String` on a Device Action node, and GraphQL refuses one response field with two types:

```graphql
query ReadFlowForUpdate($id: ID!) {
  iotLogic {
    flow(id: $id) {
      id
      version
      title
      description
      isEnabled
      nodes {
        id
        name
        description
        position { x y }
        ... on IotLogicFlowDataSourceNode { devices { id } }
        ... on IotLogicFlowInitiateAttributesNode { attributes { name expression } }
        ... on IotLogicFlowLogicNode { condition expressionName }
        ... on IotLogicFlowActionNode {
          actions {
            ... on IotLogicSetOutputAction { output enabled }
            ... on IotLogicRawCommandAction { text }
          }
          trackableNodeId
          deviceMapping { source { id } targets { id } }
        }
        ... on IotLogicFlowWebhookNode {
          url
          headers { key value }
          body
          webhookTrackableNodeId: trackableNodeId
        }
      }
      transitions { from to type }
    }
  }
}
```

The update and the delete use these documents. Their variables have the same `input` object, with the flow `id` and its current `version`:

```graphql
mutation UpdateFlow($input: IotLogicFlowUpdateInput!) {
  iotLogic {
    flowUpdate(input: $input) {
      flow {
        id
        version
      }
    }
  }
}

mutation DeleteFlow($input: IotLogicFlowDeleteInput!) {
  iotLogic {
    flowDelete(input: $input) {
      id
    }
  }
}
```

## Node rules

Every node has a unique `key`, a `type`, and a `name`. The `name` uses English letters, digits, underscores, dashes, slashes, and single spaces between words, at most 255 characters. The fields of each type:

| Node type | Fields | Rules |
| --- | --- | --- |
| `DATA_SOURCE` | `deviceIds`, which a new data source can leave out to start with no devices | At least one per flow. Nothing leads into it. At most 25,000 devices across the data sources of a flow. One device is in at most one of the data sources whose paths reach the same node. |
| `INITIATE_ATTRIBUTES` | `attributes`: a list of `{ name, expression }` | At most 50 attributes per node, 200 per flow, counting every `expressionName`. Each expression at most 960 characters on one line. |
| `LOGIC` | `condition`, and optionally `expressionName` | The condition at most 480 characters on one line. `expressionName` stores the result of the check as an attribute. |
| `OUTPUT_ENDPOINT` | None | Ends every path that calculates something. Only one Output Endpoint keeps attributes for each message. See [Output Endpoints](#output-endpoints). |
| `ACTION` | `actions`: a list of `{ setOutput: { output, enabled } }` or `{ rawCommand: { text } }`, and optionally `trackableNodeId` and `deviceMapping`: a list of `{ sourceDeviceId, targetDeviceIds }` | At most 10 actions per node, each with exactly one of `setOutput` and `rawCommand`. `output` from 1 to 8, `text` at most 512 characters, printable ASCII, not blank, with no placeholders. One output is switched at most once on a path of a message. At most 1,000 targets per node, no entry without targets, one entry per source device. On the `THEN` outcome of a logic node, next to an Output Endpoint on the same outcome. See [Device Action](../node-types.md#device-action). |
| `WEBHOOK` | `url`, `body`, and `trackableNodeId`, and optionally `headers`: a list of `{ key, value }` | At most 10 per flow and 10 headers per node. `url` at most 255 characters, starting with `http://` or `https://` in lowercase, never `localhost`, a loopback address, or a private address. `body` at most 4,000 characters, with the placeholders in [Webhook body](#webhook-body). `{{device_id}}` is the only placeholder in `url` and header values. `trackableNodeId` is a new name that follows the rule of attribute names, such as `dispatcher_notified`. On the `THEN` outcome of a logic node, next to an Output Endpoint on the same outcome. See [Webhook](../node-types.md#webhook). |

Attribute names, including `expressionName` and `trackableNodeId`, use lowercase letters and digits in groups joined by single underscores, such as `fuel_level_l`, at most 48 characters. A name can end with `_` and a channel number, such as `temperature_2`, which doesn't start with 0. They are never one of the 12 names that Navixy reserves for the position of a device, such as `position_latitude` (see [Initiate Attribute](../node-types.md#initiate-attribute)). They never repeat a name that the devices already report, that another node on the same path produces, or that another flow produces for the same devices.

### Expressions

Conditions and attribute expressions are written in the [Navixy IoT Logic Expression Language](https://app.gitbook.com/s/8Ti8OItwJGZAFzK8jsG6/navixy-iot-logic-expression-language/overview). Give the assistant its [Expression syntax reference](https://app.gitbook.com/s/8Ti8OItwJGZAFzK8jsG6/navixy-iot-logic-expression-language/expression-syntax-reference) together with this page. An expression can read:

* the attributes that the device reports, by the names that the user confirmed;
* the attributes that an earlier node on the same path calculated;
* the position of the device, as `position_latitude`, `position_longitude`, `position_speed` in km/h, and the other reserved position names, whatever the device model;
* earlier values of an attribute with `value()`, such as `value('fuel_level_l', 1, 'valid')` for the previous valid value. Navixy keeps the last 12 values of each attribute of a device.

When both numbers of a division are whole numbers, the result is a whole number too, and the fraction is lost: `21 * 9 / 5` gives `37`, not `37.8`. Write one of the numbers as a decimal, as in `temperature * 1.8 + 32` or `raw / 10.0`. See [Data types](https://app.gitbook.com/s/8Ti8OItwJGZAFzK8jsG6/navixy-iot-logic-expression-language/expression-syntax-reference#data-types-and-type-handling).

Navixy checks the syntax of a condition, and of an attribute expression of at most 480 characters, when you save the flow (`INVALID_EXPRESSION`). A longer attribute expression is saved without the check, and if it has an error, the attribute gets no value. The assistant keeps attribute expressions at 480 characters or shorter, so that the write reports every syntax error.

### Missing values

A message doesn't always have every attribute. A missing value resolves to `null`, and Navixy reports no error for it. See [Null propagation](https://app.gitbook.com/s/8Ti8OItwJGZAFzK8jsG6/navixy-iot-logic-expression-language/expression-syntax-reference#null-propagation):

* In a condition, only `==`, `!=`, `=~`, and `!~` give a real result. Every other operator, such as `>`, `<`, `!`, or arithmetic, stops the condition, and a stopped condition sends the message to the `ELSE` outcome.
* So `temperature > 8` sends a message without `temperature` to `ELSE`, and `speed != 60` sends a message without `speed` to `THEN`. Nodes on either outcome, a Device Action or Webhook node included, fire for messages that don't have the value.
* In an Initiate Attribute node, an expression that reads a missing value produces no attribute for that message. The other attributes of the node are still calculated.

When the user's answer to "what happens without the value" is "nothing", the assistant puts a presence check first in the condition, before the comparison: `temperature != null && temperature > 8`. `&&` stops at the first false, so a message without `temperature` never reaches the comparison. A separate logic node with `temperature != null` before the other nodes does the same for a whole branch. Its `ELSE` outcome then leads only to an Output Endpoint, or nowhere: a message that reaches no Output Endpoint still reaches the Navixy platform with the values that the device sent.

### Output Endpoints

Only one Output Endpoint keeps attributes for each message. When two paths are active for the same message and each one ends at its own Output Endpoint, Navixy keeps the attributes of only one of them, and the write reports no error. So the assistant calculates every attribute on one path, or ends the paths in the `THEN` and `ELSE` outcomes of one logic node, which are never active together and can share one Output Endpoint.

Every text field accepts printable ASCII only, except the `body` of a Webhook node. A letter from another alphabet that looks like a Latin one fails the write with `NON_LATIN_INPUT`.

### Webhook body

The body of a Webhook node is a JSON template inside a JSON string, so every quotation mark of the template is escaped as `\"`. Navixy replaces each placeholder with a value of the message when it sends the request, and checks neither the names nor the JSON when you save the flow. So the assistant follows these rules:

* The placeholders are `{{device_id}}`, `{{device_label}}` for the device title, `{{device_imei}}`, and `{{<attribute name>}}` for an attribute that the device reports or that the path calculated before the Webhook node. A name in braces uses the rule of attribute names, with no spaces inside the braces: `{{ speed }}` is sent as it is written.
* A text value goes in quotation marks: `\"name\": \"{{device_label}}\"`. Navixy inserts the value without them.
* A number or a `true` or `false` value can go without quotation marks, `\"temperature\": {{temperature}}`, only when the condition before the node checks that attribute. A message without the value otherwise gives a body that isn't valid JSON.
* Every attribute name in the body is one that the user confirmed, as for expressions. A misspelled name gives empty text in every request, and the write reports no error.
* The headers never include `Content-Type` other than `application/json`, or the headers that Navixy sets itself, such as `Host` and `Content-Length`. Navixy adds `Content-Type: application/json` when the node has none.

For example, the body `{"device": "{{device_id}}", "temperature": {{temperature}}}` is written in the input as:

```json
"body": "{\"device\": \"{{device_id}}\", \"temperature\": {{temperature}}}"
```

## Transition rules

A transition has `from` and `to`, which are node keys, and a `type`:

* A transition that leaves a logic node is `THEN` or `ELSE`. Every other transition is `SIMPLE`.
* `ELSE` is optional. A logic node with only `THEN` transitions is valid, and a message for which the condition isn't true goes no further.
* One outcome can have several transitions, for example `THEN` to an Output Endpoint and `THEN` to an Initiate Attribute node that starts a path of its own.
* A Device Action or Webhook node ends its path. The assistant gives it no outgoing transition, and leads the same outcome to an Output Endpoint as well, so the path keeps its attributes.
* The graph has no loops, and every node is reachable from a data source.
* Two paths that are active for the same message never lead into the same node. Outcomes that are never active together can lead into the same node: the `THEN` and `ELSE` outcomes of one logic node, and also the `ELSE` outcome of a logic node and any outcome of a logic node that comes after its `THEN`.

The last rule is the one that assistants break most often. A graph where two branches rejoin looks natural, but it fails with `SIMULTANEOUS_PATHS`. To calculate several values from one input, put them in one Initiate Attribute node, or in a straight chain of nodes.

## Layout

The assistant gives every node a `position`, so the flow opens readable in the flow editor. Lay out the nodes in columns from left to right:

* The data sources at `x: 0`, one above another with a step of 160 in `y`.
* Each following node one column to the right, with a step of 240 in `x`.
* The two outcomes of a logic node one above the other, such as `THEN` at a smaller `y` than `ELSE`.
* Several nodes on one outcome in the same column, one above another with a step of 160 in `y`.
* A node that several paths lead into, such as a shared Output Endpoint, one column to the right of the rightmost node that leads into it.

## Check before sending

The assistant checks its own output against this list before it returns it. Each item names the violation that the write returns when the item fails:

* [ ] At most 64 nodes and 96 transitions (`NODES_PER_FLOW_EXCEEDED`, `EDGES_PER_FLOW_EXCEEDED`), and every node name at most 255 characters (`NODE_NAME_TOO_LONG`)
* [ ] At least one `DATA_SOURCE`, and no transition leads into one (`MISSING_DATA_SOURCE`, `DATA_SOURCE_HAS_INCOMING_EDGE`)
* [ ] Every device ID is one that the user confirmed, at most 25,000 across the data sources, and no device is in two data sources whose paths reach the same node (`UNKNOWN_DEVICE`, `DEVICES_PER_FLOW_EXCEEDED`, `OVERLAPPING_ENTRY_PATHS`)
* [ ] Every node is reachable from a data source (`UNREACHABLE_NODE`)
* [ ] No loop in the transitions (`CYCLE_DETECTED`)
* [ ] No two parallel paths lead into the same node (`SIMULTANEOUS_PATHS`)
* [ ] Every path that calculates something ends at an `OUTPUT_ENDPOINT`, and no two paths that are active for the same message end at different Output Endpoints (no violation: the values are lost without an error)
* [ ] Every transition that leaves a logic node is `THEN` or `ELSE`, and every other transition is `SIMPLE` (`EDGE_TYPE_MISMATCH`)
* [ ] Each attribute name appears once on any path through the flow. Only the `THEN` and `ELSE` paths of one logic node may repeat it (`DUPLICATE_ATTRIBUTE_IN_FLOW`)
* [ ] No attribute name is one of the reserved position names, such as `position_latitude` (`RESERVED_ATTRIBUTE_NAME`)
* [ ] At most 50 attributes in a node and 200 in the flow, counting every `expressionName` (`ATTRIBUTES_PER_NODE_EXCEEDED`, `ATTRIBUTES_PER_FLOW_EXCEEDED`)
* [ ] No node on a `THEN` or `ELSE` outcome fires for messages without the value, unless the user wants that: a condition with `!=` sends them to `THEN`, and most other conditions send them to `ELSE` (no violation)
* [ ] Every text field is printable ASCII (`NON_LATIN_INPUT`)
* [ ] Every expression parses and stays within its length (`INVALID_EXPRESSION`, `EXPRESSION_TOO_LONG`)
* [ ] Every Device Action node has at most 10 valid actions, and its `trackableNodeId`, if any, is unique in the flow (`ACTIONS_PER_NODE_EXCEEDED`, `INVALID_DEVICE_COMMAND`, `DUPLICATE_TRACKABLE_NODE_ID`)
* [ ] Every `deviceMapping` has at most 1,000 targets, no entry without targets, and one entry per source device (`DEVICE_MAPPINGS_PER_NODE_EXCEEDED`, `EMPTY_DEVICE_MAPPING_ENTRY`, single `VALIDATION_ERROR`)
* [ ] Every Device Action node is on an outcome of a logic node, unless the user asked for commands on every message (no violation: the node sends its commands for every message that reaches it)
* [ ] Every Webhook node has `url`, `body`, and `trackableNodeId` (single `VALIDATION_ERROR`), and its `trackableNodeId` is unique among the Device Action and Webhook nodes (`DUPLICATE_TRACKABLE_NODE_ID`)
* [ ] The flow has at most 10 Webhook nodes with at most 10 headers each, and every URL, header, and body follows the [rules for Webhook nodes](../node-types.md#rules-for-webhook-nodes) (`WEBHOOK_NODES_PER_FLOW_EXCEEDED`, `WEBHOOK_HEADERS_PER_NODE_EXCEEDED`, `INVALID_WEBHOOK_URL`, `INVALID_WEBHOOK_HEADER`, `INVALID_WEBHOOK_BODY`)
* [ ] Every Webhook node is on an outcome of a logic node, unless the user asked for a request on every message (no violation: the node sends a request for every message that reaches it)
* [ ] Every Webhook body is valid JSON once each placeholder is replaced, text placeholders are in quotation marks, and every attribute name in it is confirmed (no violation: the receiving system gets a broken body or empty values)
* [ ] No header value holds an invented secret. A stand-in such as `<your token>` is named in the answer for the user to replace (no violation)
* [ ] No output is switched twice on one path of a message. Only the `THEN` and `ELSE` paths of one logic node may each switch it (`DUPLICATE_SET_OUTPUT_ACTION`)
* [ ] Keys are unique, and every transition names existing keys (single `VALIDATION_ERROR` before validation)
* [ ] For an update: the stored `id` of every kept node, and every field that the [update rules](#output-format) say to send again (no violation: the field is removed)

Two checks compare the flow with data in Navixy: `ATTRIBUTE_ALREADY_PRODUCED` with your other flows, and `ATTRIBUTE_REPORTED_BY_DEVICE` with the attributes of your devices. Give the assistant the result of the `deviceAttributes` query for the devices of the flow, so that it can avoid both. The write still reports them, and they can appear on a later attempt, after the other violations are fixed.

## Example

The [Calculating values in several steps](calculating-values-in-several-steps.md) guide has a complete input that follows every rule on this page: one data source, a chain of two Initiate Attribute nodes, a logic node, and one Output Endpoint for both outcomes. Give it to the assistant as the model to follow.

For a flow with a Webhook node, the [IoT Logic node types](../node-types.md#a-flow-with-a-webhook-node) page has a complete input: a data source, a logic node, and on its `THEN` outcome a Webhook node next to the Output Endpoint that both outcomes lead to.

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
