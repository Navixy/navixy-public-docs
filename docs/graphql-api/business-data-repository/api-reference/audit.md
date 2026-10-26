---
description: >-
  Query the audit trail to retrieve a paginated, filterable log of entity
  create, update, and delete events across your workspace.
tags:
  - reference
  - beta
---

# Audit

Audit trail of the changes to the entities of your workspace.

## Queries

### auditEvents

Lists audit events in the caller's workspace.

```graphql
auditEvents(
    filter: AuditEventFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: AuditEventOrder = { field: OCCURRED_AT, direction: DESC }
  ): AuditEventConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `AuditEventFilter` | Filtering options for the returned audit events. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `AuditEventOrder` | The ordering options for the returned audit events. |

**Input types:**

<details>

<summary>AuditEventFilter</summary>

Filtering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `aggregateTypes` | [[Code](../../core-api-reference/common.md#code)!] | Filter by entity types (OR within field). |
| `aggregateIds` | `[ID!]` | Filter by specific entity IDs (OR within field). |
| `eventTypes` | [[AuditEventType](#auditeventtype)!] | Filter by event types (OR within field). |
| `sourceTypes` | [[SourceType](#sourcetype)!] | Filter by source types (OR within field). |
| `products` | [[Code](../../core-api-reference/common.md#code)!] | Filter by products (OR within field). Absent or empty: every product the field reads that the caller may read. `BdrQuery`'s fields read the products whose events the server keeps: a product the caller may not read is refused with PERMISSION_DENIED, and one it may read that the server does not keep with VALIDATION_ERROR. A field that reads one product, such as `BillingQuery.myAuditEvents`, refuses any other with VALIDATION_ERROR. |
| `traceId` | `String` | Filter by trace ID. |
| `from` | [DateTime](../../core-api-reference/common.md#datetime) | Return events that occurred after this timestamp. |
| `to` | [DateTime](../../core-api-reference/common.md#datetime) | Return events that occurred before this timestamp. |

</details>

<details>

<summary>AuditEventOrder</summary>

Ordering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AuditEventOrderField](#auditeventorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>AuditEventConnection</summary>

A paginated list of AuditEvent items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AuditEventEdge](#auditeventedge)!]! | A list of edges. |
| `nodes` | [[AuditEvent](#auditevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### entityHistory

Retrieves the change history for any entity in the caller's workspace.

```graphql
entityHistory(
    entityId: ID!
    filter: AuditEventFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: AuditEventOrder = { field: OCCURRED_AT, direction: DESC }
  ): AuditEventConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `entityId` | `ID!` | The ID of the entity to retrieve history for. |
| `filter` | `AuditEventFilter` | Filtering options for the returned audit events. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `AuditEventOrder` | The ordering options for the returned audit events. |

**Input types:**

<details>

<summary>AuditEventFilter</summary>

