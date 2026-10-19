---
description: >-
  Reference for workspace subscriptions: the mySubscribe mutation, the
  mySubscriptions query, and the subscription, filter, and order types they use.
tags:
  - reference
  - beta
---

# Subscriptions

The subscriptions of the caller's workspace to plans and branches: how to subscribe, how to list them, and the types they return.

## Queries

### mySubscriptions

The caller's own workspace's subscriptions, most recent first.

  Answers where `myAvailablePlans` cannot: a subscription to a plan that has since been archived, or
  whose active window closed, or whose target list no longer includes the workspace, is absent from
  the catalogue but still in effect. This is the query for "what am I on".

```graphql
mySubscriptions(
    filter: BillingWorkspaceSubscriptionFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: BillingSubscriptionOrder = { field: SUBSCRIBED_AT, direction: DESC }
  ): BillingWorkspaceSubscriptionConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `BillingWorkspaceSubscriptionFilter` | Filtering options for the returned subscriptions. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | `BillingSubscriptionOrder` | The ordering options for the returned subscriptions. |

**Input types:**

<details>

<summary>BillingWorkspaceSubscriptionFilter</summary>

Filtering options for the caller's own subscriptions. Fields combine with AND.

No workspace field: the workspace is the caller's own and is never accepted from outside.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `planId` | `ID` | Filter by plan. |
| `status` | [BillingSubscriptionStatus](#billingsubscriptionstatus) | Filter by status. Omitted returns both active and cancelled. |

</details>

<details>

<summary>BillingSubscriptionOrder</summary>

Ordering options for subscriptions.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [BillingSubscriptionOrderField](#billingsubscriptionorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>BillingWorkspaceSubscriptionConnection</summary>

A page of the caller's own subscriptions.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[BillingWorkspaceSubscriptionEdge](#billingworkspacesubscriptionedge)!]! | A list of edges. |
| `nodes` | [[BillingWorkspaceSubscription](#billingworkspacesubscription)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Mutations

### mySubscribe

Subscribes the caller's own workspace to a plan, optionally on a branch.

  **The workspace is not in the input.** It is the one the caller's token names, so this mutation
  cannot put a tenant the token does not name onto a paid plan. A token carrying no workspace claim
  is refused.

  With it disabled, any token naming a workspace can put that workspace on a paid plan.

  The plan is validated before the subscription is taken: the plan, or the branch, must be a row
  that `myAvailablePlans` offers the workspace; the branch must belong to the plan; and no product
  of the plan may be covered by one of the workspace's active subscriptions. A token whose workspace
  claim names no workspace in this deployment fails with `VALIDATION_ERROR`. A workspace gets at
  most one trial per plan: once a subscription to the plan or to one of its branches has carried a
  trial, later ones start without.

```graphql
mySubscribe(
    input: BillingMySubscribeInput!
  ): BillingWorkspaceSubscribePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `BillingMySubscribeInput!` | The plan, and optionally the branch. The workspace is the caller's own and is not passed in. |

**Input types:**

<details>

<summary>BillingMySubscribeInput</summary>

Input for subscribing the caller's own workspace to a plan.

It has no workspace field: the workspace is the one the caller's token names.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `planId` | `ID!` | The plan to subscribe to. |
| `branchId` | `ID` | Optional plan branch (null = subscribe to the base plan). |

</details>

**Output types:**

<details>

<summary>BillingWorkspaceSubscribePayload</summary>

