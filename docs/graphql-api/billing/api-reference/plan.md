---
description: >-
  Reference for the plans a workspace may subscribe to: the myAvailablePlans
  query, the BillingAvailablePlan and BillingAvailablePlanItem types, and the
  product domains, metrics, and pricing vocabulary a plan is built from.
tags:
  - reference
  - beta
---

# Plans

The plans and branches the caller's workspace may subscribe to, with the subscription it already holds on each, and the catalog and pricing vocabulary a plan is built from: product domains, metrics, pricing models, and tiers.

## Queries

### myAvailablePlans

Lists the plans and branches the caller's own workspace may subscribe to, with the subscription it
  already holds on each.

  **The workspace is not an argument.** It is the one the caller's token names, so this operation
  cannot be pointed at a tenant the token does not name. A token carrying no workspace claim is
  refused rather than answered: see [Authentication](../../authentication.md).

  Unpaginated: the answer is bounded by the catalogue a single workspace can see.

```graphql
myAvailablePlans: [BillingAvailablePlan!]!
```

**Output types:**

<details>

<summary>BillingAvailablePlan</summary>

A plan or branch available to a workspace. When branchId is null this is a base plan; when set it represents a branch override.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `planId` | `ID!` | The base plan ID. |
| `branchId` | `ID` | The branch ID (null for base plans). |
| `title` | `String!` | Human-readable title. |
| `description` | `String` | Optional description. |
| `currency` | [Currency](../../core-api-reference/common.md#currency)! | [ISO 4217](https://www.iso.org/standard/4217.html) currency. |
| `periodFee` | [Money](../../core-api-reference/common.md#money)! | What the plan charges per billing period regardless of usage, in `currency`. The sum of its per-schema fees — a branch's overrides where it sets them, the base plan's fee elsewhere. |
| `trialDays` | `Int` | Must be a positive integer if provided; null means no trial. |
| `items` | [[BillingAvailablePlanItem](#billingavailableplanitem)!]! | Pricing items. For base plans — plan items; for branches — branch override items. |
| `subscription` | [BillingWorkspaceSubscription](subscription.md#billingworkspacesubscription) | The workspace's own subscription to this exact plan and branch, or null when it holds none. Only an `ACTIVE` subscription counts: a cancelled one must not render as subscribed. Matched on the pair — a subscription to a branch does not mark the base plan as taken, and the reverse. |

</details>

---

## Objects

<a id="billingproductdomain"></a>

### BillingProductDomain

A billable product in the billing catalog.

**Implements:** [Node](../../core-api-reference/common.md#node), [Versioned](../../core-api-reference/common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. |
| `code` | [Code](../../core-api-reference/common.md#code)! | Machine-readable product code. |
| `title` | `String` | Optional human-readable title. |
| `description` | `String` | Optional description. |
| `isActive` | `Boolean!` | Whether this product is active. |
| `isBillable` | `Boolean!` | Whether this product may take part in plans. True by default. When false, no new plan may include the product. Plans that already include it stay in force, but an update that resends their pricing schemas is refused. |
| `productUrl` | `String` | The product's user-facing address: an absolute http or https URL. Null when not set. |
| `internalPath` | `String` | The product's internal path: an absolute http or https URL, or a path starting with a single '/'. Null when not set. |

---

<a id="billingmetric"></a>

### BillingMetric

A billable metric.

**Implements:** [Node](../../core-api-reference/common.md#node), [Versioned](../../core-api-reference/common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. |
| `code` | [Code](../../core-api-reference/common.md#code)! | Machine-readable metric code. |
| `title` | `String` | Optional human-readable title. |
| `description` | `String` | Optional description. |
| `unit` | `String` | Optional measurement unit (for example: count, GB, requests). |
| `isActive` | `Boolean!` | Whether this metric is active. |

---

<a id="billingusagepricing"></a>

### BillingUsagePricing

Final usage pricing for a plan item. Fields depend on the parent pricingModel.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `price` | [Long](../../core-api-reference/common.md#long) | Price per unit in minor currency units (FLAT model). |
| `tiers` | [[BillingUsagePricingTier](#billingusagepricingtier)!] | Pricing tiers (TIERED and VOLUME models). |
| `pricingDimension` | `String` | Attribute used to select the pricing tier, for example: device_count (VOLUME). |
| `pricingDimensionAggregation` | [BillingAggregationFunction](#billingaggregationfunction) | How pricing dimension values are aggregated (VOLUME). |

---

<a id="billingusagepricingtier"></a>

### BillingUsagePricingTier

A tier in a plan's tiered or volume pricing.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `from` | `Float!` | Lower bound of the tier (inclusive). |
| `to` | `Float` | Upper bound of the tier: exclusive for TIERED, where the next tier starts, and inclusive for VOLUME; null means unlimited. |
| `price` | [Long](../../core-api-reference/common.md#long)! | Price for this tier in minor currency units. |

---

<a id="billingavailableplan"></a>

### BillingAvailablePlan

A plan or branch available to a workspace. When branchId is null this is a base plan; when set it represents a branch override.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `planId` | `ID!` | The base plan ID. |
| `branchId` | `ID` | The branch ID (null for base plans). |
| `title` | `String!` | Human-readable title. |
| `description` | `String` | Optional description. |
| `currency` | [Currency](../../core-api-reference/common.md#currency)! | [ISO 4217](https://www.iso.org/standard/4217.html) currency. |
| `periodFee` | [Money](../../core-api-reference/common.md#money)! | What the plan charges per billing period regardless of usage, in `currency`. The sum of its per-schema fees — a branch's overrides where it sets them, the base plan's fee elsewhere. |
| `trialDays` | `Int` | Must be a positive integer if provided; null means no trial. |
| `items` | [[BillingAvailablePlanItem](#billingavailableplanitem)!]! | Pricing items. For base plans — plan items; for branches — branch override items. |
| `subscription` | [BillingWorkspaceSubscription](subscription.md#billingworkspacesubscription) | The workspace's own subscription to this exact plan and branch, or null when it holds none. Only an `ACTIVE` subscription counts: a cancelled one must not render as subscribed. Matched on the pair — a subscription to a branch does not mark the base plan as taken, and the reverse. |

---

<a id="billingavailableplanitem"></a>

### BillingAvailablePlanItem

A pricing item in the available-plans list. For base plan items branchItemId is null; for branch overrides it is set and planItemId references the base plan item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `planItemId` | `ID!` | The base plan item ID. |
| `branchItemId` | `ID` | The branch item ID (null for base plan items). |
| `metric` | [BillingMetric](#billingmetric)! | The metric being priced. |
| `aggregationFunction` | [BillingAggregationFunction](#billingaggregationfunction)! | How metric values are aggregated. |
| `includedLimit` | `Float` | Free usage included in the period fee. |
| `quota` | `Float` | Soft usage limit. |
| `warningThresholds` | `[Int!]` | Percentage thresholds for quota warnings. |
| `pricingModel` | [BillingPricingModel](#billingpricingmodel)! | The pricing model. |
| `usagePricing` | [BillingUsagePricing](#billingusagepricing)! | Usage pricing details (base or custom override). |

---

## Enums

<a id="billingaggregationfunction"></a>

### BillingAggregationFunction

Function used to aggregate metric values over a billing period.

**Values:** `SUM`, `MAX`, `MIN`, `AVG`, `LAST`, `MEDIAN`

---

<a id="billingpricingmodel"></a>

### BillingPricingModel

The pricing model for a metric.

| Value | Description |
| ----- | ----------- |
| `FLAT` | Fixed price per unit. |
| `TIERED` | Usage is split across tiers; each tier has its own price. |
| `VOLUME` | All usage is priced at the tier the total falls into. |
| `SUBSCRIPTION` | Included in the subscription fee; no per-unit charge. |

---

## See also

* [Pagination](../../pagination.md)
* [Filtering and sorting](../../filtering-and-sorting/README.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
