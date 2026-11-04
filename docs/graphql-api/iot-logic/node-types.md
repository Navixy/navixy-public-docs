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

| Type in a write request | Object type in a read | Node in the flow editor | What the node does |
| --- | --- | --- | --- |
| `DATA_SOURCE` | `IotLogicFlowDataSourceNode` | Data Source | Receives the messages of the devices that you list in `deviceIds` |
| `INITIATE_ATTRIBUTES` | `IotLogicFlowInitiateAttributesNode` | Initiate Attribute | Calculates the attributes in `attributes` and adds them to the message |
| `LOGIC` | `IotLogicFlowLogicNode` | IF/THEN Logic | Checks `condition` and sends the message to the `THEN` or the `ELSE` transition |
| `OUTPUT_ENDPOINT` | `IotLogicFlowOutputEndpointNode` | Output Endpoint | Ends a path and sends the message, with the attributes that the flow calculated, to the Navixy platform |
| `ACTION` | `IotLogicFlowActionNode` | Device Action | Sends commands to devices, such as switching an output on |
| `WEBHOOK` | `IotLogicFlowWebhookNode` | Webhook | Sends an HTTP request to a system outside Navixy |

## Fields that every node has

Every node in the `nodes` list of a write has these fields, whatever its type:

| Field | Required | What it holds |
| --- | --- | --- |
| `key` | Yes | A name for the node, valid only inside this one request. Transitions refer to the node by this key. Keys must be unique in the request. |
| `id` | Only for a stored node | The ID of the stored node that this entry keeps. Leave it out for a new node, and Navixy creates the ID. |
| `type` | Yes | The node type, such as `DATA_SOURCE` or `LOGIC`. See [Node types at a glance](#node-types-at-a-glance). |
| `name` | Yes | The name that the flow editor shows. English letters, digits, underscores, dashes, and slashes, in words separated by spaces, at most 255 characters. No space at the start or end. |
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
* An attribute name uses lowercase letters and digits in groups joined by single underscores, such as `fuel_level_l`. A name has at most 48 characters, not counting the channel number. Navixy reserves these names for the position of a device: `position_latitude`, `position_longitude`, `position_altitude`, `position_speed`, `position_heading`, `position_accuracy`, `position_hdop`, `position_vdop`, `position_pdop`, `position_satellites`, `position_fix_type`, and `position_source`. When a message has a position, Navixy adds these attributes from it, whatever the device model, and an expression can read them. A value that the position doesn't have, such as the altitude, is left out. For example, `position_speed` is the speed in km/h. Other attribute names depend on the device model: see [Find the attribute names of your devices](guides/reading-flows-and-device-data.md#find-the-attribute-names-of-your-devices).
* An expression is printable ASCII on one line, at most 960 characters. It is written in the [Navixy IoT Logic Expression Language](https://app.gitbook.com/s/8Ti8OItwJGZAFzK8jsG6/navixy-iot-logic-expression-language/overview).
* A message can pass only one node that produces a given attribute. Two nodes on one path can't produce the same attribute, and neither can two parallel paths that leave one node. The `THEN` and `ELSE` paths of one logic node can each produce it, because a message takes only one of them. Another flow can't produce the same attribute for the same device either, and a flow can't produce an attribute that its devices already report.

An expression can read the earlier values of an attribute with `value()`, for example `value('fuel_level_l', 1, 'valid')` for the previous valid value. Navixy stores the last 12 values of each attribute of a device. See [Full syntax](https://app.gitbook.com/s/8Ti8OItwJGZAFzK8jsG6/navixy-iot-logic-expression-language/expression-syntax-reference#full-syntax-historical-and-advanced).

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
* `THEN` can lead both to a Device Action node and to an Output Endpoint node. The flow then sends the commands and keeps the attributes of the path, because a Device Action node calculates nothing.
* `THEN` and `ELSE` can lead into the same node, because a message never takes both.
* A missing value doesn't always make the condition false. `temperature > 8` is false for a message without `temperature`, but `position_speed != 60` is true for a message without `position_speed`. A Device Action node on the `THEN` outcome of `position_speed != 60` sends its commands for every message without `position_speed`. See [Null propagation](https://app.gitbook.com/s/8Ti8OItwJGZAFzK8jsG6/navixy-iot-logic-expression-language/expression-syntax-reference#null-propagation).

## Output Endpoint

**`type`: `OUTPUT_ENDPOINT`. Object type in a read: `IotLogicFlowOutputEndpointNode`.**

An Output Endpoint node ends a path and sends the message to the Navixy platform. The message has the values that the device sent and the attributes that the path calculated. The platform stores the calculated attributes in the history of the device, together with the values that the device sent.

Rules:

* A path that calculates attributes needs to end at an Output Endpoint node. On a path that ends at any other node, the calculated attributes are lost.
* A flow can have several Output Endpoint nodes, for example one after the `THEN` outcome and one after the `ELSE` outcome. When two paths are active for the same message, only one of their Output Endpoint nodes keeps its attributes. See [How a message moves through a flow](how-flows-work.md#how-a-message-moves-through-a-flow).

The node has no fields other than the fields that every node has.

## Device Action

**`type`: `ACTION`. Object type in a read: `IotLogicFlowActionNode`.**

A Device Action node sends commands to devices when a message reaches it. Put it on the `THEN` outcome of a logic node to react to a condition, such as switching on the cooling unit of a truck when the cargo is too warm. Without a logic node before it on the path, the node sends its commands on every message that reaches it.

| Field | Required | What it holds |
| --- | --- | --- |
| `actions` | No | The commands that the node sends, at most 10. Each action sets exactly one of two fields: `setOutput` switches a digital output of the device on or off, and `rawCommand` sends a text command. A node without actions sends nothing. |
| `trackableNodeId` | No | The name of a flow value that Navixy sets to `1` on each message that reaches the node and to `0` on every other message of the flow. The flow keeps the value in its history and doesn't send it to the Navixy platform. |
| `deviceMapping` | No | Entries that send the actions to other devices. Each entry has a `sourceDeviceId` and a list of `targetDeviceIds`. |

The following node switches output 1 of the device on:

```json
{
  "key": "cooling",
  "type": "ACTION",
  "name": "Switch on cooling",
  "actions": [{ "setOutput": { "output": 1, "enabled": true } }],
  "trackableNodeId": "cooling_switched_on"
}
```

An action is one of two types: `setOutput` has `output`, from 1 to 8, and `enabled`, which is `true` to switch the output on and `false` to switch it off. `rawCommand` has `text`, at most 512 characters and not blank. Navixy sends the text unchanged and as ASCII, so use only printable ASCII characters. Message values can't be inserted into the text.

Two `setOutput` actions for the same output can't be on one path of a message, in one node or in two nodes (`DUPLICATE_SET_OUTPUT_ACTION`). The `THEN` and `ELSE` paths of one logic node can each have one.

In a read, `actions` is a list of the union `IotLogicDeviceCommandAction`. Read its fields with an inline fragment for each type:

```graphql
... on IotLogicFlowActionNode {
  actions {
    ... on IotLogicSetOutputAction { output enabled }
    ... on IotLogicRawCommandAction { text }
  }
  trackableNodeId
  deviceMapping {
    source { id title }
    targets { id title }
  }
}
```

### Where the commands go

Without a mapping, the actions go to the device that sent the message. A mapping works for each source device separately: a message of a source device sends the actions to each of its targets instead of to the source. The source receives nothing unless it is also one of its targets. A device without an entry receives its own commands.

For example, a mapping can send the commands of a trailer sensor to the tractor unit that pulls the trailer:

```json
"deviceMapping": [
  {
    "sourceDeviceId": "019dbec6-d380-8000-801f-44455643e216",
    "targetDeviceIds": ["019dbec6-d380-8000-801f-44455643e217"]
  }
]
```

### How Navixy sends the commands

* The node sends its commands on every message that reaches it, not only when a value changes. While a condition stays true, a device that reports every 10 seconds receives a command every 10 seconds.
* A message that answers a command doesn't trigger the node. Neither does a message that arrives late or that Navixy processes a second time.
* Each action sends one command to each target device. One message sends at most 100 commands, counted across all flows, and Navixy drops the rest.
* Navixy adds the commands to the command queue of the device. A `setOutput` command waits up to 3 days for an offline device, and a `rawCommand` waits up to 1 hour. At most 100 commands wait for one device, and Navixy refuses and loses a newer one. A command that is the same as one already waiting isn't added again.
* The queue sends `setOutput` commands first, so a `setOutput` command can reach the device before an earlier `rawCommand` that is still waiting.
* Delivery is at most once: Navixy doesn't send a lost command again. The API doesn't return the delivery status of a command.
* Only Teltonika device models receive commands. Navixy sends `setOutput` as the Teltonika `setdigout` command, and `rawCommand` text over GPRS. For a device of any other model, or a device without an IMEI, Navixy sends nothing and reports no error.

### Rules

* A Device Action node doesn't keep the attributes of the path: on a path that ends at a Device Action node, the attributes that the path calculated are lost. To keep them, lead the same outcome to an Output Endpoint node as well.
* A node can have at most 10 actions. More fail with `ACTIONS_PER_NODE_EXCEEDED`. An output number outside 1 to 8, or a text that is blank or longer than 512 characters, fails with `INVALID_DEVICE_COMMAND`.
* `trackableNodeId` follows the naming rule of attributes: lowercase letters and digits in groups joined by single underscores, at most 48 characters, not counting a final channel number. Two Device Action or [Webhook](#webhook) nodes of one flow can't have the same name (`DUPLICATE_TRACKABLE_NODE_ID`), and a device of the flow can't report an attribute with this name (`ATTRIBUTE_REPORTED_BY_DEVICE`).
* In an update, send `actions` and `trackableNodeId` every time. A node without them has none, so leaving them out removes the stored values.
* In an update, leave out `deviceMapping` to keep the current mapping. Send `deviceMapping: []` to remove it, or a list to replace it. `null` isn't accepted. `[]` and a new list change only the entries and targets that your roles let you read, and the others stay. A new node without `deviceMapping` has no mapping.
* A node can have at most 1,000 targets, counted across its entries (`DEVICE_MAPPINGS_PER_NODE_EXCEEDED`). An entry without targets fails with `EMPTY_DEVICE_MAPPING_ENTRY`. A source device can have only one entry in a node, and a second one fails with a single `VALIDATION_ERROR`.
* Navixy checks only the devices that a write adds to the mapping. A device that doesn't exist, that belongs to another workspace, or that your roles don't let you read fails the write with `UNKNOWN_DEVICE`.
* A read returns only the entries and targets that still exist and that your roles let you read, and leaves out an entry with no such target. So a mapping that you read and send back unchanged always saves.

### Differences from the flow editor

A mapping in the API can name any device of your workspace that your roles let you read, as a source or a target. The flow editor offers only the devices of the data sources of the flow.

The flow editor also fills `trackableNodeId` on its own, with `action_` and 8 hexadecimal characters, and doesn't let you change it.

## Webhook

**`type`: `WEBHOOK`. Object type in a read: `IotLogicFlowWebhookNode`.**

A Webhook node sends an HTTP `POST` request to a system outside Navixy when a message reaches the node. The system can be a messaging service, a ticketing system, or your own app. You write the request body, and Navixy replaces the [placeholders](#placeholders) in it with values of the message. Put the node on the `THEN` outcome of a logic node to send the request only when a condition is true. Without a logic node before it on the path, the node sends a request for every message that reaches it.

| Field | Required | What it holds |
| --- | --- | --- |
| `url` | Yes | The address that the node sends the request to, at most 255 characters. It starts with `http://` or `https://`. The only placeholder that it accepts is `{{device_id}}`. |
| `headers` | No | The HTTP headers of the request, at most 10. Each header has a `key`, which is the header name, and a `value`. The only placeholder that a value accepts is `{{device_id}}`. |
| `body` | Yes | The body of the request, at most 4,000 characters. It can have [placeholders](#placeholders). Send `""` for a request with an empty body. |
| `trackableNodeId` | Yes | The name of a flow value that Navixy sets to `1` on each message that reaches the node and to `0` on every other message of the flow. `1` means that the message reached the node, not that the request succeeded. Choose any name that follows the naming rule of attributes and that no other Device Action or Webhook node of the flow uses, such as `dispatcher_notified`. The flow keeps the value in its history and doesn't send it to the Navixy platform. |

The following node sends the device ID, the device title, and the temperature to your system:

```json
{
  "key": "notify",
  "type": "WEBHOOK",
  "name": "Notify dispatcher",
  "url": "https://example.com/hooks/cold-chain",
  "headers": [{ "key": "Authorization", "value": "Bearer <your token>" }],
  "body": "{\"device\": \"{{device_id}}\", \"name\": \"{{device_label}}\", \"temperature\": {{temperature}}}",
  "trackableNodeId": "dispatcher_notified"
}
```

`body` is a JSON string, so its quotation marks are escaped. Without the escapes, the body is this JSON template:

```
{"device": "{{device_id}}", "name": "{{device_label}}", "temperature": {{temperature}}}
```

For a message with a temperature of 9.5, your system receives a request like this one:

```http
POST /hooks/cold-chain HTTP/1.1
Host: example.com
Authorization: Bearer <your token>
Content-Type: application/json
X-Navixy-Webhook: <the ID of the flow>

{"device": "019dbec6-d380-8000-801f-44455643e216", "name": "Truck 12", "temperature": 9.5}
```

The body has `{{temperature}}` without quotation marks, so the receiving system gets a number. A message without `temperature` gives a body that isn't valid JSON, so put the node after a logic node that checks `temperature`.

### A flow with a Webhook node

The following `flowCreate` variables create a flow that notifies a dispatcher when a cargo is too warm. Every message reaches the Output Endpoint, by the `THEN` or the `ELSE` transition, so the flow keeps its attributes. Only a message with `temperature > 8` also reaches the Webhook node:

```json
{
  "input": {
    "title": "Cold chain alert",
    "nodes": [
      {
        "key": "src",
        "type": "DATA_SOURCE",
        "name": "Refrigerated trucks",
        "deviceIds": ["019dbec6-d380-8000-801f-44455643e216"]
      },
      {
        "key": "check",
        "type": "LOGIC",
        "name": "Too warm",
        "condition": "temperature > 8"
      },
      {
        "key": "notify",
        "type": "WEBHOOK",
        "name": "Notify dispatcher",
        "url": "https://example.com/hooks/cold-chain",
        "headers": [{ "key": "Authorization", "value": "Bearer <your token>" }],
        "body": "{\"device\": \"{{device_id}}\", \"name\": \"{{device_label}}\", \"temperature\": {{temperature}}}",
        "trackableNodeId": "dispatcher_notified"
      },
      { "key": "out", "type": "OUTPUT_ENDPOINT", "name": "Default output" }
    ],
    "transitions": [
      { "from": "src", "to": "check", "type": "SIMPLE" },
      { "from": "check", "to": "notify", "type": "THEN" },
      { "from": "check", "to": "out", "type": "THEN" },
      { "from": "check", "to": "out", "type": "ELSE" }
    ]
  }
}
```

The flow is created disabled. Enable it to start sending requests. See [Enable the flow](guides/creating-and-updating-flows.md#enable-the-flow).

### Read a Webhook node

Read the fields of the node with an inline fragment. Each item of `headers` is an `IotLogicFlowWebhookHeader` with a `key` and a `value`:

```graphql
... on IotLogicFlowWebhookNode {
  url
  headers { key value }
  body
  trackableNodeId
}
```

`trackableNodeId` is `String!` on a Webhook node and `String` on a Device Action node. To read both node types in one query, give one of the two fields an alias, such as `webhookTrackableNodeId: trackableNodeId`. Without it, the query fails with `VALIDATION_ERROR`, because GraphQL doesn't accept one response field with two types.

An update sends the whole node again, so read all four fields before you change a Webhook node. A read returns each header as you sent it, including a secret such as an access token. Anyone whose roles let them read the flow can read the value.

### Placeholders

A placeholder is a name in double braces, such as `{{temperature}}`. When Navixy sends the request, it replaces each placeholder in the body with a value of the message:

| Placeholder | Value |
| --- | --- |
| `{{device_id}}` | The ID of the device that sent the message |
| `{{device_label}}` | The title of the device |
| `{{device_imei}}` | The IMEI of the device. For a device with several IMEIs, Navixy uses the IMEI that was added first. |
| `{{<attribute name>}}` | The value of the attribute. For an attribute with a channel, include the channel number in the name, such as `{{lls_level_0}}`. The value comes from the attributes that the path calculated, or from the values that the device sent. |

Placeholders follow these rules:

* A placeholder name follows the naming rule of attributes: lowercase letters and digits in groups joined by single underscores. Text in double braces that breaks the rule, such as `{{Speed}}` or `{{ temperature }}` with spaces, isn't a placeholder, and Navixy sends it as you wrote it.
* Navixy replaces a placeholder with empty text when the message doesn't have its value. A placeholder whose name is longer than 48 characters also becomes empty text, and so does one whose channel number starts with 0, such as `{{temp_01}}`.
* `{{device_label}}` and `{{device_imei}}` become empty text when Navixy can't look up the device. `{{device_imei}}` also becomes empty text for a device without an IMEI.
* The `trackableNodeId` of a node isn't a value of the message, so a placeholder with its name becomes empty text.
* Navixy doesn't check the attribute names in the body when you save the flow. A misspelled name gives empty text in every request.
* Navixy inserts each value as the content of a JSON string, without the quotation marks around it. Navixy escapes characters such as `"` and `\`. Put the quotation marks in the body yourself, as in `"name": "{{device_label}}"`.
* You can leave out the quotation marks for a number or for `true` or `false`, as in `"temperature": {{temperature}}`. Then a message without the value gives a body that isn't valid JSON.
* Navixy writes a number without trailing zeros, so `4.0` becomes `4`.
* Navixy writes a time in ISO 8601, such as `2026-10-29T10:15:30Z`, a duration in ISO 8601, such as `PT5M`, and a byte value as hexadecimal text. Put these placeholders in quotation marks. A value that is a structure becomes empty text.
* Navixy inserts a list of values as a JSON array of strings, numbers included, so write its placeholder without quotation marks.
* In `url` and in header values, `{{device_id}}` is the only placeholder. In `url`, Navixy encodes it for a URL.

### How Navixy sends the request

* The node sends one request for every message that reaches it, not only when a value changes. While a condition stays true, a device that reports every 10 seconds triggers a request every 10 seconds.
* A message that answers a command triggers the node too. A message that arrives late, or that Navixy processes a second time, doesn't.
* Navixy sends the request after it stores the values of the message.
* Navixy sends the body in UTF-8 and doesn't check that it is valid JSON.
* The request has the headers of the node, the headers that the HTTP library adds, such as `Host` and `Content-Length`, and the header `X-Navixy-Webhook`, whose value is the ID of the flow. Without a `Content-Type` header in the node, the request has `Content-Type: application/json`, also when the body is empty. When the node has two headers with the same name, the request has both.
* Navixy sends requests in parallel, so your system can receive them in a different order than the messages. At most 20 requests go to one host at the same time. A request that waits more than 5 seconds for a free connection fails.
* Navixy waits at most 5 seconds to connect, at most 5 seconds for the response to start, at most 5 seconds between two parts of the response, and at most 10 seconds for the whole request. Then it cancels the request.
* Navixy ignores the response, including its status code, and doesn't follow a redirect. Delivery is at most once: Navixy doesn't send a failed request again. The API doesn't return the response or the status of a request.
* When the host name of `url` resolves to at least one address that Navixy refuses in `url` (see the [rules](#rules-for-webhook-nodes)), Navixy doesn't send the request and reports no error.
* One message sends at most 100 requests, counted across all flows, and Navixy drops the rest.
* At most 500 requests of one workspace can wait or be in progress at the same time. When there are 500, Navixy drops each new request.
* When the body is larger than 64 KB after Navixy inserts the values, Navixy doesn't send the request.

### Rules for Webhook nodes

* A Webhook node doesn't keep the attributes of the path: on a path that ends at a Webhook node, the attributes that the path calculated are lost. To keep them, lead the same outcome to an Output Endpoint node as well.
* A write needs `url`, `body`, and `trackableNodeId` on every Webhook node. A node without one of them fails the write with a single `VALIDATION_ERROR` that names the field, such as `input.nodes[2].body`.
* A flow can have at most 10 Webhook nodes (`WEBHOOK_NODES_PER_FLOW_EXCEEDED`), and a node can have at most 10 headers (`WEBHOOK_HEADERS_PER_NODE_EXCEEDED`).
* `url` starts with `http://` or `https://` in lowercase, has at most 255 characters, and has no spaces. Navixy removes spaces at the start and the end. Its only placeholder is `{{device_id}}`. A `url` that breaks one of these rules, or that isn't a valid URL with a host, fails with `INVALID_WEBHOOK_URL`.
* `url` can't name `localhost` or a host name that ends in `.localhost`. It can't name an IP address of these kinds either, and a `url` that does fails with `INVALID_WEBHOOK_URL`:
  * loopback, such as `127.0.0.1` or `::1`
  * private: `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, and the IPv6 range `fc00::/7`
  * link-local, such as `169.254.0.1` or `fe80::1`
  * multicast, broadcast (`255.255.255.255`), and `0.0.0.0/8`
  * carrier-grade NAT: `100.64.0.0/10`
  * an IPv6 address that contains one of these IPv4 addresses
* A header name has at most 255 characters and is an HTTP token, as [RFC 9110](https://www.rfc-editor.org/rfc/rfc9110.html#name-tokens) defines it: English letters, digits, and the characters ``!#$%&'*+-.^_`|~``. Navixy removes spaces at the start and the end of a name. A header value has at most 255 characters, and its only placeholder is `{{device_id}}`. A header that breaks these rules fails with `INVALID_WEBHOOK_HEADER`.
* Navixy sets these headers itself when it sends the request, so a node can't have them: `Content-Length`, `Transfer-Encoding`, `Host`, `Connection`, `Keep-Alive`, `Proxy-Connection`, `TE`, `Trailer`, `Upgrade`, `Expect`, and `X-Navixy-Webhook`. A node with one of these headers fails with `INVALID_WEBHOOK_HEADER`. Header names aren't case-sensitive, so `host` fails too.
* A node can have one `Content-Type` header. Its value is `application/json` or another `application` media type that ends in `+json`, such as `application/problem+json`. A `charset` parameter can only be `utf-8`. Any other value, or a second `Content-Type` header, fails with `INVALID_WEBHOOK_HEADER`.
* A body longer than 4,000 characters fails with `INVALID_WEBHOOK_BODY`.
* `url` and header values have only printable ASCII characters. A character outside printable ASCII fails with `NON_LATIN_INPUT`. Navixy doesn't check the characters of the body.
* `trackableNodeId` follows the naming rule of attributes: lowercase letters and digits in groups joined by single underscores, at most 48 characters, not counting a final channel number. A channel number can't start with 0, so `alert_01` isn't valid. A name that breaks the rule fails with a single `VALIDATION_ERROR` on `input.nodes[2].trackableNodeId`. Two Device Action or Webhook nodes of one flow can't have the same name (`DUPLICATE_TRACKABLE_NODE_ID`), and a device of the flow can't report an attribute with this name (`ATTRIBUTE_REPORTED_BY_DEVICE`).
* In an update, send `url`, `headers`, `body`, and `trackableNodeId` every time. A node without `headers` has none, so leaving them out removes the stored headers.

## See also

* [How IoT Logic flows work](how-flows-work.md): Learn how a message moves through a flow and where calculated attributes appear.
* [Creating and updating flows](guides/creating-and-updating-flows.md): Build a flow from these node types, step by step.
* [Flow validation errors](flow-validation-errors.md): Fix the violations that the rules on this page produce.
* [Flows](api-reference/flows.md): See every field of every node type.
