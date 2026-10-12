---
description: >-
  Reference for IoT Logic flows: the flow and its node types, transitions, the
  inputs that write a whole graph, and the queries and mutations for flows.
tags:
  - reference
  - beta
---

# Flows

This page describes IoT Logic flows: the graph of nodes and transitions that processes the messages of the devices in a flow, the queries that read flows and the attribute names of devices, and the mutations that create, update, and delete flows.

## Queries

### flow

Reads one flow. Returns `null` when your workspace has no flow with this ID, including a deleted flow.

```graphql
flow(id: ID!): IotLogicFlow
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The flow to read. |

**Output types:**

<details>

<summary>IotLogicFlow</summary>

A graph of nodes and transitions that processes the messages of the devices attached to its data sources, and adds the attributes that it calculates.

**Implements:** [Node](../../core-api-reference/common.md#node), [Titled](../../core-api-reference/common.md#titled), [Versioned](../../core-api-reference/common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. It increases by one with each update. `flowUpdate` and `flowDelete` require the current version, so a write fails if someone else changed the flow after you read it. |
| `title` | `String!` | The human-readable display name. |
| `description` | `String` | Notes about the flow. Printable ASCII and line feeds (`\n`). |
| `isEnabled` | `Boolean!` | Whether the flow processes the messages of its devices. A new flow is disabled. |
| `nodes` | [[IotLogicFlowNode](#iotlogicflownode)!]! | The nodes of the graph, in the order of the last write. |
| `transitions` | [[IotLogicFlowTransition](#iotlogicflowtransition)!]! | The transitions of the graph, in the order of the last write. |
| `deviceCount` | `Int!` | The number of devices in the data sources of the flow that your roles let you read. A device in two data sources counts twice. In rare cases, a flow keeps a device that no longer exists, and the device still counts until the data source no longer lists it. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the flow was created. |
| `updatedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the flow was last changed. |

</details>

---

### flows

Lists the flows of your workspace.

```graphql
flows(
    filter: IotLogicFlowFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: IotLogicFlowOrder = { field: TITLE, direction: ASC }
  ): IotLogicFlowConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `IotLogicFlowFilter` | Filtering options for the returned flows. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `IotLogicFlowOrder` | The ordering options for the returned flows. |

**Input types:**

<details>

<summary>IotLogicFlowFilter</summary>

Filtering options for flows. A flow must match every condition that you set.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Only flows whose title contains this text. The match ignores letter case. |
| `isEnabled` | `Boolean` | Only enabled, or only disabled, flows. |
| `deviceId` | `ID` | Only flows that have this device in one of their data sources. No flows for a device that your roles don't let you read. |

</details>

<details>

<summary>IotLogicFlowOrder</summary>

Ordering options for flows.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [IotLogicFlowOrderField](#iotlogicfloworderfield) | The standard field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>IotLogicFlowConnection</summary>

A paginated list of IotLogicFlow items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[IotLogicFlowEdge](#iotlogicflowedge)!]! | A list of edges. |
| `nodes` | [[IotLogicFlow](#iotlogicflow)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### deviceFlowLinks

Lists which devices enter which flows in your workspace, with one link for each device in each data source. A device that is in no flow isn't listed. Use it before you delete devices: while a flow lists a device, `deviceDelete` returns `IN_USE`. See [Finding the flows of a device](../guides/reading-flows-and-device-data.md#finding-the-flows-of-a-device).

```graphql
deviceFlowLinks(
    filter: IotLogicDeviceFlowLinkFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: IotLogicDeviceFlowLinkOrder = { field: CREATED_AT, direction: DESC }
  ): IotLogicDeviceFlowLinkConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `IotLogicDeviceFlowLinkFilter` | Filtering options for the returned links. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `IotLogicDeviceFlowLinkOrder` | The ordering options for the returned links. |

**Input types:**

<details>

<summary>IotLogicDeviceFlowLinkFilter</summary>

Filtering options for device links. A link must match every condition that you set.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `flowId` | `ID` | Only the links of this flow. |
| `deviceIds` | `[ID!]` | Only the links of these devices, at most 1,000 IDs. An empty list returns no links. Leave the field out to list the links of every device. |

</details>

<details>

<summary>IotLogicDeviceFlowLinkOrder</summary>

