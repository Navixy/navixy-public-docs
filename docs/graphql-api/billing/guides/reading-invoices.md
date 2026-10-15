---
description: >-
  List your workspace's invoices, read the lines of one invoice, match it to
  the balance statement, and find the invoices that are still unpaid.
tags:
  - guide
  - beta
---

# Reading invoices

This guide shows how to read your workspace's invoices through the Billing API. You will list the invoices, read one invoice with its lines, and find the balance statement entry that charged it. You will also learn how an invoice becomes paid or overdue, and what to do with an overdue invoice.

Every operation on this page acts in the workspace named by your access token. You never pass a workspace ID, so a request can't read the invoices of another workspace.

## Prerequisites

You need an access token whose `workspace_id` claim names the workspace. The Billing [my\* operations](../README.md#purpose-and-capabilities), the operations named with the prefix `my` such as [myInvoices](../api-reference/invoice.md#myinvoices) and [myInvoice](../api-reference/invoice.md#myinvoice), read only that claim. Without it, they refuse the request and don't fall back to the workspace of your single membership. See [Authentication](../../authentication.md#how-the-api-picks-your-workspace) to learn how the API identifies the workspace.

To check that the token names a workspace and that Billing accepts it, count the workspace's invoices with this query:

```graphql
query InvoiceCheck {
  billing {
    myInvoices(first: 1) {
      total {
        count
      }
    }
  }
}
```

Response for a workspace that has 3 invoices:

```json
{
  "data": {
    "billing": {
      "myInvoices": {
        "total": { "count": 3 }
      }
    }
  }
}
```

A count of zero is a valid response: it means that Billing hasn't issued any invoice for the workspace. A `PERMISSION_DENIED` error instead means that the token has no workspace claim. See [Error handling](#error-handling).

## How invoices work

An invoice charges a workspace for billing periods that ended. The sections below explain what one invoice covers, how its total is charged to the balance, and how its status changes. [Invoices](../api-reference/invoice.md) documents every field.

### What an invoice covers

A billing period is one month of a subscription. Each period starts at 00:00 UTC on the day of the month on which the subscription started. If a month has no such day, for example the 31st in September, the period starts on that month's last day. A subscription made on July 1 has periods from the 1st of each month to the 1st of the next.

Once a day, shortly after midnight UTC, Billing checks each workspace for billing periods that ended. This guide calls that check the daily pass. Each daily pass writes at most one invoice for your workspace. The invoice covers every period of every workspace subscription that ended and that no earlier invoice covered. When Navixy cancels a subscription, Billing doesn't wait for the daily pass: it invoices the subscription's last period right away, and that period ends at the cancellation.

`periodStart` and `periodEnd` span all the periods that the invoice has lines for. The start is inclusive and the end is exclusive.

### What the lines are

The lines of an invoice are grouped by subscription and period. Each line description ends with the period that the line belongs to, for example `, 2026-09-01 – 2026-09-30`. Each period has fee lines, metric lines, and reported charge lines:

* One fee line for each product of the plan, named `Subscription: <plan> (<product code>)`. Its `quantity` is the share of the period that the subscription paid for: `1` for a full period, less for a period in which the subscription started or was cancelled. A trial is free, so only the part of the period after the trial counts.
* One metric line for each metric of the plan, even when nothing was used. Its `quantity` is the usage above the included limit, combined over the period by the metric's aggregation function. Usage is charged in full during a trial too.
* One reported charge line for each charge that a Navixy product reported for the period. A reported charge is a ready-made line with its own description, quantity, and unit price, and the invoice shows it as reported. [myInvoiceLineItemSubmissions](../api-reference/submission.md#myinvoicelineitemsubmissions) lists these charges.

`amount` is `quantity` times `unitPrice`, rounded half up to the minor unit. A reported charge that has its own amount keeps that amount. `subtotal` is the sum of the lines. Billing writes `priceModify` as zero, so `total` equals `subtotal`.

### What happens to usage reported late

A product can report usage for a period after the daily pass that invoiced that period. Billing charges that usage in the next daily pass. When no billing period ended that day, the late usage is on an invoice of its own.

Billing prices the period's usage again, with the late usage included. When an amount changes, the invoice gets a line that starts with `Correction:` and names the earlier period. Its amount is the difference between the new amount and the amount already invoiced, so it's negative when the late usage lowers a charge. The invoice's `periodStart` is then the start of the earliest period that it corrects. A reported charge that arrives late is on the next invoice, as reported.

When the late usage changes no amount, Billing adds no line. It puts the usage on the invoice that charged its period, so the usage's `invoicedAt` is later than that invoice's `issuedAt`.

### How the total is charged to the balance

Billing charges the total to your workspace's balance when it issues the invoice. The charge is an `INVOICE` entry on the [balance statement](reading-balance-and-statement.md), dated at `issuedAt`, with the invoice number as its `description`. An invoice with a total of zero adds no entry. An invoice with a total below zero, from corrections that lower an earlier charge, adds money to the balance.

### How the status changes

Billing writes 3 of the 5 values of [BillingInvoiceStatus](../api-reference/invoice.md#billinginvoicestatus): `ISSUED`, `PAID`, and `OVERDUE`. It never sets `DRAFT` or `VOID`.

| Status    | When Billing sets it                                                                                                                                                                                                 |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `PAID`    | When Billing issues the invoice, if the balance after the charge is zero or more. Later, when a payment or a correction that adds money to the balance covers the invoice. An invoice with a total of zero or less is always `PAID`. |
| `ISSUED`  | When Billing issues the invoice, if the charge leaves the balance below zero.                                                                                                                                        |
| `OVERDUE` | At the first check at or after `dueAt`, if the invoice is still unpaid.                                                                                                                                              |

Billing checks the status of unpaid invoices in every daily pass, after every entry that adds money to the balance, and after it issues an invoice.

Payments aren't linked to invoices: a payment is added to the balance. Billing pays the oldest invoices first. As a result, the newest unpaid invoice becomes `PAID` only when the balance is zero or more. `paidAt` is the moment of that change. It's the moment Navixy entered the payment, not the payment's value date. A `PAID` invoice stays `PAID`.

`dueAt` is the end of the grace period: the time that you have to pay before the invoice becomes `OVERDUE`. The grace period starts at the daily pass that issued the invoice. For an invoice issued on cancellation, it starts at the cancellation.

## Example scenario: An operator checks the latest invoice

TransLog GmbH runs delivery trucks out of Hamburg. Their workspace has been on the Fleet Pro plan since July 1, 2026, with a 14-day trial. The plan costs $49.00 per month, includes 10 registered devices, and charges $3.00 for each device above that. Registered assets are included in the fee. The Navixy platform reports each month's device and asset counts at 00:02 UTC on the first day of the next month, before the daily pass, so each invoice charges the month that just ended.

On October 6, their administrator reads the workspace's invoices and checks what the latest invoice, issued on October 1, charged for. To check the latest invoice, follow these steps:

{% stepper %}
{% step %}
### List the invoices

[myInvoices](../api-reference/invoice.md#myinvoices) returns your workspace's invoices, newest first by default. Ask for the number, the period, the total, and the dates that set the status.

Run this query:

```graphql
query Invoices {
  billing {
    myInvoices(first: 20) {
      nodes {
        id
        invoiceNumber
        periodStart
        periodEnd
        total {
          amount
          currency
        }
        status
        issuedAt
        dueAt
        paidAt
      }
      pageInfo {
        hasNextPage
        endCursor
      }
      total {
        count
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
      "myInvoices": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-42494e560003",
            "invoiceNumber": "INV-2026-000132",
            "periodStart": "2026-09-01T00:00:00Z",
            "periodEnd": "2026-10-01T00:00:00Z",
            "total": { "amount": 5800, "currency": "USD" },
            "status": "ISSUED",
            "issuedAt": "2026-10-01T00:05:02Z",
            "dueAt": "2026-10-15T00:05:00Z",
            "paidAt": null
          },
          {
            "id": "019a6b2f-793e-807b-8001-42494e560002",
            "invoiceNumber": "INV-2026-000087",
            "periodStart": "2026-08-01T00:00:00Z",
            "periodEnd": "2026-09-01T00:00:00Z",
            "total": { "amount": 5500, "currency": "USD" },
            "status": "PAID",
            "issuedAt": "2026-09-01T00:05:03Z",
            "dueAt": "2026-09-15T00:05:00Z",
            "paidAt": "2026-09-01T00:05:03Z"
          },
          {
            "id": "019a6b2f-793e-807b-8001-42494e560001",
            "invoiceNumber": "INV-2026-000041",
            "periodStart": "2026-07-01T00:00:00Z",
            "periodEnd": "2026-08-01T00:00:00Z",
            "total": { "amount": 2933, "currency": "USD" },
            "status": "PAID",
            "issuedAt": "2026-08-01T00:05:02Z",
            "dueAt": "2026-08-15T00:05:00Z",
            "paidAt": "2026-08-01T00:05:02Z"
          }
        ],
        "pageInfo": {
          "hasNextPage": false,
          "endCursor": "aW52b2ljZTowMDAx"
        },
        "total": { "count": 3 }
      }
    }
  }
}
```

The workspace has one invoice for each month since July. `INV-2026-000041`, for July, is $29.33. The trial made the first 2 weeks free, so the fee is $26.33, a share of 0.537351 of the $49.00 fee. The other $3.00 is for 1 device above the included 10.

The 2 older invoices were `PAID` when Billing issued them: `paidAt` equals `issuedAt`, because the balance covered them. `INV-2026-000132` is `ISSUED`, because its charge left the balance below zero. It becomes `OVERDUE` if it's still unpaid at the first check on or after October 15, 00:05 UTC.

Request the next page with `after: endCursor` while `hasNextPage` is true. Pass the cursor back exactly as received. See [Pagination](../../pagination.md) for the cursor rules.

* `invoiceNumber` is the number to show people and to quote to Navixy. It has the form `INV-<year>-<number>`. All workspaces share one sequence of numbers, so your invoice numbers aren't consecutive.
* `total` is in minor units, like every amount in Billing, so $58.00 is returned as `5800`.
* `issuedAt` sets the place of an invoice in the list. Invoices with the same `issuedAt` are ordered by `id`, so a page stays stable across cursors.
{% endstep %}

{% step %}
### Read one invoice with its lines

[myInvoice](../api-reference/invoice.md#myinvoice) returns one of your workspace's invoices by its ID, with its lines. Use the `id` of `INV-2026-000132` from the list.

Run this query:

```graphql
query LatestInvoice {
  billing {
    myInvoice(id: "019a6b2f-793e-807b-8001-42494e560003") {
      invoiceNumber
      subtotal {
        amount
        currency
      }
      priceModify {
        amount
        currency
      }
      total {
        amount
        currency
      }
      lineItems {
        id
        description
        quantity
        unitPrice {
          amount
          currency
        }
        amount {
          amount
          currency
        }
        productDomain {
          code
        }
        metric {
          code
          title
          unit
        }
        subscriptionId
        planId
        periodStart
        periodEnd
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
      "myInvoice": {
        "invoiceNumber": "INV-2026-000132",
        "subtotal": { "amount": 5800, "currency": "USD" },
        "priceModify": { "amount": 0, "currency": "USD" },
        "total": { "amount": 5800, "currency": "USD" },
        "lineItems": [
          {
            "id": "019a6b2f-793e-807b-8001-424c49540007",
            "description": "Subscription: Fleet Pro (bdr), 2026-09-01 – 2026-09-30",
            "quantity": 1.0,
            "unitPrice": { "amount": 4900, "currency": "USD" },
            "amount": { "amount": 4900, "currency": "USD" },
            "productDomain": { "code": "bdr" },
            "metric": null,
            "subscriptionId": "019a6b2f-793e-807b-8001-425355420002",
            "planId": "019a6b2f-793e-807b-8001-42504c4e0002",
            "periodStart": "2026-09-01T00:00:00Z",
            "periodEnd": "2026-10-01T00:00:00Z"
          },
          {
            "id": "019a6b2f-793e-807b-8001-424c49540008",
            "description": "Registered devices, 2026-09-01 – 2026-09-30",
            "quantity": 3.0,
            "unitPrice": { "amount": 300, "currency": "USD" },
            "amount": { "amount": 900, "currency": "USD" },
            "productDomain": { "code": "bdr" },
            "metric": { "code": "registered_devices", "title": "Registered devices", "unit": "count" },
            "subscriptionId": "019a6b2f-793e-807b-8001-425355420002",
            "planId": "019a6b2f-793e-807b-8001-42504c4e0002",
            "periodStart": "2026-09-01T00:00:00Z",
            "periodEnd": "2026-10-01T00:00:00Z"
          },
          {
            "id": "019a6b2f-793e-807b-8001-424c49540009",
            "description": "Registered assets (included), 2026-09-01 – 2026-09-30",
            "quantity": 10.0,
            "unitPrice": { "amount": 0, "currency": "USD" },
            "amount": { "amount": 0, "currency": "USD" },
            "productDomain": { "code": "bdr" },
            "metric": { "code": "registered_assets", "title": "Registered assets", "unit": "count" },
            "subscriptionId": "019a6b2f-793e-807b-8001-425355420002",
            "planId": "019a6b2f-793e-807b-8001-42504c4e0002",
            "periodStart": "2026-09-01T00:00:00Z",
            "periodEnd": "2026-10-01T00:00:00Z"
          }
        ]
      }
    }
  }
}
```

The invoice charges September: the $49.00 fee, and 3 devices above the included 10 at $3.00 each. The Navixy platform reported 13 devices and 10 assets for September at 00:02 UTC on October 1, so the daily pass at 00:05 put both counts on this invoice. The asset line ends with `(included)`, because the fee covers assets.

`myInvoice` returns `null` when no invoice has this ID or when the invoice belongs to another workspace. The 2 cases look the same, so an ID tells you nothing about other workspaces.
{% endstep %}

{% step %}
### Render each line

Use these fields to show a line:

* `description` is the text to show. It ends with the line's period: the first and the last day, in UTC. Don't parse it: read the period from `periodStart` and `periodEnd`, and the metric from `metric`.
* `metric` is null on a fee line and on a reported charge line. Use it to tell a metric line from the others, and show `metric.unit` next to `quantity`.
* `quantity` on a fee line is a share of the period, with up to 6 decimal places, not a count. Show it as a fraction or a percentage. On a metric line, it's the usage above the included limit, in `metric.unit`.
* `unitPrice` and `amount` are in minor units, in the invoice currency. A correction line has a negative amount when late usage lowers a charge.
* `subscriptionId` is the subscription that the line charges for, the `id` that [mySubscriptions](../api-reference/subscription.md#mysubscriptions) returns. `planId` is the plan of that subscription. When the subscription is on a branch, `planId` is the base plan's ID, and the branch set the fee and the prices.

With `TIERED` pricing, a metric has one line for each tier that the usage reached. When `pricingModel` is `SUBSCRIPTION`, the metric line ends with `(included)`, its `quantity` is all the usage, and its unit price is zero. The [Plans](../api-reference/plan.md#billingpricingmodel) reference defines each pricing model.
{% endstep %}

{% step %}
### Find the charge on the balance statement

The invoice total is an `INVOICE` entry on the statement. Read the entries since the invoice was issued with [myBalanceEntries](../api-reference/balance.md#mybalanceentries):

```graphql
query OctoberStatement {
  billing {
    myBalanceEntries(filter: { createdAtFrom: "2026-10-01T00:00:00Z" }, first: 20) {
      nodes {
        createdAt
        type
        amount {
          amount
          currency
        }
        balanceAfter {
          amount
          currency
        }
        description
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
      "myBalanceEntries": {
        "nodes": [
          {
            "createdAt": "2026-10-01T00:05:02Z",
            "type": "INVOICE",
            "amount": { "amount": -5800, "currency": "USD" },
            "balanceAfter": { "amount": -3333, "currency": "USD" },
            "description": "INV-2026-000132"
          }
        ]
      }
    }
  }
}
```

The entry has the date of `issuedAt`, the total as a negative amount, and the invoice number. Use the number to link the statement line to the invoice. The balance after the charge is -$33.33. TransLog paid $100.00 in July and received a $9.00 credit in September, and the 3 invoices charged $142.33 in all.

To have `INV-2026-000132` marked `PAID`, the workspace has to pay at least $33.33. Navixy enters the payment from the bank transfer, and the invoice becomes `PAID` at that moment. [Reading balance and statement](reading-balance-and-statement.md) shows such a payment on the statement.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You listed your workspace's invoices, read the lines of one invoice, and found the statement entry that charged it. Everything acted in the workspace named by your token.
{% endhint %}

## Finding unpaid invoices

An unpaid invoice is `ISSUED` or `OVERDUE`. The `status` filter takes one value, so request each status under its own alias in one query. See [Aliases](../../graphql-basics/graphql-tips-and-patterns.md#aliases) to learn how aliases work. Run this query:

```graphql
query UnpaidInvoices {
  billing {
    issued: myInvoices(filter: { status: ISSUED }, first: 20) {
      nodes {
        invoiceNumber
        total {
          amount
        }
        dueAt
      }
      total {
        count
      }
    }
    overdue: myInvoices(filter: { status: OVERDUE }, first: 20) {
      nodes {
        invoiceNumber
        total {
          amount
        }
        dueAt
      }
      total {
        count
      }
    }
  }
}
```

Response, on October 6:

```json
{
  "data": {
    "billing": {
      "issued": {
        "nodes": [
          {
            "invoiceNumber": "INV-2026-000132",
            "total": { "amount": 5800 },
            "dueAt": "2026-10-15T00:05:00Z"
          }
        ],
        "total": { "count": 1 }
      },
      "overdue": {
        "nodes": [],
        "total": { "count": 0 }
      }
    }
  }
}
```

`INV-2026-000132` is unpaid, and no invoice of TransLog is overdue. If `INV-2026-000132` is still unpaid at the first check on or after October 15, 00:05 UTC, it moves from the `issued` list to the `overdue` list.

### How to handle an overdue invoice

Read the balance with [myBalance](../api-reference/balance.md#mybalance). A negative amount is what the workspace owes, and it can be more than one invoice. Pay Navixy at least that amount: when the balance is zero or more, every unpaid invoice becomes `PAID`. A smaller payment covers the oldest invoices first. See [Reading balance and statement](reading-balance-and-statement.md) to follow the payment on the statement.

Billing doesn't block a workspace with an overdue invoice. Whether the workspace keeps working is decided outside Billing.

## Listing invoices by issue date

To show the invoices issued in a period, filter by `issuedFrom` and `issuedTo`. Both bounds are inclusive and apply to `issuedAt`. To list the oldest invoice first, set `orderBy` to `{ field: ISSUED_AT, direction: ASC }`. `ISSUED_AT` is the only sort field. Run this query:

```graphql
query ThirdQuarterInvoices {
  billing {
    myInvoices(
      filter: {
        issuedFrom: "2026-07-01T00:00:00Z"
        issuedTo: "2026-09-30T23:59:59Z"
      }
      orderBy: { field: ISSUED_AT, direction: ASC }
      first: 20
    ) {
      nodes {
        invoiceNumber
        total {
          amount
        }
        status
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
      "myInvoices": {
        "nodes": [
          {
            "invoiceNumber": "INV-2026-000041",
            "total": { "amount": 2933 },
            "status": "PAID"
          },
          {
            "invoiceNumber": "INV-2026-000087",
            "total": { "amount": 5500 },
            "status": "PAID"
          }
        ]
      }
    }
  }
}
```

The response has `INV-2026-000041` and `INV-2026-000087`, oldest first. `INV-2026-000132` isn't in it, because Billing issued it on October 1. The filter fields combine with AND, so add `status` to narrow the list further.

## Error handling

The Billing operations return errors in the shared format. See [Error handling](../../error-handling.md) for the response structure and the general codes.

| Code                | When it occurs                                                            | Resolution                                                                                         |
| ------------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `PERMISSION_DENIED` | The token has no `workspace_id` claim, or the claim isn't a workspace ID. | Request a token issued for the workspace. The Billing `my*` operations don't use your memberships. |
| `VALIDATION_ERROR`  | The `id` of `myInvoice` isn't an invoice ID.                              | Read `field` and `detail`. Take the ID from `myInvoices`.                                          |
| `VALIDATION_ERROR`  | `first` is above 100.                                                     | Send `first` of 100 or less.                                                                       |
| `VALIDATION_ERROR`  | `after` or `before` isn't a valid cursor.                                 | Start the list again without a cursor.                                                             |

An invoice that doesn't exist and an invoice of another workspace return no error. `myInvoice` returns `null` for both:

```json
{
  "data": {
    "billing": {
      "myInvoice": null
    }
  }
}
```

### How to handle a null invoice

Read [myInvoices](../api-reference/invoice.md#myinvoices) again and take the `id` from it. A token for another workspace lists other invoices, so check that the token names the workspace that the invoice belongs to.

### How to handle a missing workspace claim

A request without a workspace claim returns `PERMISSION_DENIED` on every Billing field that it selects. Request a token issued for the workspace whose invoices you read. The Billing `my*` operations don't fall back to the workspace of your single membership. See [Billing errors](../errors.md#how-to-handle-a-missing-workspace-claim) for the error response.

## See also

* [Invoices](../api-reference/invoice.md): Learn how the invoice, the line, the filter, and the queries work.
* [Reading balance and statement](reading-balance-and-statement.md): Read the balance that the invoices are charged to, and the payments that cover them.
* [Reading usage submissions](reading-usage-submissions.md): List the usage that the metric lines of an invoice price.
* [Choosing a plan and subscribing](choosing-a-plan-and-subscribing.md): See the fee, the included usage, and the prices that an invoice applies.
* [Billing errors](../errors.md): Learn how the Billing operations report a missing entity and which fields their errors have.
* [Pagination](../../pagination.md): Page through a long list of invoices with cursors.
