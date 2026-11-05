---
description: >-
  Reference for the audit events of billing in your workspace: the myAuditEvents
  and myEntityHistory queries, with the filter and the order they take.
tags:
  - reference
  - beta
---

# Audit

The audit events of Billing in the caller's workspace or organization: changes to subscriptions, invoices, and balance, with the queries that read them.

## Queries

### myAuditEvents

The audit events of billing in the caller's own workspace or in its organization, as `scope` asks, newest first
  by default: who wrote what to the workspace's subscriptions, invoices, balance and other billing entities, and when.

  - `WORKSPACE`, the default, reads the events of the workspace the caller's token names.
  - `ORGANIZATION` reads the events of the caller's organization: its own events, those of no workspace, and the
    events of its workspaces recorded with the organization, those a member of staff or billing's own jobs, such as
    the invoicing pass, record in a workspace Console confirmed to billing as the organization's included. It needs
    the token to name the organization of a workspace Console synced, and a role whose billing access covers every
    workspace of the organization; otherwise the call fails with `PERMISSION_DENIED`. Events that carry no
    organization appear in the `WORKSPACE` scope alone: those of a workspace Console has not confirmed to billing,
    and those recorded before events carried the organization, which are not filled in later.

  **The workspace and the organization are not arguments.** They are the ones the caller's token names; a token
  carrying no workspace claim is refused. Another organization's events are never read.

  Only `billing`'s events are read: `filter.products` absent or empty reads them, and naming any other product
  fails with `VALIDATION_ERROR`, since this field does not serve it. The types are the ones
  `BdrQuery.myAuditEvents` returns too.

```graphql
myAuditEvents(
    scope: BillingAuditScope = WORKSPACE
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
| `scope` | [BillingAuditScope](#billingauditscope) | Whose events to read: the caller's workspace, or its organization. |
| `filter` | `AuditEventFilter` | Filtering options for the returned events. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `AuditEventOrder` | The ordering options for the returned events. |

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

The audit events of billing about one entity, such as a subscription or an invoice, in the caller's own workspace
  or in its organization, as `scope` asks, newest first by default. The scopes, the access and the products are as for
  `myAuditEvents`.

```graphql
myEntityHistory(
    entityId: ID!
    scope: BillingAuditScope = WORKSPACE
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
| `scope` | [BillingAuditScope](#billingauditscope) | Whose events to read: the caller's workspace, or its organization. |
| `filter` | `AuditEventFilter` | Filtering options for the returned events. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `AuditEventOrder` | The ordering options for the returned events. |

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

## Enums

<a id="billingauditscope"></a>

### BillingAuditScope

Whose audit events `myAuditEvents` and `myEntityHistory` read.

| Value | Description |
| ----- | ----------- |
| `WORKSPACE` | The events of the workspace the caller's token names. |
| `ORGANIZATION` | The events of the caller's organization: its own events, those of no workspace, and the events of its workspaces recorded with the organization, those a member of staff or billing's own jobs record in a workspace Console confirmed to billing as the organization's included. Needs billing access that covers every workspace of the organization. Events that carry no organization, those of a workspace Console has not confirmed to billing and those recorded before events carried it, are read in `WORKSPACE` alone; they are not filled in later. |

---

## See also

* [Pagination](../../pagination.md)
* [Filtering and sorting](../../filtering-and-sorting/README.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