Ordering options for device links.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [IotLogicDeviceFlowLinkOrderField](#iotlogicdeviceflowlinkorderfield) | The standard field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>IotLogicDeviceFlowLinkConnection</summary>

A paginated list of IotLogicDeviceFlowLink items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[IotLogicDeviceFlowLinkEdge](#iotlogicdeviceflowlinkedge)!]! | A list of edges. |
| `nodes` | [[IotLogicDeviceFlowLink](#iotlogicdeviceflowlink)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### flowDebugValues

Reads the recent values of up to 10 devices of one flow, as the messages leave the flow. In each message, a value that the flow calculated for an attribute replaces the value from the device, and the value from the device stays where the flow calculated none. The result has one entry per device, in the order of `deviceIds`. A device that isn't in the flow, or that your roles don't let you read, is left out. A device of the flow with no values yet has empty maps. A disabled flow still returns the values recorded while it was enabled. Returns `null` when your workspace has no flow with this ID. See [Checking what a flow calculated](../guides/reading-flows-and-device-data.md#checking-what-a-flow-calculated).

```graphql
flowDebugValues(
    flowId: ID!
    deviceIds: [ID!]!
  ): [IotLogicFlowDebugValues!]
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `flowId` | `ID!` | The flow to read. |
| `deviceIds` | `[ID!]!` | The devices to read: at least 1 and at most 10 different IDs. |

**Output types:**

<details>

<summary>IotLogicFlowDebugValues</summary>

The recent values of one device. Both maps use the attribute name as the key, with the channel number if it has one, such as `lls_level_0`. Each attribute has at most 12 entries, newest first. Each entry is `{ "value": number | boolean | string | null, "messageTime": DateTime, "serverTime": DateTime }`. `messageTime` is when the device created the message, and `serverTime` is when Navixy received it.

Integers larger than 2^53 − 1 or smaller than −(2^53 − 1), times, and byte arrays are returned as strings. Durations are returned in milliseconds. Attributes that hold arrays or objects aren't returned.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceId` | `ID!` | The ID of the device. |
| `allFields` | [JSON](../../core-api-reference/common.md#json)! | One entry per recent message of the device. A message without the attribute, or with an empty value for it, has an entry with `value: null` and the `messageTime` of that message. For a message without the attribute, `serverTime` has the same value as `messageTime`, because Navixy doesn't record when such a message arrived. |
| `nonNullFields` | [JSON](../../core-api-reference/common.md#json)! | The recent values of the attribute that aren't `null`. They can be older than the messages in `allFields`, so this map isn't part of `allFields`. An attribute with no such value isn't in the map. |

</details>

---

## Mutations

### flowCreate

Creates a flow, which starts disabled. If the graph breaks a validation rule, the mutation fails and returns every violation at once. See [Flow validation errors](../flow-validation-errors.md).

```graphql
flowCreate(input: IotLogicFlowCreateInput!): IotLogicFlowPayload!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `IotLogicFlowCreateInput!` | The input fields for creating the flow. |

**Input types:**

<details>

<summary>IotLogicFlowCreateInput</summary>

Input for creating a flow. The flow is created disabled.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `title` | `String!` | Printable ASCII, not blank, at most 255 characters. |
| `description` | `String` | Printable ASCII and line feeds (`\n`), at most 4,000 characters. Spaces at the start and the end are kept. |
| `nodes` | [[IotLogicFlowNodeInput](#iotlogicflownodeinput)!]! | At most 64. |
| `transitions` | [[IotLogicFlowTransitionInput](#iotlogicflowtransitioninput)!]! | At most 96. |

</details>

<details>

<summary>IotLogicFlowNodeInput</summary>

A node in a create or update request. `key` names the node inside this one request, and transitions refer to the node by its key. `id` is the ID of the stored node that this entry keeps. Leave out `id` for a new node.

Each field below that names a node type belongs to that type only. The other types ignore it without an error: `deviceIds` to `DATA_SOURCE`, `condition` and `expressionName` to `LOGIC`, and `attributes` to `INITIATE_ATTRIBUTES`. `OUTPUT_ENDPOINT`, `ACTION`, and `WEBHOOK` nodes have no fields of their own.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `key` | `String!` | A name for the node, valid only inside this request. Each node in the request needs a different key. |
| `id` | `ID` | The ID of the stored node that this entry keeps. Leave it out for a new node. |
| `type` | [IotLogicFlowNodeType](#iotlogicflownodetype)! | The node type. |
| `name` | `String!` | English letters, digits, underscores, dashes, and slashes, in words separated by spaces. No space at the start or end. Tabs and line breaks aren't accepted. |
| `description` | `String` | Printable ASCII and line feeds (`\n`), at most 4,000 characters. Spaces at the start and the end are kept. |
| `position` | [IotLogicFlowNodePositionInput](#iotlogicflownodepositioninput) | The coordinates where the flow editor draws the node. |
| `deviceIds` | `[ID!]` | `DATA_SOURCE` only. The devices whose messages enter the flow at this node. In an update, leave it out to keep the current devices, send `[]` to remove all of them, or send a list to replace them. `[]` and a new list change only the devices that your roles let you read, and the other devices stay attached. A new node without it has no devices. The data sources of a flow can have at most 25,000 devices together, and a device in two data sources counts twice. A device that your roles don't let you read fails with `UNKNOWN_DEVICE`, as a device that doesn't exist does. Validation counts every attached device, including the ones that you can't read, toward the limit and toward attribute conflicts. |
| `condition` | `String` | `LOGIC` only. The condition to check, in the Navixy IoT Logic Expression Language. Printable ASCII on one line, at most 480 characters. |
| `expressionName` | `String` | `LOGIC` only, optional. The attribute that stores the result of the check. Lowercase letters and digits in groups joined by single underscores, such as `is_too_warm` (`^[a-z0-9]+(_[a-z0-9]+)*$`). A final group of digits, such as `_2`, gives the channel number. At most 48 characters, not counting the channel number. Navixy reserves 12 names for the position of a device, such as `position_latitude`. See [Initiate Attribute](../node-types.md#initiate-attribute). |
| `attributes` | [[IotLogicFlowAttributeAssignmentInput](#iotlogicflowattributeassignmentinput)!] | `INITIATE_ATTRIBUTES` only. The attributes that the node calculates. At most 50 per node, and at most 200 per flow, counting the `expressionName` of every logic node. |

</details>

<details>

<summary>IotLogicFlowNodePositionInput</summary>

The coordinates where the flow editor draws the node.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `x` | `Int!` | The horizontal coordinate. |
| `y` | `Int!` | The vertical coordinate. |

</details>

<details>

<summary>IotLogicFlowAttributeAssignmentInput</summary>

One attribute that an Initiate Attribute node calculates: its name and the expression for its value.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `name` | `String!` | The name of the attribute. Lowercase letters and digits in groups joined by single underscores, such as `fuel_level_l` (`^[a-z0-9]+(_[a-z0-9]+)*$`). A final group of digits, such as `_2`, gives the channel number. At most 48 characters, not counting the channel number. Navixy reserves 12 names for the position of a device, such as `position_latitude`. See [Initiate Attribute](../node-types.md#initiate-attribute). |
| `expression` | `String!` | The expression that calculates the value, in the Navixy IoT Logic Expression Language. Printable ASCII on one line, at most 960 characters. |

</details>

<details>

<summary>IotLogicFlowTransitionInput</summary>

A transition in a create or update request. `from` and `to` are node keys from the same request, not node IDs.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `from` | `String!` | The `key` of the node the transition leaves. |
| `to` | `String!` | The `key` of the node the transition enters. |
| `type` | [IotLogicFlowTransitionType](#iotlogicflowtransitiontype)! | The outcome that the transition follows: `THEN` or `ELSE` after a logic node, `SIMPLE` after every other node. |

</details>

**Output types:**

<details>

<summary>IotLogicFlowPayload</summary>

The flow as it was saved.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `flow` | [IotLogicFlow](#iotlogicflow)! | The flow after the write. |

</details>

<details>

<summary>IotLogicFlow (entity)</summary>

A graph of nodes and transitions that processes the messages of the devices attached to its data sources, and adds the attributes that it calculates.

**Implements:** [Node](../../core-api-reference/common.md#node), [Titled](../../core-api-reference/common.md#titled), [Versioned](../../core-api-reference/common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. It increases by one with each update. `flowUpdate` and `flowDelete` require the current version, so a write fails if someone else changed the flow after you read it. |
| `title` | `String!` | The human-readable display name. |
| `description` | `String` | Notes about the flow. Printable ASCII and line feeds (`\n`). |
| `isEnabled` | `Boolean!` | Whether the flow processes the messages of its devices. A new flow is disabled. |
| `nodes` | [[IotLogicFlowNode](#iotlogicflownode)!]! | The nodes of the graph, in the order of the last write. |
| `transitions` | [[IotLogicFlowTransition](#iotlogicflowtransition)!]! | The transitions of the graph, in the order of the last write. |
| `deviceCount` | `Int!` | The number of devices in the data sources of the flow that your roles let you read. A device in two data sources counts twice. In rare cases, a flow keeps a device that no longer exists, and the device still counts until the data source no longer lists it. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the flow was created. |
| `updatedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the flow was last changed. |

</details>

---

### flowUpdate

Updates a flow. If the flow after the update breaks a validation rule, the mutation fails, changes nothing, and returns every violation at once. See [Flow validation errors](../flow-validation-errors.md).

```graphql
flowUpdate(input: IotLogicFlowUpdateInput!): IotLogicFlowPayload!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `IotLogicFlowUpdateInput!` | The input fields for updating the flow. |

**Input types:**

<details>

<summary>IotLogicFlowUpdateInput</summary>

Input for updating a flow. `nodes` and `transitions` are the whole new graph, not a list of changes. A node that the list leaves out is removed, together with its devices. Send both lists together. A field that you leave out keeps its stored value.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The flow to update. |
| `version` | `Int!` | The current version for optimistic locking. |
| `title` | `String` | Printable ASCII, not blank, at most 255 characters. |
| `description` | `String` | Printable ASCII and line feeds (`\n`), at most 4,000 characters. Spaces at the start and the end are kept. |
| `nodes` | [[IotLogicFlowNodeInput](#iotlogicflownodeinput)!] | At most 64. |
| `transitions` | [[IotLogicFlowTransitionInput](#iotlogicflowtransitioninput)!] | At most 96. |
| `isEnabled` | `Boolean` | `true` enables the flow and validates the whole graph again. `false` disables the flow. An update that only sets `isEnabled: false` validates nothing. |

</details>

<details>

<summary>IotLogicFlowNodeInput</summary>

A node in a create or update request. `key` names the node inside this one request, and transitions refer to the node by its key. `id` is the ID of the stored node that this entry keeps. Leave out `id` for a new node.

Each field below that names a node type belongs to that type only. The other types ignore it without an error: `deviceIds` to `DATA_SOURCE`, `condition` and `expressionName` to `LOGIC`, and `attributes` to `INITIATE_ATTRIBUTES`. `OUTPUT_ENDPOINT`, `ACTION`, and `WEBHOOK` nodes have no fields of their own.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `key` | `String!` | A name for the node, valid only inside this request. Each node in the request needs a different key. |
| `id` | `ID` | The ID of the stored node that this entry keeps. Leave it out for a new node. |
| `type` | [IotLogicFlowNodeType](#iotlogicflownodetype)! | The node type. |
| `name` | `String!` | English letters, digits, underscores, dashes, and slashes, in words separated by spaces. No space at the start or end. Tabs and line breaks aren't accepted. |
| `description` | `String` | Printable ASCII and line feeds (`\n`), at most 4,000 characters. Spaces at the start and the end are kept. |
| `position` | [IotLogicFlowNodePositionInput](#iotlogicflownodepositioninput) | The coordinates where the flow editor draws the node. |
| `deviceIds` | `[ID!]` | `DATA_SOURCE` only. The devices whose messages enter the flow at this node. In an update, leave it out to keep the current devices, send `[]` to remove all of them, or send a list to replace them. `[]` and a new list change only the devices that your roles let you read, and the other devices stay attached. A new node without it has no devices. The data sources of a flow can have at most 25,000 devices together, and a device in two data sources counts twice. A device that your roles don't let you read fails with `UNKNOWN_DEVICE`, as a device that doesn't exist does. Validation counts every attached device, including the ones that you can't read, toward the limit and toward attribute conflicts. |
| `condition` | `String` | `LOGIC` only. The condition to check, in the Navixy IoT Logic Expression Language. Printable ASCII on one line, at most 480 characters. |
| `expressionName` | `String` | `LOGIC` only, optional. The attribute that stores the result of the check. Lowercase letters and digits in groups joined by single underscores, such as `is_too_warm` (`^[a-z0-9]+(_[a-z0-9]+)*$`). A final group of digits, such as `_2`, gives the channel number. At most 48 characters, not counting the channel number. Navixy reserves 12 names for the position of a device, such as `position_latitude`. See [Initiate Attribute](../node-types.md#initiate-attribute). |
| `attributes` | [[IotLogicFlowAttributeAssignmentInput](#iotlogicflowattributeassignmentinput)!] | `INITIATE_ATTRIBUTES` only. The attributes that the node calculates. At most 50 per node, and at most 200 per flow, counting the `expressionName` of every logic node. |

</details>

<details>

<summary>IotLogicFlowNodePositionInput</summary>

The coordinates where the flow editor draws the node.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `x` | `Int!` | The horizontal coordinate. |
| `y` | `Int!` | The vertical coordinate. |

</details>

<details>

<summary>IotLogicFlowAttributeAssignmentInput</summary>

One attribute that an Initiate Attribute node calculates: its name and the expression for its value.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `name` | `String!` | The name of the attribute. Lowercase letters and digits in groups joined by single underscores, such as `fuel_level_l` (`^[a-z0-9]+(_[a-z0-9]+)*$`). A final group of digits, such as `_2`, gives the channel number. At most 48 characters, not counting the channel number. Navixy reserves 12 names for the position of a device, such as `position_latitude`. See [Initiate Attribute](../node-types.md#initiate-attribute). |
| `expression` | `String!` | The expression that calculates the value, in the Navixy IoT Logic Expression Language. Printable ASCII on one line, at most 960 characters. |

</details>

<details>

<summary>IotLogicFlowTransitionInput</summary>

A transition in a create or update request. `from` and `to` are node keys from the same request, not node IDs.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `from` | `String!` | The `key` of the node the transition leaves. |
| `to` | `String!` | The `key` of the node the transition enters. |
| `type` | [IotLogicFlowTransitionType](#iotlogicflowtransitiontype)! | The outcome that the transition follows: `THEN` or `ELSE` after a logic node, `SIMPLE` after every other node. |

</details>

**Output types:**

<details>

<summary>IotLogicFlowPayload</summary>

The flow as it was saved.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `flow` | [IotLogicFlow](#iotlogicflow)! | The flow after the write. |

</details>

<details>

<summary>IotLogicFlow (entity)</summary>

A graph of nodes and transitions that processes the messages of the devices attached to its data sources, and adds the attributes that it calculates.

**Implements:** [Node](../../core-api-reference/common.md#node), [Titled](../../core-api-reference/common.md#titled), [Versioned](../../core-api-reference/common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. It increases by one with each update. `flowUpdate` and `flowDelete` require the current version, so a write fails if someone else changed the flow after you read it. |
| `title` | `String!` | The human-readable display name. |
| `description` | `String` | Notes about the flow. Printable ASCII and line feeds (`\n`). |
| `isEnabled` | `Boolean!` | Whether the flow processes the messages of its devices. A new flow is disabled. |
| `nodes` | [[IotLogicFlowNode](#iotlogicflownode)!]! | The nodes of the graph, in the order of the last write. |
| `transitions` | [[IotLogicFlowTransition](#iotlogicflowtransition)!]! | The transitions of the graph, in the order of the last write. |
| `deviceCount` | `Int!` | The number of devices in the data sources of the flow that your roles let you read. A device in two data sources counts twice. In rare cases, a flow keeps a device that no longer exists, and the device still counts until the data source no longer lists it. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the flow was created. |
| `updatedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the flow was last changed. |

</details>

---

### flowDelete

Deletes a flow and removes its devices from it. After the delete, the flow no longer keeps those devices from being deleted. A flow can always be deleted, even when its graph no longer passes validation.

```graphql
flowDelete(input: IotLogicFlowDeleteInput!): IotLogicFlowDeletePayload!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `IotLogicFlowDeleteInput!` | The input fields for deleting the flow. |

**Input types:**

<details>

<summary>IotLogicFlowDeleteInput</summary>

Input for deleting a flow.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The flow to delete. |
| `version` | `Int!` | The current version for optimistic locking. |

</details>

**Output types:**

<details>

<summary>IotLogicFlowDeletePayload</summary>

The outcome of deleting a flow.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the deleted flow. |

</details>

---

## Objects

<a id="iotlogicflow"></a>

### IotLogicFlow

A graph of nodes and transitions that processes the messages of the devices attached to its data sources, and adds the attributes that it calculates.

**Implements:** [Node](../../core-api-reference/common.md#node), [Titled](../../core-api-reference/common.md#titled), [Versioned](../../core-api-reference/common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. It increases by one with each update. `flowUpdate` and `flowDelete` require the current version, so a write fails if someone else changed the flow after you read it. |
| `title` | `String!` | The human-readable display name. |
| `description` | `String` | Notes about the flow. Printable ASCII and line feeds (`\n`). |
| `isEnabled` | `Boolean!` | Whether the flow processes the messages of its devices. A new flow is disabled. |
| `nodes` | [[IotLogicFlowNode](#iotlogicflownode)!]! | The nodes of the graph, in the order of the last write. |
| `transitions` | [[IotLogicFlowTransition](#iotlogicflowtransition)!]! | The transitions of the graph, in the order of the last write. |
| `deviceCount` | `Int!` | The number of devices in the data sources of the flow that your roles let you read. A device in two data sources counts twice. In rare cases, a flow keeps a device that no longer exists, and the device still counts until the data source no longer lists it. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the flow was created. |
| `updatedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the flow was last changed. |

---

<a id="iotlogicflowdebugvalues"></a>

### IotLogicFlowDebugValues

The recent values of one device. Both maps use the attribute name as the key, with the channel number if it has one, such as `lls_level_0`. Each attribute has at most 12 entries, newest first. Each entry is `{ "value": number | boolean | string | null, "messageTime": DateTime, "serverTime": DateTime }`. `messageTime` is when the device created the message, and `serverTime` is when Navixy received it.

Integers larger than 2^53 − 1 or smaller than −(2^53 − 1), times, and byte arrays are returned as strings. Durations are returned in milliseconds. Attributes that hold arrays or objects aren't returned.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceId` | `ID!` | The ID of the device. |
| `allFields` | [JSON](../../core-api-reference/common.md#json)! | One entry per recent message of the device. A message without the attribute, or with an empty value for it, has an entry with `value: null` and the `messageTime` of that message. For a message without the attribute, `serverTime` has the same value as `messageTime`, because Navixy doesn't record when such a message arrived. |
| `nonNullFields` | [JSON](../../core-api-reference/common.md#json)! | The recent values of the attribute that aren't `null`. They can be older than the messages in `allFields`, so this map isn't part of `allFields`. An attribute with no such value isn't in the map. |

---

<a id="iotlogicdeviceflowlink"></a>

### IotLogicDeviceFlowLink

A device in a data source of a flow. The messages of the device enter the flow at this data source.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `device` | [Device](../../business-data-repository/api-reference/devices/README.md#device) | The device. `null` in rare cases, when the flow kept a device that no longer exists. The link stays until no data source of the flow lists the device. |
| `deviceId` | `ID!` | The ID of the device, also when `device` is `null`. |
| `flow` | [IotLogicFlow](#iotlogicflow)! | The flow that the device is in. |
| `entryNode` | [IotLogicFlowDataSourceNode](#iotlogicflowdatasourcenode)! | The data source of the flow that lists the device. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the device was added to this data source. |

---

<a id="iotlogicflowdatasourcenode"></a>

### IotLogicFlowDataSourceNode

A Data Source node: the devices whose messages enter the flow at this node.

**Implements:** [IotLogicFlowNode](#iotlogicflownode)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the node. It is unique within the flow, and it stays the same when an update keeps the node. |
| `name` | `String!` | The name that the flow editor shows. |
| `description` | `String` | Notes about the node. |
| `position` | [IotLogicFlowNodePosition](#iotlogicflownodeposition) | The coordinates where the flow editor draws the node. `null` for a node that has no stored position. |
| `devices` | [[Device](../../business-data-repository/api-reference/devices/README.md#device)!]! | The devices whose messages enter the flow at this node and that your roles let you read, ordered by device ID. The list has no pagination. A device that no longer exists isn't in the list, so the list can be shorter than `IotLogicFlow.deviceCount`. If you only need the number of devices, read `deviceCount`, because it doesn't load the device records. |

---

<a id="iotlogicflowlogicnode"></a>

### IotLogicFlowLogicNode

An IF/THEN Logic node: a message for which `condition` is true leaves by the `THEN` transitions, and every other message leaves by the `ELSE` transitions.

**Implements:** [IotLogicFlowNode](#iotlogicflownode)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the node. It is unique within the flow, and it stays the same when an update keeps the node. |
| `name` | `String!` | The name that the flow editor shows. |
| `description` | `String` | Notes about the node. |
| `position` | [IotLogicFlowNodePosition](#iotlogicflownodeposition) | The coordinates where the flow editor draws the node. `null` for a node that has no stored position. |
| `condition` | `String!` | An expression in the Navixy IoT Logic Expression Language, calculated for each message. |
| `expressionName` | `String` | The attribute that stores the result of the check. `null` when the node only chooses the transition. |

---

<a id="iotlogicflowinitiateattributesnode"></a>

### IotLogicFlowInitiateAttributesNode

An Initiate Attribute node: it calculates attributes and adds them to the message.

**Implements:** [IotLogicFlowNode](#iotlogicflownode)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the node. It is unique within the flow, and it stays the same when an update keeps the node. |
| `name` | `String!` | The name that the flow editor shows. |
| `description` | `String` | Notes about the node. |
| `position` | [IotLogicFlowNodePosition](#iotlogicflownodeposition) | The coordinates where the flow editor draws the node. `null` for a node that has no stored position. |
| `attributes` | [[IotLogicFlowAttributeAssignment](#iotlogicflowattributeassignment)!]! | The attribute assignments of the node. |

---

<a id="iotlogicflowoutputendpointnode"></a>

### IotLogicFlowOutputEndpointNode

An Output Endpoint node: it ends a path and sends the message, with the attributes that the path calculated, to the Navixy platform.

**Implements:** [IotLogicFlowNode](#iotlogicflownode)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the node. It is unique within the flow, and it stays the same when an update keeps the node. |
| `name` | `String!` | The name that the flow editor shows. |
| `description` | `String` | Notes about the node. |
| `position` | [IotLogicFlowNodePosition](#iotlogicflownodeposition) | The coordinates where the flow editor draws the node. `null` for a node that has no stored position. |

---

<a id="iotlogicflowactionnode"></a>

### IotLogicFlowActionNode

A Device Action node.

**Implements:** [IotLogicFlowNode](#iotlogicflownode)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the node. It is unique within the flow, and it stays the same when an update keeps the node. |
| `name` | `String!` | The name that the flow editor shows. |
| `description` | `String` | Notes about the node. |
| `position` | [IotLogicFlowNodePosition](#iotlogicflownodeposition) | The coordinates where the flow editor draws the node. `null` for a node that has no stored position. |

---

<a id="iotlogicflowwebhooknode"></a>

### IotLogicFlowWebhookNode

A Webhook node.

**Implements:** [IotLogicFlowNode](#iotlogicflownode)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the node. It is unique within the flow, and it stays the same when an update keeps the node. |
| `name` | `String!` | The name that the flow editor shows. |
| `description` | `String` | Notes about the node. |
| `position` | [IotLogicFlowNodePosition](#iotlogicflownodeposition) | The coordinates where the flow editor draws the node. `null` for a node that has no stored position. |

---

<a id="iotlogicflowattributeassignment"></a>

### IotLogicFlowAttributeAssignment

One attribute that an Initiate Attribute node calculates.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `name` | `String!` | The name of the attribute. |
| `expression` | `String!` | An expression in the Navixy IoT Logic Expression Language, calculated for each message. |

---

<a id="iotlogicflownodeposition"></a>

### IotLogicFlowNodePosition

The coordinates where the flow editor draws a node.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `x` | `Int!` | The horizontal coordinate. |
| `y` | `Int!` | The vertical coordinate. |

---

<a id="iotlogicflowtransition"></a>

### IotLogicFlowTransition

A directed connection between two nodes of the graph.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `from` | `ID!` | The ID of the node that the transition leaves. |
| `to` | `ID!` | The ID of the node that the transition enters. |
| `type` | [IotLogicFlowTransitionType](#iotlogicflowtransitiontype)! | The outcome that the transition follows: `THEN` or `ELSE` after a logic node, `SIMPLE` after every other node. |

---

<a id="iotlogicflowpayload"></a>

### IotLogicFlowPayload

The flow as it was saved.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `flow` | [IotLogicFlow](#iotlogicflow)! | The flow after the write. |

---

<a id="iotlogicflowdeletepayload"></a>

### IotLogicFlowDeletePayload

The outcome of deleting a flow.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the deleted flow. |

---

## Inputs

<a id="iotlogicflowfilter"></a>

### IotLogicFlowFilter

Filtering options for flows. A flow must match every condition that you set.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Only flows whose title contains this text. The match ignores letter case. |
| `isEnabled` | `Boolean` | Only enabled, or only disabled, flows. |
| `deviceId` | `ID` | Only flows that have this device in one of their data sources. No flows for a device that your roles don't let you read. |

---

<a id="iotlogicfloworder"></a>

### IotLogicFlowOrder

Ordering options for flows.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [IotLogicFlowOrderField](#iotlogicfloworderfield) | The standard field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

<a id="iotlogicdeviceflowlinkfilter"></a>

### IotLogicDeviceFlowLinkFilter

Filtering options for device links. A link must match every condition that you set.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `flowId` | `ID` | Only the links of this flow. |
| `deviceIds` | `[ID!]` | Only the links of these devices, at most 1,000 IDs. An empty list returns no links. Leave the field out to list the links of every device. |

---

<a id="iotlogicdeviceflowlinkorder"></a>

### IotLogicDeviceFlowLinkOrder

Ordering options for device links.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [IotLogicDeviceFlowLinkOrderField](#iotlogicdeviceflowlinkorderfield) | The standard field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

<a id="iotlogicflownodeinput"></a>

### IotLogicFlowNodeInput

A node in a create or update request. `key` names the node inside this one request, and transitions refer to the node by its key. `id` is the ID of the stored node that this entry keeps. Leave out `id` for a new node.

Each field below that names a node type belongs to that type only. The other types ignore it without an error: `deviceIds` to `DATA_SOURCE`, `condition` and `expressionName` to `LOGIC`, and `attributes` to `INITIATE_ATTRIBUTES`. `OUTPUT_ENDPOINT`, `ACTION`, and `WEBHOOK` nodes have no fields of their own.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `key` | `String!` | A name for the node, valid only inside this request. Each node in the request needs a different key. |
| `id` | `ID` | The ID of the stored node that this entry keeps. Leave it out for a new node. |
| `type` | [IotLogicFlowNodeType](#iotlogicflownodetype)! | The node type. |
| `name` | `String!` | English letters, digits, underscores, dashes, and slashes, in words separated by spaces. No space at the start or end. Tabs and line breaks aren't accepted. |
| `description` | `String` | Printable ASCII and line feeds (`\n`), at most 4,000 characters. Spaces at the start and the end are kept. |
| `position` | [IotLogicFlowNodePositionInput](#iotlogicflownodepositioninput) | The coordinates where the flow editor draws the node. |
| `deviceIds` | `[ID!]` | `DATA_SOURCE` only. The devices whose messages enter the flow at this node. In an update, leave it out to keep the current devices, send `[]` to remove all of them, or send a list to replace them. `[]` and a new list change only the devices that your roles let you read, and the other devices stay attached. A new node without it has no devices. The data sources of a flow can have at most 25,000 devices together, and a device in two data sources counts twice. A device that your roles don't let you read fails with `UNKNOWN_DEVICE`, as a device that doesn't exist does. Validation counts every attached device, including the ones that you can't read, toward the limit and toward attribute conflicts. |
| `condition` | `String` | `LOGIC` only. The condition to check, in the Navixy IoT Logic Expression Language. Printable ASCII on one line, at most 480 characters. |
| `expressionName` | `String` | `LOGIC` only, optional. The attribute that stores the result of the check. Lowercase letters and digits in groups joined by single underscores, such as `is_too_warm` (`^[a-z0-9]+(_[a-z0-9]+)*$`). A final group of digits, such as `_2`, gives the channel number. At most 48 characters, not counting the channel number. Navixy reserves 12 names for the position of a device, such as `position_latitude`. See [Initiate Attribute](../node-types.md#initiate-attribute). |
| `attributes` | [[IotLogicFlowAttributeAssignmentInput](#iotlogicflowattributeassignmentinput)!] | `INITIATE_ATTRIBUTES` only. The attributes that the node calculates. At most 50 per node, and at most 200 per flow, counting the `expressionName` of every logic node. |

---

<a id="iotlogicflowattributeassignmentinput"></a>

### IotLogicFlowAttributeAssignmentInput

One attribute that an Initiate Attribute node calculates: its name and the expression for its value.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `name` | `String!` | The name of the attribute. Lowercase letters and digits in groups joined by single underscores, such as `fuel_level_l` (`^[a-z0-9]+(_[a-z0-9]+)*$`). A final group of digits, such as `_2`, gives the channel number. At most 48 characters, not counting the channel number. Navixy reserves 12 names for the position of a device, such as `position_latitude`. See [Initiate Attribute](../node-types.md#initiate-attribute). |
| `expression` | `String!` | The expression that calculates the value, in the Navixy IoT Logic Expression Language. Printable ASCII on one line, at most 960 characters. |

---

<a id="iotlogicflowtransitioninput"></a>

### IotLogicFlowTransitionInput

A transition in a create or update request. `from` and `to` are node keys from the same request, not node IDs.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `from` | `String!` | The `key` of the node the transition leaves. |
| `to` | `String!` | The `key` of the node the transition enters. |
| `type` | [IotLogicFlowTransitionType](#iotlogicflowtransitiontype)! | The outcome that the transition follows: `THEN` or `ELSE` after a logic node, `SIMPLE` after every other node. |

---

<a id="iotlogicflownodepositioninput"></a>

### IotLogicFlowNodePositionInput

The coordinates where the flow editor draws the node.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `x` | `Int!` | The horizontal coordinate. |
| `y` | `Int!` | The vertical coordinate. |

---

<a id="iotlogicflowcreateinput"></a>

### IotLogicFlowCreateInput

Input for creating a flow. The flow is created disabled.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `title` | `String!` | Printable ASCII, not blank, at most 255 characters. |
| `description` | `String` | Printable ASCII and line feeds (`\n`), at most 4,000 characters. Spaces at the start and the end are kept. |
| `nodes` | [[IotLogicFlowNodeInput](#iotlogicflownodeinput)!]! | At most 64. |
| `transitions` | [[IotLogicFlowTransitionInput](#iotlogicflowtransitioninput)!]! | At most 96. |

---

<a id="iotlogicflowupdateinput"></a>

### IotLogicFlowUpdateInput

Input for updating a flow. `nodes` and `transitions` are the whole new graph, not a list of changes. A node that the list leaves out is removed, together with its devices. Send both lists together. A field that you leave out keeps its stored value.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The flow to update. |
| `version` | `Int!` | The current version for optimistic locking. |
| `title` | `String` | Printable ASCII, not blank, at most 255 characters. |
| `description` | `String` | Printable ASCII and line feeds (`\n`), at most 4,000 characters. Spaces at the start and the end are kept. |
| `nodes` | [[IotLogicFlowNodeInput](#iotlogicflownodeinput)!] | At most 64. |
| `transitions` | [[IotLogicFlowTransitionInput](#iotlogicflowtransitioninput)!] | At most 96. |
| `isEnabled` | `Boolean` | `true` enables the flow and validates the whole graph again. `false` disables the flow. An update that only sets `isEnabled: false` validates nothing. |

---

<a id="iotlogicflowdeleteinput"></a>

### IotLogicFlowDeleteInput

Input for deleting a flow.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The flow to delete. |
| `version` | `Int!` | The current version for optimistic locking. |

---

## Enums

<a id="iotlogicflowtransitiontype"></a>

### IotLogicFlowTransitionType

The outcome of the node that a transition leaves, and so the messages that follow the transition.

| Value | Description |
| ----- | ----------- |
| `SIMPLE` | A transition that leaves any node other than a logic node. |
| `THEN` | Followed by the messages for which the condition of the logic node is true. |
| `ELSE` | Followed by every other message that leaves the logic node. |

---

<a id="iotlogicflownodetype"></a>

### IotLogicFlowNodeType

The node types that a flow can have.

**Values:** `DATA_SOURCE`, `LOGIC`, `INITIATE_ATTRIBUTES`, `OUTPUT_ENDPOINT`, `ACTION`, `WEBHOOK`

---

<a id="iotlogicfloworderfield"></a>

### IotLogicFlowOrderField

Fields available for ordering flows.

| Value | Description |
| ----- | ----------- |
| `TITLE` | Order by title. |
| `CREATED_AT` | Order by creation time. |
| `UPDATED_AT` | Order by last modification time. |

---

<a id="iotlogicdeviceflowlinkorderfield"></a>

### IotLogicDeviceFlowLinkOrderField

Fields to order device links by: `CREATED_AT`, when the device was added to the data source, or `FLOW_TITLE`, the title of the flow.

**Values:** `CREATED_AT`, `FLOW_TITLE`

---

## Interfaces

<a id="iotlogicflownode"></a>

### IotLogicFlowNode

A node of a flow: one step of the graph, shown as a box in the flow editor. A flow node isn't a [`Node`](../../core-api-reference/common.md#node), so the `node` and `nodes` queries don't return it. Read the nodes of a flow through the flow.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the node. It is unique within the flow, and it stays the same when an update keeps the node. |
| `name` | `String!` | The name that the flow editor shows. |
| `description` | `String` | Notes about the node. |
| `position` | [IotLogicFlowNodePosition](#iotlogicflownodeposition) | The coordinates where the flow editor draws the node. `null` for a node that has no stored position. |

---

## Pagination types

<a id="iotlogicflowconnection"></a>

### IotLogicFlowConnection

A paginated list of IotLogicFlow items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[IotLogicFlowEdge](#iotlogicflowedge)!]! | A list of edges. |
| `nodes` | [[IotLogicFlow](#iotlogicflow)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="iotlogicflowedge"></a>

### IotLogicFlowEdge

An edge in the IotLogicFlow connection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [IotLogicFlow](#iotlogicflow)! | The flow at the end of the edge. |

---

<a id="iotlogicdeviceflowlinkconnection"></a>

### IotLogicDeviceFlowLinkConnection

A paginated list of IotLogicDeviceFlowLink items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[IotLogicDeviceFlowLinkEdge](#iotlogicdeviceflowlinkedge)!]! | A list of edges. |
| `nodes` | [[IotLogicDeviceFlowLink](#iotlogicdeviceflowlink)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="iotlogicdeviceflowlinkedge"></a>

### IotLogicDeviceFlowLinkEdge

An edge in the IotLogicDeviceFlowLink connection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [IotLogicDeviceFlowLink](#iotlogicdeviceflowlink)! | The link at the end of the edge. |

---

## See also

* [Pagination](../../pagination.md)
* [Filtering and sorting](../../filtering-and-sorting/README.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
