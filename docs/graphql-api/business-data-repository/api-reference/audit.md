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

### myAuditEvents

Lists the audit events of the product `bdr` in the caller's workspace, newest first by default. Only `bdr`'s events
  are read: `filter.products` absent or empty reads them, and naming any other product fails with `VALIDATION_ERROR`,
  since this field does not serve it. Billing's events are read through `BillingQuery.myAuditEvents`.

```graphql
myAuditEvents(
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

Filtering options for audit events. The server that keeps the events can bound each statement of a read by a
statement timeout, five seconds by default: a read that runs longer fails with `VALIDATION_ERROR` and no field, and a
narrower one goes through: a shorter period between `from` and `to`, exact values instead of `*Contains` text, or
fewer products. A field whose filter sets `*Contains` text that is not blank adds 50 to the request's complexity, so
one request holds only a few such reads.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `actorIdContains` | `String` | Partial match on actor ID (case-insensitive contains). |
| `aggregateTypes` | [[Code](../../core-api-reference/common.md#code)!] | Filter by entity types (OR within field). |
| `aggregateTypeContains` | `String` | Partial match on entity type (case-insensitive contains). |
| `aggregateIds` | `[ID!]` | Filter by specific entity IDs (OR within field). |
| `aggregateIdContains` | `String` | Partial match on entity ID (case-insensitive contains). |
| `eventCodes` | `[String!]` | Filter by event codes (OR within field), each matched exactly as the product recorded it, letter case included. A code no event has matches nothing. |
| `operations` | [[AuditEventType](../../core-api-reference/audit.md#auditeventtype)!] | Filter by operations (OR within field). Naming both `operations` and `eventTypes` fails with `VALIDATION_ERROR`. |
| `eventTypes` | [[AuditEventType](../../core-api-reference/audit.md#auditeventtype)!] | Filter by operations (OR within field); the same as `operations`. |
| `sourceTypes` | [[SourceType](../../core-api-reference/audit.md#sourcetype)!] | Filter by source types (OR within field). |
| `products` | [[Code](../../core-api-reference/common.md#code)!] | Filter by products (OR within field). Each field reads one product, such as `bdr` for `BdrQuery.myAuditEvents` and `billing` for `BillingQuery.myAuditEvents`. Absent or empty: that product; any other product fails with VALIDATION_ERROR. |
| `traceId` | `String` | Filter by trace ID. |
| `traceIdContains` | `String` | Partial match on trace ID (case-insensitive contains). |
| `from` | [DateTime](../../core-api-reference/common.md#datetime) | Return events that occurred after this timestamp. |
| `to` | [DateTime](../../core-api-reference/common.md#datetime) | Return events that occurred before this timestamp. |

</details>

<details>

<summary>AuditEventOrder</summary>

Ordering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AuditEventOrderField](../../core-api-reference/audit.md#auditeventorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>AuditEventConnection</summary>

A paginated list of AuditEvent items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AuditEventEdge](../../core-api-reference/audit.md#auditeventedge)!]! | A list of edges. |
| `nodes` | [[AuditEvent](../../core-api-reference/audit.md#auditevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### myEntityHistory

Retrieves the change history of any entity in the caller's workspace: the audit events of the product `bdr` about
  it, newest first by default. The products are as for `myAuditEvents`.

```graphql
myEntityHistory(
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

Filtering options for audit events. The server that keeps the events can bound each statement of a read by a
statement timeout, five seconds by default: a read that runs longer fails with `VALIDATION_ERROR` and no field, and a
narrower one goes through: a shorter period between `from` and `to`, exact values instead of `*Contains` text, or
fewer products. A field whose filter sets `*Contains` text that is not blank adds 50 to the request's complexity, so
one request holds only a few such reads.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `actorIdContains` | `String` | Partial match on actor ID (case-insensitive contains). |
| `aggregateTypes` | [[Code](../../core-api-reference/common.md#code)!] | Filter by entity types (OR within field). |
| `aggregateTypeContains` | `String` | Partial match on entity type (case-insensitive contains). |
| `aggregateIds` | `[ID!]` | Filter by specific entity IDs (OR within field). |
| `aggregateIdContains` | `String` | Partial match on entity ID (case-insensitive contains). |
| `eventCodes` | `[String!]` | Filter by event codes (OR within field), each matched exactly as the product recorded it, letter case included. A code no event has matches nothing. |
| `operations` | [[AuditEventType](../../core-api-reference/audit.md#auditeventtype)!] | Filter by operations (OR within field). Naming both `operations` and `eventTypes` fails with `VALIDATION_ERROR`. |
| `eventTypes` | [[AuditEventType](../../core-api-reference/audit.md#auditeventtype)!] | Filter by operations (OR within field); the same as `operations`. |
| `sourceTypes` | [[SourceType](../../core-api-reference/audit.md#sourcetype)!] | Filter by source types (OR within field). |
| `products` | [[Code](../../core-api-reference/common.md#code)!] | Filter by products (OR within field). Each field reads one product, such as `bdr` for `BdrQuery.myAuditEvents` and `billing` for `BillingQuery.myAuditEvents`. Absent or empty: that product; any other product fails with VALIDATION_ERROR. |
| `traceId` | `String` | Filter by trace ID. |
| `traceIdContains` | `String` | Partial match on trace ID (case-insensitive contains). |
| `from` | [DateTime](../../core-api-reference/common.md#datetime) | Return events that occurred after this timestamp. |
| `to` | [DateTime](../../core-api-reference/common.md#datetime) | Return events that occurred before this timestamp. |

</details>

<details>

<summary>AuditEventOrder</summary>

Ordering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AuditEventOrderField](../../core-api-reference/audit.md#auditeventorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>AuditEventConnection</summary>

A paginated list of AuditEvent items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AuditEventEdge](../../core-api-reference/audit.md#auditeventedge)!]! | A list of edges. |
| `nodes` | [[AuditEvent](../../core-api-reference/audit.md#auditevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## See also

* [Pagination](../../pagination.md)
* [Filtering and sorting](../../filtering-and-sorting/README.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
