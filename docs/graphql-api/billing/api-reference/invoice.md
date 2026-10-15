---
tags:
  - reference
  - beta
---

# Invoices

The invoices of the caller's workspace: what each one charges for each billing period, its lines, its status, and the queries that read them.

## Queries

### myInvoice

One of the caller's own workspace's invoices; null when it does not exist or belongs to another workspace.

```graphql
myInvoice(id: ID!): BillingWorkspaceInvoice
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The ID of the invoice to retrieve. |

**Output types:**

<details>

<summary>BillingWorkspaceInvoice</summary>

One of the caller's own workspace's invoices: its charge for every complete billing period of its subscriptions that
no invoice covered yet, as of one pass. `periodStart` and `periodEnd` span the periods covered. The total is posted to
the balance as the `INVOICE` entry on its statement.

`status` is `PAID` at issue when the balance covers the total, and `ISSUED` when the invoice opens a debt.
Payments are not tied to invoices and settle the oldest invoices first; the payment that covers an invoice marks it
`PAID` and sets `paidAt`. Once the grace period has passed (`dueAt`), the daily pass marks an unpaid one `OVERDUE`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` |  |
| `workspaceId` | `ID!` | The workspace being charged: the one the token names. |
| `invoiceNumber` | `String!` | Human-facing number, unique across the schema. |
| `periodStart` | [DateTime](../../core-api-reference/common.md#datetime)! | First instant of the earliest billing period covered. |
| `periodEnd` | [DateTime](../../core-api-reference/common.md#datetime)! | End of the latest billing period covered, exclusive. |
| `subtotal` | [Money](../../core-api-reference/common.md#money)! | Sum of the line items, before adjustments. |
| `priceModify` | [Money](../../core-api-reference/common.md#money)! | Signed adjustment applied to the subtotal — a discount is negative. |
| `total` | [Money](../../core-api-reference/common.md#money)! | What the customer owes: `subtotal` plus `priceModify`. |
| `status` | [BillingInvoiceStatus](#billinginvoicestatus)! | Current lifecycle status. |
| `issuedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the invoice was issued. Its place in a list is this, not `createdAt`. |
| `dueAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When payment is due. |
| `paidAt` | [DateTime](../../core-api-reference/common.md#datetime) | When it was paid; null while it is not. |
| `lineItems` | [[BillingWorkspaceInvoiceLineItem](#billingworkspaceinvoicelineitem)!]! | The lines, in display order: per covered period, the period fees, then every metric of the plan, then the line-item submissions. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `updatedAt` | [DateTime](../../core-api-reference/common.md#datetime)! |  |

</details>

---

### myInvoices

The caller's own workspace's invoices, newest issue date first.

  **The workspace is not an argument.** It is the one the caller's token names; a token carrying no
  workspace claim is refused.

```graphql
myInvoices(
    filter: BillingWorkspaceInvoiceFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: BillingInvoiceOrder = { field: ISSUED_AT, direction: DESC }
  ): BillingWorkspaceInvoiceConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `BillingWorkspaceInvoiceFilter` | Filtering options for the returned invoices. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `BillingInvoiceOrder` | The ordering options for the returned invoices. |

**Input types:**

<details>

<summary>BillingWorkspaceInvoiceFilter</summary>

Filtering options for the caller's own invoices. Fields combine with AND.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `status` | [BillingInvoiceStatus](#billinginvoicestatus) | Keep only invoices in this status. |
| `issuedFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Keep only invoices issued at or after this instant. |
| `issuedTo` | [DateTime](../../core-api-reference/common.md#datetime) | Keep only invoices issued at or before this instant. |

</details>

<details>

<summary>BillingInvoiceOrder</summary>

Ordering options for invoices.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [BillingInvoiceOrderField](#billinginvoiceorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>BillingWorkspaceInvoiceConnection</summary>

A page of the caller's own workspace's invoices.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[BillingWorkspaceInvoiceEdge](#billingworkspaceinvoiceedge)!]! | A list of edges. |
| `nodes` | [[BillingWorkspaceInvoice](#billingworkspaceinvoice)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Objects

<a id="billingworkspaceinvoice"></a>

### BillingWorkspaceInvoice

One of the caller's own workspace's invoices: its charge for every complete billing period of its subscriptions that
no invoice covered yet, as of one pass. `periodStart` and `periodEnd` span the periods covered. The total is posted to
the balance as the `INVOICE` entry on its statement.

`status` is `PAID` at issue when the balance covers the total, and `ISSUED` when the invoice opens a debt.
Payments are not tied to invoices and settle the oldest invoices first; the payment that covers an invoice marks it
`PAID` and sets `paidAt`. Once the grace period has passed (`dueAt`), the daily pass marks an unpaid one `OVERDUE`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` |  |
| `workspaceId` | `ID!` | The workspace being charged: the one the token names. |
| `invoiceNumber` | `String!` | Human-facing number, unique across the schema. |
| `periodStart` | [DateTime](../../core-api-reference/common.md#datetime)! | First instant of the earliest billing period covered. |
| `periodEnd` | [DateTime](../../core-api-reference/common.md#datetime)! | End of the latest billing period covered, exclusive. |
| `subtotal` | [Money](../../core-api-reference/common.md#money)! | Sum of the line items, before adjustments. |
| `priceModify` | [Money](../../core-api-reference/common.md#money)! | Signed adjustment applied to the subtotal — a discount is negative. |
| `total` | [Money](../../core-api-reference/common.md#money)! | What the customer owes: `subtotal` plus `priceModify`. |
| `status` | [BillingInvoiceStatus](#billinginvoicestatus)! | Current lifecycle status. |
| `issuedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the invoice was issued. Its place in a list is this, not `createdAt`. |
| `dueAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When payment is due. |
| `paidAt` | [DateTime](../../core-api-reference/common.md#datetime) | When it was paid; null while it is not. |
| `lineItems` | [[BillingWorkspaceInvoiceLineItem](#billingworkspaceinvoicelineitem)!]! | The lines, in display order: per covered period, the period fees, then every metric of the plan, then the line-item submissions. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `updatedAt` | [DateTime](../../core-api-reference/common.md#datetime)! |  |

---

<a id="billingworkspaceinvoicelineitem"></a>

### BillingWorkspaceInvoiceLineItem

One line of a workspace's invoice: a period fee, a metric's priced usage, or a line-item submission passed through.
`amount` is `quantity × unitPrice` rounded half up to the minor unit, except on a line-item submission that carried
its own amount.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` |  |
| `description` | `String!` | What the line charges for, ending with the billing period it belongs to, as `, 2026-01-15 – 2026-02-14`. |
| `quantity` | `Float!` | Units charged: the share of the month on a period-fee line, the aggregated usage over the included limit on a metric line, the quantity as submitted on a line-item line. `metric.unit` names the unit. |
| `unitPrice` | [Money](../../core-api-reference/common.md#money)! | Price of one unit in the invoice's currency. |
| `amount` | [Money](../../core-api-reference/common.md#money)! | The line's total. |
| `productDomain` | [BillingProductDomain](plan.md#billingproductdomain) | The product the line belongs to. |
| `metric` | [BillingMetric](plan.md#billingmetric) | The metric the line prices; null on a period-fee line and on a line-item submission's line. |
| `subscriptionId` | `ID` | The subscription the line charges for, as in `mySubscriptions`. |
| `planId` | `ID` | The plan that priced the line; the subscription's branch, when it has one, overrode this plan's fee and prices. |
| `periodStart` | [DateTime](../../core-api-reference/common.md#datetime) | First instant of the billing period the line belongs to. |
| `periodEnd` | [DateTime](../../core-api-reference/common.md#datetime) | End of that billing period, exclusive. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! |  |

---

## Inputs

<a id="billinginvoiceorder"></a>

### BillingInvoiceOrder

Ordering options for invoices.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [BillingInvoiceOrderField](#billinginvoiceorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

<a id="billingworkspaceinvoicefilter"></a>

### BillingWorkspaceInvoiceFilter

Filtering options for the caller's own invoices. Fields combine with AND.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `status` | [BillingInvoiceStatus](#billinginvoicestatus) | Keep only invoices in this status. |
| `issuedFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Keep only invoices issued at or after this instant. |
| `issuedTo` | [DateTime](../../core-api-reference/common.md#datetime) | Keep only invoices issued at or before this instant. |

---

## Enums

<a id="billinginvoicestatus"></a>

### BillingInvoiceStatus

Lifecycle of an invoice. Billing writes ISSUED, PAID and OVERDUE; DRAFT and VOID are reserved and never set.

| Value | Description |
| ----- | ----------- |
| `DRAFT` | Assembled but not yet issued to the customer. |
| `ISSUED` | Issued and outstanding. |
| `PAID` | Settled. |
| `OVERDUE` | Issued, past its due date, still outstanding. |
| `VOID` | Cancelled without being paid. |

---

<a id="billinginvoiceorderfield"></a>

### BillingInvoiceOrderField

Fields available for ordering invoices.

| Value | Description |
| ----- | ----------- |
| `ISSUED_AT` | Order by the day the invoice was issued. |

---

## Pagination types

<a id="billingworkspaceinvoiceconnection"></a>

### BillingWorkspaceInvoiceConnection

A page of the caller's own workspace's invoices.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[BillingWorkspaceInvoiceEdge](#billingworkspaceinvoiceedge)!]! | A list of edges. |
| `nodes` | [[BillingWorkspaceInvoice](#billingworkspaceinvoice)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="billingworkspaceinvoiceedge"></a>

### BillingWorkspaceInvoiceEdge

An edge in the BillingWorkspaceInvoice connection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [BillingWorkspaceInvoice](#billingworkspaceinvoice)! | The invoice at the end of the edge. |

---

## See also

* [Pagination](../../pagination.md)
* [Filtering and sorting](../../filtering-and-sorting/README.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
