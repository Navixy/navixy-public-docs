---
tags:
  - reference
  - beta
---

# Rule history

The history of each rule: one event every time a rule turns on or off for an asset, with the device, the message, and the values that met the equations. The `events` query lists the events. It belongs to the `rules` field of the `Query` type: `query { rules { events { ... } } }`.

## Queries

### events

Lists the transitions of the rules' alarms in the caller's workspace, about the assets the caller may read.

```graphql
events(
    filter: RulesRuleEventFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: RulesRuleEventOrder = { field: EVENT_TIME, direction: DESC }
  ): RulesRuleEventConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `RulesRuleEventFilter` | Filtering options for the returned events. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../../pagination.md). |
| `orderBy` | `RulesRuleEventOrder` | The ordering options for the returned events. |

**Input types:**

<details>

<summary>RulesRuleEventFilter</summary>

Filtering options for events. All criteria are ANDed; each list holds at most 100 ids, and an empty list matches
nothing.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `ruleIds` | `[ID!]` |  |
| `assetIds` | `[ID!]` |  |
| `deviceIds` | `[ID!]` |  |
| `kinds` | [[RulesRuleEventKind](#rulesruleeventkind)!] |  |
| `occurredFrom` | [DateTime](../../../core-api-reference/common.md#datetime) | Events at or after this time. |
| `occurredTo` | [DateTime](../../../core-api-reference/common.md#datetime) | Events at or before this time. |

</details>

<details>

<summary>RulesRuleEventOrder</summary>

Ordering options for events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [RulesRuleEventOrderField](#rulesruleeventorderfield) | The standard field to order by. |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>RulesRuleEventConnection</summary>

A paginated list of RulesRuleEvent items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[RulesRuleEventEdge](#rulesruleeventedge)!]! | A list of edges. |
| `nodes` | [[RulesRuleEvent](#rulesruleevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Objects

<a id="rulesruleevent"></a>

### RulesRuleEvent

One transition of a rule's alarm for an asset: the message that caused it and the values that
satisfied the equations. An activation's id is the key the notification service deduplicates its
notification on; the notification has an id of its own and does not carry this one.

**Implements:** [Node](../../../core-api-reference/common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `kind` | [RulesRuleEventKind](#rulesruleeventkind)! |  |
| `ruleId` | `ID!` |  |
| `assetId` | `ID!` | The asset whose alarm changed. |
| `deviceId` | `ID!` | The device whose message caused the transition. |
| `messageId` | [UUID](../../../core-api-reference/common.md#uuid)! | The telemetry message's own id, as the platform's telemetry contract minted it. |
| `eventTime` | [DateTime](../../../core-api-reference/common.md#datetime)! | When the message's event happened; a buffered upload delivers events hours or months old. |
| `mayNotify` | `Boolean!` | Whether the message's delivery allowed a notification: a live message does, a buffered one only within the module's notification window, a replay never. Only an activation notifies; a deactivation records the same decision. |
| `values` | [[RulesEvaluatedValue](#rulesevaluatedvalue)!]! | The values the equations were satisfied with, one per satisfied parameter equation. |
| `sourceEventId` | `String` | The activation in the engine's own terms, carried by its notification as a reference. Absent on a deactivation. |
| `activationCounter` | [Long](../../../core-api-reference/common.md#long) | How many times this rule's alarm has turned on for this asset, this activation included. Absent on a deactivation. |

---

<a id="rulesevaluatedvalue"></a>

### RulesEvaluatedValue

A value an equation was satisfied with and where it came from: the device's own message or another device's.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | `String!` |  |
| `index` | `Int` | The channel, for a field that has one. Absent otherwise. |
| `value` | [RulesValue](rules.md#rulesvalue)! |  |
| `unit` | `String` | The unit as the device's decoder labelled it. Absent when the field carried none. |
| `deviceId` | `ID!` | The device that reported the value. |
| `eventTime` | [DateTime](../../../core-api-reference/common.md#datetime)! | When that device's message happened. |

---

## Inputs

<a id="rulesruleeventfilter"></a>

### RulesRuleEventFilter

Filtering options for events. All criteria are ANDed; each list holds at most 100 ids, and an empty list matches
nothing.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `ruleIds` | `[ID!]` |  |
| `assetIds` | `[ID!]` |  |
| `deviceIds` | `[ID!]` |  |
| `kinds` | [[RulesRuleEventKind](#rulesruleeventkind)!] |  |
| `occurredFrom` | [DateTime](../../../core-api-reference/common.md#datetime) | Events at or after this time. |
| `occurredTo` | [DateTime](../../../core-api-reference/common.md#datetime) | Events at or before this time. |

---

<a id="rulesruleeventorder"></a>

### RulesRuleEventOrder

Ordering options for events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [RulesRuleEventOrderField](#rulesruleeventorderfield) | The standard field to order by. |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

## Enums

<a id="rulesruleeventkind"></a>

### RulesRuleEventKind

The two transitions of an alarm.

**Values:** `ACTIVATION`, `DEACTIVATION`

---

<a id="rulesruleeventorderfield"></a>

### RulesRuleEventOrderField

Fields available for ordering events. Within one event time the id breaks the tie.

| Value | Description |
| ----- | ----------- |
| `EVENT_TIME` | Order by the event's time. |

---

## Pagination types

<a id="rulesruleeventconnection"></a>

### RulesRuleEventConnection

A paginated list of RulesRuleEvent items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[RulesRuleEventEdge](#rulesruleeventedge)!]! | A list of edges. |
| `nodes` | [[RulesRuleEvent](#rulesruleevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="rulesruleeventedge"></a>

### RulesRuleEventEdge

An edge in the RulesRuleEvent connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [RulesRuleEvent](#rulesruleevent)! | The event at the end of the edge. |

---

## See also

* [Pagination](../../../pagination.md)
* [Filtering and sorting](../../../filtering-and-sorting/README.md)
* [Error handling](../../../error-handling.md)
* [Optimistic locking](../../../optimistic-locking.md)
* [Limits](../../../limits.md)