Filtering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `aggregateTypes` | [[Code](../../core-api-reference/common.md#code)!] | Filter by entity types (OR within field). |
| `aggregateIds` | `[ID!]` | Filter by specific entity IDs (OR within field). |
| `eventTypes` | [[AuditEventType](#auditeventtype)!] | Filter by event types (OR within field). |
| `sourceTypes` | [[SourceType](#sourcetype)!] | Filter by source types (OR within field). |
| `products` | [[Code](../../core-api-reference/common.md#code)!] | Filter by products (OR within field). Absent or empty: every product the field reads that the caller may read. `BdrQuery`'s fields read the products whose events the server keeps: a product the caller may not read is refused with PERMISSION_DENIED, and one it may read that the server does not keep with VALIDATION_ERROR. A field that reads one product, such as `BillingQuery.myAuditEvents`, refuses any other with VALIDATION_ERROR. |
| `traceId` | `String` | Filter by trace ID. |
| `from` | [DateTime](../../core-api-reference/common.md#datetime) | Return events that occurred after this timestamp. |
| `to` | [DateTime](../../core-api-reference/common.md#datetime) | Return events that occurred before this timestamp. |

</details>

<details>

<summary>AuditEventOrder</summary>

Ordering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AuditEventOrderField](#auditeventorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>AuditEventConnection</summary>

A paginated list of AuditEvent items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AuditEventEdge](#auditeventedge)!]! | A list of edges. |
| `nodes` | [[AuditEvent](#auditevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Objects

<a id="auditevent"></a>

### AuditEvent

An audit log entry recording an event that occurred in the system.

**Implements:** [Node](../../core-api-reference/common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `workspace` | [Workspace](workspaces.md#workspace) | The workspace the event was recorded in. `auditEvents` and `entityHistory` list only the caller's workspace's events, so this is always the caller's workspace. |
| `actorId` | `ID` | The id of the actor who triggered the event: a user's id from Console, a member of staff's, or the system's. |
| `actor` | [Actor](../../core-api-reference/common.md#actor) | The actor who triggered the event; null when there is none or Console does not know the id. |
| `ipAddress` | `String` | The client IP address. |
| `userAgent` | `String` | The client User-Agent string. |
| `sourceType` | [SourceType](#sourcetype)! | The source type of the request. |
| `traceId` | `String` | The distributed tracing ID (32 hex characters) for log correlation. |
| `aggregateType` | [Code](../../core-api-reference/common.md#code) | The type of entity affected: `asset`, `asset_group`, `catalog_item` (any catalog item), `custom_field_definition`, `device`, `geo_object`, `inventory`, `schedule`, or `workspace`. Older events can also have `integration` or `member`. |
| `aggregateId` | `ID` | The ID of the affected entity. |
| `eventType` | [AuditEventType](#auditeventtype)! | The type of event that occurred. |
| `product` | [Code](../../core-api-reference/common.md#code)! | The product the event belongs to, such as `bdr`. |
| `eventData` | [JSON](../../core-api-reference/common.md#json) | The event payload with details such as changed fields. |
| `occurredAt` | [DateTime](../../core-api-reference/common.md#datetime)! | The date and time when the event occurred. |

---

## Inputs

<a id="auditeventfilter"></a>

### AuditEventFilter

Filtering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `aggregateTypes` | [[Code](../../core-api-reference/common.md#code)!] | Filter by entity types (OR within field). |
| `aggregateIds` | `[ID!]` | Filter by specific entity IDs (OR within field). |
| `eventTypes` | [[AuditEventType](#auditeventtype)!] | Filter by event types (OR within field). |
| `sourceTypes` | [[SourceType](#sourcetype)!] | Filter by source types (OR within field). |
| `products` | [[Code](../../core-api-reference/common.md#code)!] | Filter by products (OR within field). Absent or empty: every product the field reads that the caller may read. `BdrQuery`'s fields read the products whose events the server keeps: a product the caller may not read is refused with PERMISSION_DENIED, and one it may read that the server does not keep with VALIDATION_ERROR. A field that reads one product, such as `BillingQuery.myAuditEvents`, refuses any other with VALIDATION_ERROR. |
| `traceId` | `String` | Filter by trace ID. |
| `from` | [DateTime](../../core-api-reference/common.md#datetime) | Return events that occurred after this timestamp. |
| `to` | [DateTime](../../core-api-reference/common.md#datetime) | Return events that occurred before this timestamp. |

---

<a id="auditeventorder"></a>

### AuditEventOrder

Ordering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AuditEventOrderField](#auditeventorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

## Enums

<a id="sourcetype"></a>

### SourceType

The source type identifying the origin of an API request.

| Value | Description |
| ----- | ----------- |
| `WEB` | Not recorded. Events have `API` or `INTERNAL`. |
| `MOBILE` | Not recorded. Events have `API` or `INTERNAL`. |
| `API` | A change that a caller of the API made. Events recorded before `INTERNAL` was introduced have `API` too. |
| `INTERNAL` | A change that the platform made on its own, such as a workspace that Navixy Console created or changed. |
| `INTEGRATION` | Not recorded. Events have `API` or `INTERNAL`. |
| `AGENT` | Reserved for a change that an AI client makes through the MCP server. Not recorded: events have `API` or `INTERNAL`. |

---

<a id="auditeventtype"></a>

### AuditEventType

The type of event recorded in the audit log.

| Value | Description |
| ----- | ----------- |
| `CREATED` | A new entity was created. |
| `UPDATED` | An existing entity was modified. |
| `DELETED` | An entity was deleted. |
| `RESTORED` | A workspace was restored. Recorded only when Navixy Console reactivates a workspace. |

---

<a id="auditeventorderfield"></a>

### AuditEventOrderField

Fields available for ordering audit events.

| Value | Description |
| ----- | ----------- |
| `OCCURRED_AT` | Order by occurrence date. |

---

## Pagination types

<a id="auditeventconnection"></a>

### AuditEventConnection

A paginated list of AuditEvent items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AuditEventEdge](#auditeventedge)!]! | A list of edges. |
| `nodes` | [[AuditEvent](#auditevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="auditeventedge"></a>

### AuditEventEdge

An edge in the AuditEvent connection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [AuditEvent](#auditevent)! | The audit event at the end of the edge. |

---

## See also

* [Pagination](../../pagination.md)
* [Filtering and sorting](../../filtering-and-sorting/README.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