What `mySubscribe` answers with.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `subscription` | [BillingWorkspaceSubscription](#billingworkspacesubscription)! | The subscription as taken. |

</details>

<details>

<summary>BillingWorkspaceSubscription (entity)</summary>

A workspace's own view of one of its subscriptions: the plan and branch as ids, and the dates.

Not a `Node`. Read a subscription through `mySubscriptions`. `myAvailablePlans` shows an active
subscription on the row of its exact plan and branch, for as long as the catalogue still offers
that row.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The identifier of the subscription. |
| `workspaceId` | `ID!` | The workspace holding it. |
| `planId` | `ID!` | The plan subscribed to. |
| `branchId` | `ID` | The branch, when the subscription is to one rather than to the base plan. |
| `status` | [BillingSubscriptionStatus](#billingsubscriptionstatus)! | Current lifecycle status. |
| `subscribedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the workspace subscribed. |
| `expiresAt` | [DateTime](../../core-api-reference/common.md#datetime) | When it expires; null while it does not. |
| `cancelledAt` | [DateTime](../../core-api-reference/common.md#datetime) | When it was cancelled; null while it is in effect. |
| `trialExpiresAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the trial granted on it expires; null when no trial was granted. |

</details>

---

## Objects

<a id="billingworkspacesubscription"></a>

### BillingWorkspaceSubscription

A workspace's own view of one of its subscriptions: the plan and branch as ids, and the dates.

Not a `Node`. Read a subscription through `mySubscriptions`. `myAvailablePlans` shows an active
subscription on the row of its exact plan and branch, for as long as the catalogue still offers
that row.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The identifier of the subscription. |
| `workspaceId` | `ID!` | The workspace holding it. |
| `planId` | `ID!` | The plan subscribed to. |
| `branchId` | `ID` | The branch, when the subscription is to one rather than to the base plan. |
| `status` | [BillingSubscriptionStatus](#billingsubscriptionstatus)! | Current lifecycle status. |
| `subscribedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When the workspace subscribed. |
| `expiresAt` | [DateTime](../../core-api-reference/common.md#datetime) | When it expires; null while it does not. |
| `cancelledAt` | [DateTime](../../core-api-reference/common.md#datetime) | When it was cancelled; null while it is in effect. |
| `trialExpiresAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the trial granted on it expires; null when no trial was granted. |

---

<a id="billingworkspacesubscribepayload"></a>

### BillingWorkspaceSubscribePayload

What `mySubscribe` answers with.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `subscription` | [BillingWorkspaceSubscription](#billingworkspacesubscription)! | The subscription as taken. |

---

## Inputs

<a id="billingworkspacesubscriptionfilter"></a>

### BillingWorkspaceSubscriptionFilter

Filtering options for the caller's own subscriptions. Fields combine with AND.

No workspace field: the workspace is the caller's own and is never accepted from outside.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `planId` | `ID` | Filter by plan. |
| `status` | [BillingSubscriptionStatus](#billingsubscriptionstatus) | Filter by status. Omitted returns both active and cancelled. |

---

<a id="billingmysubscribeinput"></a>

### BillingMySubscribeInput

Input for subscribing the caller's own workspace to a plan.

It has no workspace field: the workspace is the one the caller's token names.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `planId` | `ID!` | The plan to subscribe to. |
| `branchId` | `ID` | Optional plan branch (null = subscribe to the base plan). |

---

<a id="billingsubscriptionorder"></a>

### BillingSubscriptionOrder

Ordering options for subscriptions.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [BillingSubscriptionOrderField](#billingsubscriptionorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

## Enums

<a id="billingsubscriptionstatus"></a>

### BillingSubscriptionStatus

Lifecycle status of a workspace's subscription.

| Value | Description |
| ----- | ----------- |
| `ACTIVE` | The subscription is currently in effect. |
| `CANCELLED` | The subscription has been cancelled (replaced by another or stopped explicitly). |

---

<a id="billingsubscriptionorderfield"></a>

### BillingSubscriptionOrderField

Fields available for ordering subscriptions.

| Value | Description |
| ----- | ----------- |
| `SUBSCRIBED_AT` | Order by when the workspace subscribed. |

---

## Pagination types

<a id="billingworkspacesubscriptionconnection"></a>

### BillingWorkspaceSubscriptionConnection

A page of the caller's own subscriptions.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[BillingWorkspaceSubscriptionEdge](#billingworkspacesubscriptionedge)!]! | A list of edges. |
| `nodes` | [[BillingWorkspaceSubscription](#billingworkspacesubscription)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="billingworkspacesubscriptionedge"></a>

### BillingWorkspaceSubscriptionEdge

An edge in the BillingWorkspaceSubscription connection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [BillingWorkspaceSubscription](#billingworkspacesubscription)! | The subscription at the end of the edge. |

---

## See also

* [Pagination](../../pagination.md)
* [Filtering and sorting](../../filtering-and-sorting/README.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
