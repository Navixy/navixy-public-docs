---
description: >-
  Reference for the audit events of billing in your workspace: the myAuditEvents
  and myEntityHistory queries, with the filter and the order they take.
tags:
  - reference
  - beta
---

# Audit

The audit events of billing in the caller's workspace: who changed its subscriptions, invoices, balance and other billing entities, and when, with the queries that read them.

## Queries

### myAuditEvents

The audit events of billing in the caller's own workspace, newest first by default: who wrote what to the
  workspace's subscriptions, invoices, balance and other billing entities, and when.

  **The workspace is not an argument.** It is the one the caller's token names; a token carrying no
  workspace claim is refused.

  Only `billing`'s events are read: `filter.products` absent or empty reads them, and naming any other
  product fails with `VALIDATION_ERROR`, since this field does not serve it. The types are those of
  nvx4-audit.graphql, which `BdrQuery.auditEvents` returns too.

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
| `filter` | `AuditEventFilter` | Filtering options for the returned events. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `AuditEventOrder` | The ordering options for the returned events. |

**Input types:**

<details>

<summary>AuditEventFilter</summary>

Filtering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `aggregateTypes` | [[Code](../../core-api-reference/common.md#code)!] | Filter by entity types (OR within field). |
| `aggregateIds` | `[ID!]` | Filter by specific entity IDs (OR within field). |
| `eventTypes` | [[AuditEventType](../../business-data-repository/api-reference/audit.md#auditeventtype)!] | Filter by event types (OR within field). |
| `sourceTypes` | [[SourceType](../../business-data-repository/api-reference/audit.md#sourcetype)!] | Filter by source types (OR within field). |
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
| `field` | [AuditEventOrderField](../../business-data-repository/api-reference/audit.md#auditeventorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>AuditEventConnection</summary>

A paginated list of AuditEvent items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AuditEventEdge](../../business-data-repository/api-reference/audit.md#auditeventedge)!]! | A list of edges. |
| `nodes` | [[AuditEvent](../../business-data-repository/api-reference/audit.md#auditevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### myEntityHistory

The audit events of billing about one entity of the caller's own workspace, such as a subscription or an
  invoice, newest first by default. The workspace, the access and the products are as for `myAuditEvents`.

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
| `entityId` | `ID!` | The ID of the entity whose events to return. |
| `filter` | `AuditEventFilter` | Filtering options for the returned events. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `AuditEventOrder` | The ordering options for the returned events. |

**Input types:**

<details>

<summary>AuditEventFilter</summary>

Filtering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `aggregateTypes` | [[Code](../../core-api-reference/common.md#code)!] | Filter by entity types (OR within field). |
| `aggregateIds` | `[ID!]` | Filter by specific entity IDs (OR within field). |
| `eventTypes` | [[AuditEventType](../../business-data-repository/api-reference/audit.md#auditeventtype)!] | Filter by event types (OR within field). |
| `sourceTypes` | [[SourceType](../../business-data-repository/api-reference/audit.md#sourcetype)!] | Filter by source types (OR within field). |
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
| `field` | [AuditEventOrderField](../../business-data-repository/api-reference/audit.md#auditeventorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>AuditEventConnection</summary>

A paginated list of AuditEvent items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AuditEventEdge](../../business-data-repository/api-reference/audit.md#auditeventedge)!]! | A list of edges. |
| `nodes` | [[AuditEvent](../../business-data-repository/api-reference/audit.md#auditevent)!]! | A list of nodes in the connection (without edge metadata). |
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
