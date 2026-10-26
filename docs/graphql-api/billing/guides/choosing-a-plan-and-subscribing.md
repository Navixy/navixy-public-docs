---
description: >-
  List the plans your workspace may subscribe to and what each one charges,
  subscribe your workspace to the plan you choose, and confirm the
  subscription.
tags:
  - guide
  - beta
---

# Choosing a plan and subscribing

This guide shows how to choose a plan for your workspace and subscribe to it through the Billing API. You will list the plans open to your workspace and read what each one costs and includes, then subscribe your workspace and confirm the result.

Every operation on this page reads or changes the data of the workspace named by your access token. You never pass a workspace ID, so a request can't subscribe a workspace that the token doesn't name.

## Prerequisites

You need an access token whose `workspace_id` claim names the workspace. The Billing [my\* operations](../README.md#purpose-and-capabilities), the operations named with the prefix `my` such as [myAvailablePlans](../api-reference/plan.md#myavailableplans) and [mySubscribe](../api-reference/subscription.md#mysubscribe), read only that claim. Without it, they refuse the request and don't fall back to the workspace of your single membership. See [Authentication](../../authentication.md#how-the-api-picks-your-workspace) to learn how the API identifies the workspace.

To check that the token names a workspace and that Billing accepts it, run this query and list the subscriptions that the workspace already has:

```graphql
query CurrentSubscriptions {
  billing {
    mySubscriptions(filter: { status: ACTIVE }, first: 10) {
      nodes {
        id
        planId
        branchId
        subscribedAt
        trialExpiresAt
      }
      total {
        count
      }
    }
  }
}
```

Response for a workspace that has no subscription:

```json
{
  "data": {
    "billing": {
      "mySubscriptions": {
        "nodes": [],
        "total": { "count": 0 }
      }
    }
  }
}
```

An empty list is a valid response. A `PERMISSION_DENIED` error instead means that the token has no workspace claim. See [Error handling](#error-handling).

## How plans and subscriptions work

This guide uses 2 important concepts: plans and subscriptions. [Plans](../api-reference/plan.md) and [Subscriptions](../api-reference/subscription.md) document every field.

A plan is what a workspace pays for: a fee per billing period, plus a price for the usage of each metered product. A plan may have branches, which are variants of the plan with their own price. Billing offers a branch as its own row, next to the base plan or in its place, so you choose a row, not a plan and then a branch. [myAvailablePlans](../api-reference/plan.md#myavailableplans) lists every row currently open to your workspace. An archived plan, a plan outside its active dates, or a plan reserved for other workspaces won't be there.

A subscription is the link between a workspace and one row: the plan and the branch when the row is a branch. A subscription is `ACTIVE` until it is cancelled. Cancelling and switching aren't part of this API. Contact Navixy at [sales@navixy.com](mailto:sales@navixy.com) to perform them.

## Example scenario: A fleet operator moves to a larger plan

TransLog GmbH runs delivery trucks out of Hamburg. On July 1, 2026, their workspace has no active subscription: Navixy cancelled the Basic plan that the workspace started on, at TransLog's request. TransLog has more devices than Basic includes, so their administrator chooses a plan that covers more devices through the API. To subscribe the workspace, follow these steps:

{% stepper %}
{% step %}

### Read the plans open to the workspace

[myAvailablePlans](../api-reference/plan.md#myavailableplans) returns every row the workspace may subscribe to, with the subscription that the workspace already has on each. The list isn't paginated, because a workspace sees a small catalogue.

Run this query:

```graphql
query AvailablePlans {
  billing {
    myAvailablePlans {
      planId
      branchId
      title
      description
      currency
      periodFee {
        amount
        currency
      }
      trialDays
      items {
        planItemId
        branchItemId
        metric {
          code
          title
          unit
        }
        aggregationFunction
        includedLimit
        quota
        warningThresholds
        pricingModel
        usagePricing {
          price
          tiers {
            from
            to
            price
          }
          pricingDimension
        }
      }
      subscription {
        id
        status
        subscribedAt
        trialExpiresAt
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "billing": {
      "myAvailablePlans": [
        {
          "planId": "019a6b2f-793e-807b-8001-42504c4e0001",
          "branchId": null,
          "title": "Basic",
          "description": "Business Data Repository and Tracking for a small fleet.",
          "currency": "USD",
          "periodFee": { "amount": 0, "currency": "USD" },
          "trialDays": null,
          "items": [
            {
              "planItemId": "019a6b2f-793e-807b-8001-425049540001",
              "branchItemId": null,
              "metric": { "code": "registered_devices", "title": "Registered devices", "unit": "count" },
              "aggregationFunction": "MAX",
              "includedLimit": 5,
              "quota": 5,
              "warningThresholds": [80, 100],
              "pricingModel": "SUBSCRIPTION",
              "usagePricing": { "price": null, "tiers": null, "pricingDimension": null }
            }
          ],
          "subscription": null
        },
        {
          "planId": "019a6b2f-793e-807b-8001-42504c4e0002",
          "branchId": null,
          "title": "Fleet Pro",
          "description": "Business Data Repository and Tracking for a growing fleet, billed per device.",
          "currency": "USD",
          "periodFee": { "amount": 4900, "currency": "USD" },
          "trialDays": 14,
          "items": [
            {
              "planItemId": "019a6b2f-793e-807b-8001-425049540002",
              "branchItemId": null,
              "metric": { "code": "registered_devices", "title": "Registered devices", "unit": "count" },
              "aggregationFunction": "MAX",
              "includedLimit": 10,
              "quota": 500,
              "warningThresholds": [80, 100],
              "pricingModel": "FLAT",
              "usagePricing": { "price": 300, "tiers": null, "pricingDimension": null }
            },
            {
              "planItemId": "019a6b2f-793e-807b-8001-425049540003",
              "branchItemId": null,
              "metric": { "code": "registered_assets", "title": "Registered assets", "unit": "count" },
              "aggregationFunction": "MAX",
              "includedLimit": null,
              "quota": null,
              "warningThresholds": null,
              "pricingModel": "SUBSCRIPTION",
              "usagePricing": { "price": null, "tiers": null, "pricingDimension": null }
            }
          ],
          "subscription": null
        },
        {
          "planId": "019a6b2f-793e-807b-8001-42504c4e0002",
          "branchId": "019a6b2f-793e-807b-8001-4242524e0001",
          "title": "Fleet Pro, large fleet",
          "description": "Fleet Pro for fleets of more than 50 devices, with 50 devices included in the fee.",
          "currency": "USD",
          "periodFee": { "amount": 14900, "currency": "USD" },
          "trialDays": 14,
          "items": [
            {
              "planItemId": "019a6b2f-793e-807b-8001-425049540002",
              "branchItemId": "019a6b2f-793e-807b-8001-424252490001",
              "metric": { "code": "registered_devices", "title": "Registered devices", "unit": "count" },
              "aggregationFunction": "MAX",
              "includedLimit": 50,
              "quota": 1000,
              "warningThresholds": [80, 100],
              "pricingModel": "FLAT",
              "usagePricing": { "price": 200, "tiers": null, "pricingDimension": null }
            }
          ],
          "subscription": null
        }
      ]
    }
  }
}
```

The workspace sees 3 rows: the Basic plan, the Fleet Pro plan, and one branch of Fleet Pro for large fleets. A branch row has the same `planId` as its base plan and its own `branchId`. No row has a `subscription`, because the workspace has no active subscription. When it has one, that row has a `subscription`.
{% endstep %}

{% step %}

### Render each row with its price and limits

Read what each row costs and what it includes. Every amount of a row is in the row's `currency`. These fields set the price, and the items set the limits:

- `periodFee` is the fee per billing period, in the minor units of `currency`. Every billing period is one month. Divide by 100 for a currency with 2 decimal places: `4900` is $49.00. This value is the sum of the fees of every product in the plan. When a branch sets its own fee for a product, that fee replaces the base plan's fee for the product.
- `trialDays` is the length of the trial that a new subscription gets. A trial is granted once per plan: a workspace that already had a trial on Fleet Pro or on one of its branches will resubscribe without a trial.
- Each entry of `items` prices one metric. `includedLimit` is the usage included in the fee. `quota` is a soft limit on usage: usage may exceed it, and Billing doesn't block it. `warningThresholds` lists the percentages of the quota that quota warnings use.
- `pricingModel` says how usage above the included limit is priced. `SUBSCRIPTION` means the fee covers it and there is no per-unit charge. `FLAT` charges `usagePricing.price` per unit, in minor units: in the example, $3.00 per device above the 10 included. `TIERED` and `VOLUME` price usage by `usagePricing.tiers`. [Plans](../api-reference/plan.md#billingpricingmodel) define each model.

In the example, Basic includes 5 devices at no charge, with a quota of 5. Fleet Pro costs $49.00 per month, includes 10 devices, and charges $3.00 for each device above that, with a quota of 500. Its fee also covers registered assets, with no per-unit charge.

The large fleet branch costs $149.00 per month, includes 50 devices, and charges $2.00 for each device above that, with a quota of 1,000. The branch row lists only the items that the branch changes, so it shows the device item alone. A subscription to the branch uses the branch's fee and device item, and the base plan's asset item. For a fleet of more than 43 devices, the branch costs less than the base plan.

Billing applies the fee and the prices on the invoice of each billing period: the fee, and the usage above the included limit at the plan's price. The invoice total is charged to the balance. See [Reading invoices](reading-invoices.md) to learn what an invoice charges. The quota doesn't limit usage: Billing accepts usage above the quota, and the Billing API returns no warning when usage reaches a threshold. See [Coming soon](../README.md#coming-soon) on the overview.
{% endstep %}

{% step %}

### Subscribe your workspace to the plan you chose

[mySubscribe](../api-reference/subscription.md#mysubscribe) subscribes the workspace named by the token. The input takes the row's `planId` and, for a branch row, its `branchId`. Leave `branchId` out to subscribe to the base plan.

TransLog has 11 devices. On the base plan, that's $49.00 plus $3.00 for 1 device above the included 10, which costs less than the branch. TransLog chooses the Fleet Pro row, so the mutation leaves out `branchId`. Run this mutation:

```graphql
mutation SubscribeToFleetPro {
  billing {
    mySubscribe(input: { planId: "019a6b2f-793e-807b-8001-42504c4e0002" }) {
      subscription {
        id
        workspaceId
        planId
        branchId
        status
        subscribedAt
        expiresAt
        trialExpiresAt
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "billing": {
      "mySubscribe": {
        "subscription": {
          "id": "019a6b2f-793e-807b-8001-425355420002",
          "workspaceId": "019a6b2f-793e-807b-8001-575350430001",
          "planId": "019a6b2f-793e-807b-8001-42504c4e0002",
          "branchId": null,
          "status": "ACTIVE",
          "subscribedAt": "2026-07-01T08:12:40Z",
          "expiresAt": null,
          "trialExpiresAt": "2026-07-15T08:12:40Z"
        }
      }
    }
  }
}
```

The subscription starts at once and has a trial that ends 14 days later. `expiresAt` is the end date of the branch when the subscription is on a branch that has one, otherwise null. The trial makes the fee free until July 15, so the first invoice, issued on August 1, charges the fee only for the part of July after the trial. See [Reading invoices](reading-invoices.md) to read that invoice.

Before Billing accepts the subscription, it checks the row. The plan, or the branch, must be one that [myAvailablePlans](../api-reference/plan.md#myavailableplans) offers the workspace. The branch must belong to the plan. And no product of the plan may be covered by an active workspace subscription. A workspace has one active subscription per product, and this API doesn't replace one. Read the rows from `myAvailablePlans` in the same session and send `planId` and `branchId` as they came, so that the first 2 checks pass. For the third, see [Error handling](#error-handling).

{% hint style="warning" %}
The workspace claim names the workspace, and the roles of the token must give access to the Billing product. No finer role is checked: any such token can subscribe the workspace to a paid plan. Show the plan page only to the people who may subscribe the workspace to a plan.
{% endhint %}
{% endstep %}

{% step %}

### Confirm the subscription

Read the active workspace subscriptions again. The new subscription is first, because the list is ordered by `SUBSCRIBED_AT` descending by default:

```graphql
query ConfirmSubscription {
  billing {
    mySubscriptions(filter: { status: ACTIVE }, first: 10) {
      nodes {
        id
        planId
        branchId
        status
        subscribedAt
        trialExpiresAt
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "billing": {
      "mySubscriptions": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-425355420002",
            "planId": "019a6b2f-793e-807b-8001-42504c4e0002",
            "branchId": null,
            "status": "ACTIVE",
            "subscribedAt": "2026-07-01T08:12:40Z",
            "trialExpiresAt": "2026-07-15T08:12:40Z"
          }
        ]
      }
    }
  }
}
```

The new subscription is the only active one. Run [myAvailablePlans](../api-reference/plan.md#myavailableplans) again, and the Fleet Pro row now has a `subscription`, while the Basic row and the branch row have none. A subscription to the base plan doesn't mark the branch row as taken.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
Your workspace is subscribed to the plan you chose. You listed the rows it may take, read the price and the limits of each one, and subscribed with one mutation. Everything happened in the workspace named by your token.
{% endhint %}

## Listing past subscriptions

[mySubscriptions](../api-reference/subscription.md#mysubscriptions) returns cancelled subscriptions too. Omit the `status` filter to list both, or filter by `planId` to show the history of one plan:

```graphql
query SubscriptionHistory {
  billing {
    mySubscriptions(
      filter: { planId: "019a6b2f-793e-807b-8001-42504c4e0001" }
      orderBy: { field: SUBSCRIBED_AT, direction: ASC }
      first: 20
    ) {
      nodes {
        id
        status
        subscribedAt
        cancelledAt
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "billing": {
      "mySubscriptions": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-425355420001",
            "status": "CANCELLED",
            "subscribedAt": "2026-06-15T09:30:12Z",
            "cancelledAt": "2026-06-30T16:03:00Z"
          }
        ]
      }
    }
  }
}
```

The filter has 2 fields, `planId` and `status`, and they combine with AND. The list orders by `SUBSCRIBED_AT` only. See [Pagination](../../pagination.md) for the cursor rules.

`mySubscriptions` returns subscriptions that [myAvailablePlans](../api-reference/plan.md#myavailableplans) doesn't show. A subscription to a plan that was archived later, or whose active dates have passed, is absent from the catalogue but still in effect. To show what the workspace is on, read `mySubscriptions`, not the `subscription` field of the catalogue rows.

## Error handling

The Billing operations return errors in the shared format. See [Error handling](../../error-handling.md) for the response structure and the general codes.

| Code                | When it occurs                                                                                                                                                                                                                              | Resolution                                                                                                                                             |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `PERMISSION_DENIED` | The token has no `workspace_id` claim, or the claim isn't a workspace ID.                                                                                                                                                                   | Request a token issued for the workspace. The Billing `my*` operations don't use your memberships.                                                     |
| `VALIDATION_ERROR`  | The plan or the branch isn't a row that [myAvailablePlans](../api-reference/plan.md#myavailableplans) offers the workspace: it is archived, outside its active dates, reserved for other workspaces, or the branch belongs to another plan. | Read `myAvailablePlans` again and send `planId` and `branchId` from one of its rows.                                                                   |
| `VALIDATION_ERROR`  | A product of the plan is covered by an active subscription of the workspace. This API doesn't replace it.                                                                                                                                   | Read the active subscription from [mySubscriptions](../api-reference/subscription.md#mysubscriptions). To cancel or switch it, contact Navixy at [sales@navixy.com](mailto:sales@navixy.com). |
| `VALIDATION_ERROR`  | The `workspace_id` claim names a workspace that this deployment doesn't have.                                                                                                                                                               | Request a token for a workspace of this deployment.                                                                                                    |

When an active subscription already covers a product of the plan, the mutation returns this error. For example, a second subscription to Fleet Pro, while the subscription from the scenario is active, returns this error:

```json
{
  "errors": [{
    "message": "Cannot subscribe: product domains conflict with existing active subscriptions [019a6b2f-793e-807b-8001-425355420002]",
    "path": ["billing", "mySubscribe"],
    "extensions": {
      "type": "https://api.navixy.com/errors/validation",
      "title": "Validation Error",
      "status": 400,
      "detail": "Cannot subscribe: product domains conflict with existing active subscriptions [019a6b2f-793e-807b-8001-425355420002]",
      "instance": "/graphql",
      "code": "VALIDATION_ERROR",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-07-01T08:15:00Z"
    }
  }],
  "data": { "billing": { "mySubscribe": null } }
}
```

### How to handle a product conflict

Read the subscription IDs in `detail` and look them up in [mySubscriptions](../api-reference/subscription.md#mysubscriptions). Your workspace already has a subscription that covers a product of the plan you chose. To move to the new plan, contact Navixy at [sales@navixy.com](mailto:sales@navixy.com): switching and cancelling a subscription aren't in this API. To avoid the error, check `mySubscriptions(filter: { status: ACTIVE })` before you subscribe.

## See also

- [Plans](../api-reference/plan.md): Learn how the rows of the catalogue, their items, and the pricing vocabulary work.
- [Subscriptions](../api-reference/subscription.md): Learn how [mySubscribe](../api-reference/subscription.md#mysubscribe), [mySubscriptions](../api-reference/subscription.md#mysubscriptions), and the subscription type work.
- [Reading balance and statement](reading-balance-and-statement.md): Read the workspace's prepaid balance and the entries that changed it.
- [Billing errors](../errors.md): Learn how the Billing operations report a missing entity and which fields their errors have.
- [Authentication](../../authentication.md): Learn how the token names the workspace that every operation acts in.
