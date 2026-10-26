---
description: >-
  Reference for the usage-metric and invoice-line-item submissions of your
  workspace: the myUsageMetricSubmissions and myInvoiceLineItemSubmissions
  queries, with their filter, order, and pagination types.
tags:
  - reference
  - beta
---

# Submissions

The usage-metric and invoice-line-item submissions reported for your workspace, and the queries that list them.

## Queries

### myUsageMetricSubmissions

The usage-metric submissions reported for the caller's own workspace, newest first by default.

  **The workspace is not an argument.** It is the one the caller's token names; a token carrying no
  workspace claim is refused.

```graphql
myUsageMetricSubmissions(
    filter: BillingUsageMetricSubmissionFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: BillingUsageMetricSubmissionOrder = { field: SUBMITTED_AT, direction: DESC }
  ): BillingUsageMetricSubmissionConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `BillingUsageMetricSubmissionFilter` | Filtering options for the returned submissions. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `BillingUsageMetricSubmissionOrder` | The ordering options for the returned submissions. |

**Input types:**

<details>

<summary>BillingUsageMetricSubmissionFilter</summary>

Filtering options for the caller's own usage-metric submissions. Fields combine with AND. No workspace field: the workspace is the one the token names.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `metricId` | `ID` | Filter by the metric the usage was reported against. |
| `productDomainId` | `ID` | Filter by the product domain the usage belongs to. |
| `accounted` | `Boolean` | Filter by invoicing state: `false` = unaccounted (not yet rolled into an invoice), `true` = accounted (already invoiced), omitted = both. |
| `submittedAtFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Return submissions whose `submittedAt` is at or after this timestamp (inclusive). |
| `submittedAtTo` | [DateTime](../../core-api-reference/common.md#datetime) | Return submissions whose `submittedAt` is at or before this timestamp (inclusive). |
| `periodStartFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Return submissions whose `periodStart` is at or after this timestamp (inclusive). A value after `periodStartTo` matches nothing. |
| `periodStartTo` | [DateTime](../../core-api-reference/common.md#datetime) | Return submissions whose `periodStart` is at or before this timestamp (inclusive). |
| `metricValueMin` | `Float` | Return submissions whose `metricValue` is at least this value (inclusive). A value above `metricValueMax` matches nothing. |
| `metricValueMax` | `Float` | Return submissions whose `metricValue` is at most this value (inclusive). For whole counts, `metricValueMax: 9` means fewer than 10. |

</details>

<details>

<summary>BillingUsageMetricSubmissionOrder</summary>

Ordering options for usage-metric submissions.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [BillingUsageMetricSubmissionOrderField](#billingusagemetricsubmissionorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>BillingUsageMetricSubmissionConnection</summary>

A paginated list of BillingUsageMetricSubmission items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[BillingUsageMetricSubmissionEdge](#billingusagemetricsubmissionedge)!]! | A list of edges. |
| `nodes` | [[BillingUsageMetricSubmission](#billingusagemetricsubmission)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### myInvoiceLineItemSubmissions

The invoice-line-item submissions reported for the caller's own workspace, newest first by default.

  **The workspace is not an argument.** It is the one the caller's token names; a token carrying no
  workspace claim is refused.

```graphql
myInvoiceLineItemSubmissions(
    filter: BillingInvoiceLineItemSubmissionFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: BillingInvoiceLineItemSubmissionOrder = { field: SUBMITTED_AT, direction: DESC }
  ): BillingInvoiceLineItemSubmissionConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `BillingInvoiceLineItemSubmissionFilter` | Filtering options for the returned submissions. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `BillingInvoiceLineItemSubmissionOrder` | The ordering options for the returned submissions. |

**Input types:**

<details>

<summary>BillingInvoiceLineItemSubmissionFilter</summary>

Filtering options for the caller's own invoice-line-item submissions. Fields combine with AND. No workspace field: the workspace is the one the token names.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `productDomainId` | `ID` | Filter by the product domain the line item belongs to. |
| `accounted` | `Boolean` | Filter by invoicing state: `false` = unaccounted (not yet rolled into an invoice), `true` = accounted (already invoiced), omitted = both. |
| `submittedAtFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Return submissions whose `submittedAt` is at or after this timestamp (inclusive). |
| `submittedAtTo` | [DateTime](../../core-api-reference/common.md#datetime) | Return submissions whose `submittedAt` is at or before this timestamp (inclusive). |

</details>

<details>

<summary>BillingInvoiceLineItemSubmissionOrder</summary>

Ordering options for invoice-line-item submissions.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [BillingInvoiceLineItemSubmissionOrderField](#billinginvoicelineitemsubmissionorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>BillingInvoiceLineItemSubmissionConnection</summary>

A paginated list of BillingInvoiceLineItemSubmission items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[BillingInvoiceLineItemSubmissionEdge](#billinginvoicelineitemsubmissionedge)!]! | A list of edges. |
| `nodes` | [[BillingInvoiceLineItemSubmission](#billinginvoicelineitemsubmission)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Objects

<a id="billingusagemetricsubmission"></a>

### BillingUsageMetricSubmission

Raw usage measurement submitted by a product service. Aggregated later into invoice line items.

**Implements:** [Node](../../core-api-reference/common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `workspaceId` | `ID!` | The workspace the usage belongs to. |
| `productDomain` | [BillingProductDomain](plan.md#billingproductdomain)! | The product domain the usage was reported for. |
| `metric` | [BillingMetric](plan.md#billingmetric)! | The metric whose value is reported. |
| `externalId` | `String` | External idempotency key (per producer scope). |
| `metricValue` | `Float!` | The metric value reported for the period. |
| `periodStart` | [DateTime](../../core-api-reference/common.md#datetime)! | Inclusive lower bound of the reporting window. |
| `periodEnd` | [DateTime](../../core-api-reference/common.md#datetime)! | Exclusive upper bound of the reporting window. |
| `pricingDimensions` | [JSON](../../core-api-reference/common.md#json) | Pricing dimension values used for VOLUME pricing tier resolution (key/value JSON). |
| `usageModifiers` | [JSON](../../core-api-reference/common.md#json) | Modifier values used for FLAT pricing multipliers (key/value JSON). |
| `extraData` | [JSON](../../core-api-reference/common.md#json) | Arbitrary justification metadata; not regulated. |
| `invoiceId` | `ID` | Identifier of the invoice this submission has been claimed by; null while in the unbilled pool. |
| `invoicedAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the submission was claimed onto an invoice; null iff invoiceId is null. |
| `submittedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When billing accepted the submission, from the server clock. A producer cannot supply its own. |
| `submittedBy` | `String!` | The actor that sent the submission, or `system` when billing could not attribute it. Resolved server-side; a producer cannot supply its own. |

---

<a id="billinginvoicelineitemsubmission"></a>

### BillingInvoiceLineItemSubmission

Pre-built invoice line item submitted externally (no aggregation, one row = one line).

**Implements:** [Node](../../core-api-reference/common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `workspaceId` | `ID!` | The workspace the line item belongs to. |
| `productDomain` | [BillingProductDomain](plan.md#billingproductdomain)! | The product domain the line item was reported for. |
| `externalId` | `String` | External idempotency key (per producer scope). |
| `description` | `String!` | Human-readable line description; the invoice shows it followed by the billing period the line belongs to. |
| `quantity` | `Float!` | Quantity reported for the period. |
| `unitPrice` | [Money](../../core-api-reference/common.md#money)! | Unit price for this line. |
| `amount` | [Money](../../core-api-reference/common.md#money) | Optional total amount; if null, the invoicer derives it from quantity * unitPrice. |
| `periodStart` | [DateTime](../../core-api-reference/common.md#datetime)! | Inclusive lower bound of the reporting window. |
| `periodEnd` | [DateTime](../../core-api-reference/common.md#datetime)! | Exclusive upper bound of the reporting window. |
| `extraData` | [JSON](../../core-api-reference/common.md#json) | Arbitrary justification metadata; not regulated. |
| `invoiceId` | `ID` | Identifier of the invoice this submission has been claimed by; null while in the unbilled pool. |
| `invoicedAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the submission was claimed onto an invoice; null iff invoiceId is null. |
| `submittedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When billing accepted the submission, from the server clock. A producer cannot supply its own. |
| `submittedBy` | `String!` | The actor that sent the submission, or `system` when billing could not attribute it. Resolved server-side; a producer cannot supply its own. |

---

## Inputs

<a id="billingusagemetricsubmissionfilter"></a>

### BillingUsageMetricSubmissionFilter

Filtering options for the caller's own usage-metric submissions. Fields combine with AND. No workspace field: the workspace is the one the token names.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `metricId` | `ID` | Filter by the metric the usage was reported against. |
| `productDomainId` | `ID` | Filter by the product domain the usage belongs to. |
| `accounted` | `Boolean` | Filter by invoicing state: `false` = unaccounted (not yet rolled into an invoice), `true` = accounted (already invoiced), omitted = both. |
| `submittedAtFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Return submissions whose `submittedAt` is at or after this timestamp (inclusive). |
| `submittedAtTo` | [DateTime](../../core-api-reference/common.md#datetime) | Return submissions whose `submittedAt` is at or before this timestamp (inclusive). |
| `periodStartFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Return submissions whose `periodStart` is at or after this timestamp (inclusive). A value after `periodStartTo` matches nothing. |
| `periodStartTo` | [DateTime](../../core-api-reference/common.md#datetime) | Return submissions whose `periodStart` is at or before this timestamp (inclusive). |
| `metricValueMin` | `Float` | Return submissions whose `metricValue` is at least this value (inclusive). A value above `metricValueMax` matches nothing. |
| `metricValueMax` | `Float` | Return submissions whose `metricValue` is at most this value (inclusive). For whole counts, `metricValueMax: 9` means fewer than 10. |

---

<a id="billingusagemetricsubmissionorder"></a>

### BillingUsageMetricSubmissionOrder

Ordering options for usage-metric submissions.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [BillingUsageMetricSubmissionOrderField](#billingusagemetricsubmissionorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

<a id="billinginvoicelineitemsubmissionfilter"></a>

### BillingInvoiceLineItemSubmissionFilter

Filtering options for the caller's own invoice-line-item submissions. Fields combine with AND. No workspace field: the workspace is the one the token names.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `productDomainId` | `ID` | Filter by the product domain the line item belongs to. |
| `accounted` | `Boolean` | Filter by invoicing state: `false` = unaccounted (not yet rolled into an invoice), `true` = accounted (already invoiced), omitted = both. |
| `submittedAtFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Return submissions whose `submittedAt` is at or after this timestamp (inclusive). |
| `submittedAtTo` | [DateTime](../../core-api-reference/common.md#datetime) | Return submissions whose `submittedAt` is at or before this timestamp (inclusive). |

---

<a id="billinginvoicelineitemsubmissionorder"></a>

### BillingInvoiceLineItemSubmissionOrder

Ordering options for invoice-line-item submissions.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [BillingInvoiceLineItemSubmissionOrderField](#billinginvoicelineitemsubmissionorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

## Enums

<a id="billingusagemetricsubmissionorderfield"></a>

### BillingUsageMetricSubmissionOrderField

Fields available for ordering usage-metric submissions.

| Value | Description |
| ----- | ----------- |
| `SUBMITTED_AT` | Order by when billing recorded the submission. |
| `PERIOD_START` | Order by the start of the usage period. |
| `VALUE` | Order by the reported metric value. |
| `METRIC` | Order by the code of the referenced metric. |

---

<a id="billinginvoicelineitemsubmissionorderfield"></a>

### BillingInvoiceLineItemSubmissionOrderField

Fields available for ordering invoice-line-item submissions.

| Value | Description |
| ----- | ----------- |
| `SUBMITTED_AT` | Order by when billing recorded the submission. |
| `PERIOD_START` | Order by the start of the billing period. |

---

## Pagination types

<a id="billingusagemetricsubmissionconnection"></a>

### BillingUsageMetricSubmissionConnection

A paginated list of BillingUsageMetricSubmission items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[BillingUsageMetricSubmissionEdge](#billingusagemetricsubmissionedge)!]! | A list of edges. |
| `nodes` | [[BillingUsageMetricSubmission](#billingusagemetricsubmission)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="billingusagemetricsubmissionedge"></a>

### BillingUsageMetricSubmissionEdge

An edge in the BillingUsageMetricSubmission connection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [BillingUsageMetricSubmission](#billingusagemetricsubmission)! | The usage-metric submission at the end of the edge. |

---

<a id="billinginvoicelineitemsubmissionconnection"></a>

### BillingInvoiceLineItemSubmissionConnection

A paginated list of BillingInvoiceLineItemSubmission items.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[BillingInvoiceLineItemSubmissionEdge](#billinginvoicelineitemsubmissionedge)!]! | A list of edges. |
| `nodes` | [[BillingInvoiceLineItemSubmission](#billinginvoicelineitemsubmission)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="billinginvoicelineitemsubmissionedge"></a>

### BillingInvoiceLineItemSubmissionEdge

An edge in the BillingInvoiceLineItemSubmission connection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [BillingInvoiceLineItemSubmission](#billinginvoicelineitemsubmission)! | The invoice-line-item submission at the end of the edge. |

---

## See also

* [Pagination](../../pagination.md)
* [Filtering and sorting](../../filtering-and-sorting/README.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
