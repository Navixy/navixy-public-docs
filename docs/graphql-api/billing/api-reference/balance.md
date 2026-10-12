---
description: >-
  Reference for the workspace balance: the one balance in USD, the statement
  entries that moved it, and the myBalance and myBalanceEntries queries.
tags:
  - reference
  - beta
---

# Balances

The prepaid balance the caller's workspace holds, the statement of postings that moved it, and the queries that read them.

## Queries

### myBalance

The caller's own workspace's balance. Opened at zero if nothing has opened it yet.

```graphql
myBalance: BillingBalance!
```

**Output types:**

<details>

<summary>BillingBalance</summary>

The prepaid balance a workspace holds. A workspace holds exactly one, in USD.

`amount` is signed: negative means the workspace owes. It is a running total kept in step with the
workspace's posting journal, and the journal — the balance's statement entries — stays the source of
truth for how the total was reached.

**Implements:** [Node](../../core-api-reference/common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `workspaceId` | `ID!` | The workspace this balance belongs to. |
| `amount` | [Money](../../core-api-reference/common.md#money)! | The current total, signed. Negative means the workspace owes. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the balance was opened: on the workspace's creation, its first read, or its first posting, whichever came first. |
| `updatedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the balance last moved. |

</details>

---

### myBalanceEntries

The movements of the caller's own workspace's balance, newest first, as a statement.

  `balanceAfter` is a property of the journal, not of this ordering: an entry carries the same running
  total whichever direction you read in. Reversing the direction reverses the order lines arrive in
  and nothing else.

```graphql
myBalanceEntries(
    filter: BillingBalanceEntryFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: BillingBalanceEntryOrder = { field: CREATED_AT, direction: DESC }
  ): BillingBalanceEntryConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `BillingBalanceEntryFilter` | Filtering options for the returned entries. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `BillingBalanceEntryOrder` | The ordering options for the returned entries. |

**Input types:**

<details>

<summary>BillingBalanceEntryFilter</summary>

Filtering options for balance entries.

Narrowing a statement to a period does not restart its running totals: `balanceAfter` still
accumulates over the whole journal, so the first line of a period carries the balance carried into
that period rather than the period's own subtotal.

Only time is filterable, and that is a constraint rather than an oversight — a statement window has
to stay a contiguous run of the journal for its running totals to mean anything. Dropping, say,
every charge and keeping the corrections would leave each surviving line with a total that no
sequence of shown lines adds up to.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `createdAtFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Keep only entries posted at or after this instant. |
| `createdAtTo` | [DateTime](../../core-api-reference/common.md#datetime) | Keep only entries posted at or before this instant. |

</details>

<details>

<summary>BillingBalanceEntryOrder</summary>

Ordering options for balance entries.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [BillingBalanceEntryOrderField](#billingbalanceentryorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>BillingBalanceEntryConnection</summary>

A page of balance entries.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[BillingBalanceEntryEdge](#billingbalanceentryedge)!]! | A list of edges. |
| `nodes` | [[BillingBalanceEntry](#billingbalanceentry)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Objects

<a id="billingbalance"></a>

### BillingBalance

The prepaid balance a workspace holds. A workspace holds exactly one, in USD.

`amount` is signed: negative means the workspace owes. It is a running total kept in step with the
workspace's posting journal, and the journal — the balance's statement entries — stays the source of
truth for how the total was reached.

**Implements:** [Node](../../core-api-reference/common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `workspaceId` | `ID!` | The workspace this balance belongs to. |
| `amount` | [Money](../../core-api-reference/common.md#money)! | The current total, signed. Negative means the workspace owes. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the balance was opened: on the workspace's creation, its first read, or its first posting, whichever came first. |
| `updatedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the balance last moved. |

---

<a id="billingbalanceentry"></a>

### BillingBalanceEntry

One movement of a workspace's balance, as a line on a statement.

Not a stored row: an entry is a posting joined to the document that justifies it, and `balanceAfter`
is derived at read time rather than stored against the posting. A payment whose `valueDate` falls
before existing entries therefore shifts every line after it, the way a statement should, instead of
leaving a stale snapshot behind.

An entry is identified but not addressable — there is no `node(id:)` lookup for one, because a line
only means anything within the statement that produced it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The identifier of the posting behind this entry. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the posting happened. |
| `type` | [BillingBalanceEntryType](#billingbalanceentrytype)! | What moved the balance. |
| `amount` | [Money](../../core-api-reference/common.md#money)! | Signed from the workspace's side: positive credits the balance, negative debits it. |
| `balanceAfter` | [Money](../../core-api-reference/common.md#money)! | The balance total immediately after this entry. Independent of the ordering you requested. |
| `description` | `String` | What the justifying document has to say for itself: a correction's reason, an invoice's number, a payment's note. Null whenever that document has nothing to say, which is routine for a `TOP_UP` -- the line is labelled by its `type`, so there is nothing the accountant has to type. Not a ready-to-display label. The label belongs to `type` and needs translating; this is the operator's or the document's own words and does not. |
| `authoredBy` | `ID!` | The actor on whose authority the posting was made — the staff member behind a correction, the accountant who entered a top-up, or the service account behind a charge. Never absent: every posting is attributable. An id, not a name: billing does not hold actors, so rendering a person needs a second lookup. |

---

## Inputs

<a id="billingbalanceentryfilter"></a>

### BillingBalanceEntryFilter

Filtering options for balance entries.

Narrowing a statement to a period does not restart its running totals: `balanceAfter` still
accumulates over the whole journal, so the first line of a period carries the balance carried into
that period rather than the period's own subtotal.

Only time is filterable, and that is a constraint rather than an oversight — a statement window has
to stay a contiguous run of the journal for its running totals to mean anything. Dropping, say,
every charge and keeping the corrections would leave each surviving line with a total that no
sequence of shown lines adds up to.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `createdAtFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Keep only entries posted at or after this instant. |
| `createdAtTo` | [DateTime](../../core-api-reference/common.md#datetime) | Keep only entries posted at or before this instant. |

---

<a id="billingbalanceentryorder"></a>

### BillingBalanceEntryOrder

Ordering options for balance entries.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [BillingBalanceEntryOrderField](#billingbalanceentryorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

## Enums

<a id="billingbalanceentrytype"></a>

### BillingBalanceEntryType

What moved a balance.

| Value | Description |
| ----- | ----------- |
| `INVOICE` | A charge issued against the balance. |
| `CORRECTION` | A staff-authored correction, in either direction. |
| `TOP_UP` | Money the customer sent, credited to the balance. Distinct from a correction on purpose: one is cash taken in, the other is revenue given away, and a statement that called both a correction would make the two impossible to tell apart. |

---

<a id="billingbalanceentryorderfield"></a>

### BillingBalanceEntryOrderField

Fields available for ordering balance entries.

| Value | Description |
| ----- | ----------- |
| `CREATED_AT` | Order by the time the posting happened. |

---

## Pagination types

<a id="billingbalanceentryconnection"></a>

### BillingBalanceEntryConnection

A page of balance entries.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[BillingBalanceEntryEdge](#billingbalanceentryedge)!]! | A list of edges. |
| `nodes` | [[BillingBalanceEntry](#billingbalanceentry)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="billingbalanceentryedge"></a>

### BillingBalanceEntryEdge

An edge in the BillingBalanceEntry connection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `node` | [BillingBalanceEntry](#billingbalanceentry)! | The balance entry at the end of the edge. |
| `cursor` | `String!` | A cursor for use in pagination. |

---

## See also

* [Pagination](../../pagination.md)
* [Filtering and sorting](../../filtering-and-sorting/README.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
