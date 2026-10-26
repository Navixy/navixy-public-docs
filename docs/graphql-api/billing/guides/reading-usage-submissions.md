---
description: >-
  List the usage that Navixy products reported for your workspace, read what
  each submission says, and narrow the list to one metric or one period.
tags:
  - guide
  - beta
---

# Reading usage submissions

This guide shows how to read the usage reported for your workspace through the Billing API. You will list your workspace's usage submissions, read what each submission says, and narrow the list to one metric or one period.

Every operation on this page reads the data of the workspace named by your access token. You never pass a workspace ID, so a request can't read the usage of another workspace.

## Prerequisites

You need an access token whose `workspace_id` claim names the workspace. The Billing [my\* operations](../README.md#purpose-and-capabilities), the operations named with the prefix `my` such as [myUsageMetricSubmissions](../api-reference/submission.md#myusagemetricsubmissions), read only that claim. Without it, they refuse the request and don't fall back to the workspace of your single membership. See [Authentication](../../authentication.md#how-the-api-picks-your-workspace) to learn how the API identifies the workspace.

To check that the token names a workspace and that Billing accepts it, count the workspace's usage submissions with this query:

```graphql
query UsageCount {
  billing {
    myUsageMetricSubmissions(first: 1) {
      total {
        count
      }
    }
  }
}
```

Response for a workspace with 3 reported months:

```json
{
  "data": {
    "billing": {
      "myUsageMetricSubmissions": {
        "total": { "count": 6 }
      }
    }
  }
}
```

A count of zero is a valid response: it means that no usage was reported for the workspace. A `PERMISSION_DENIED` error instead means that the token has no workspace claim. See [Error handling](#error-handling).

## How usage submissions work

A usage submission is one value of one metric for one period, reported to Billing for your workspace. Navixy products report usage to Billing outside this API, and Billing accepts a submission only for a period that a subscription of the workspace covers. [Submissions](../api-reference/submission.md) documents every field.

The Navixy platform reports 2 metrics for every workspace whose subscription covers Business Data Repository, once for each closed calendar month. `registered_devices` is the number of GPS devices registered in the workspace at the end of the month, and `registered_assets` is the number of assets. The period of such a submission is the calendar month, cut to the moment the subscription started or was cancelled in its first and last month. Its `submittedBy` is `system`, because no person sent it. Until Navixy turns on usage metering for your deployment, the list is empty.

A submission is never changed. A product may report a second submission for the same period, and the platform reports each monthly count once.

Once a day, shortly after midnight UTC, Billing invoices the billing periods that ended. See [What an invoice covers](reading-invoices.md#what-an-invoice-covers) to learn which periods an invoice covers. This daily invoicing run puts a submission on an invoice once the submission has arrived and the billing period that contains the submission's end is over. From then on, `invoiceId` names that invoice, and `invoicedAt` is when Billing put the submission on it. Until then, both are null. See [Reading invoices](reading-invoices.md) to learn how the invoice prices the usage.

## Example scenario: An operator checks the reported device count

TransLog GmbH runs delivery trucks out of Hamburg. Their workspace has been on the Fleet Pro plan since July 1, 2026, and the plan charges per registered device. On October 6, their administrator checks what the Navixy platform reported for July, August, and September to compare it with the fleet list. To check the reported usage, follow these steps:

{% stepper %}
{% step %}

### List the usage reported for the workspace

[myUsageMetricSubmissions](../api-reference/submission.md#myusagemetricsubmissions) lists the workspace's usage submissions, newest first by default. Ask for the metric, the value, the period, and when Billing accepted the submission.

Run this query:

```graphql
query Usage {
  billing {
    myUsageMetricSubmissions(first: 20) {
      nodes {
        id
        metric {
          code
          title
          unit
        }
        productDomain {
          code
          title
        }
        metricValue
        periodStart
        periodEnd
        submittedAt
        submittedBy
        invoiceId
        invoicedAt
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
      "myUsageMetricSubmissions": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-42554d530006",
            "metric": { "code": "registered_devices", "title": "Registered devices", "unit": "count" },
            "productDomain": { "code": "bdr", "title": "Business Data Repository" },
            "metricValue": 13.0,
            "periodStart": "2026-09-01T00:00:00Z",
            "periodEnd": "2026-10-01T00:00:00Z",
            "submittedAt": "2026-10-01T00:02:09Z",
            "submittedBy": "system",
            "invoiceId": "019a6b2f-793e-807b-8001-42494e560003",
            "invoicedAt": "2026-10-01T00:05:02Z"
          },
          {
            "id": "019a6b2f-793e-807b-8001-42554d530005",
            "metric": { "code": "registered_assets", "title": "Registered assets", "unit": "count" },
            "productDomain": { "code": "bdr", "title": "Business Data Repository" },
            "metricValue": 10.0,
            "periodStart": "2026-09-01T00:00:00Z",
            "periodEnd": "2026-10-01T00:00:00Z",
            "submittedAt": "2026-10-01T00:02:09Z",
            "submittedBy": "system",
            "invoiceId": "019a6b2f-793e-807b-8001-42494e560003",
            "invoicedAt": "2026-10-01T00:05:02Z"
          },
          {
            "id": "019a6b2f-793e-807b-8001-42554d530004",
            "metric": { "code": "registered_devices", "title": "Registered devices", "unit": "count" },
            "productDomain": { "code": "bdr", "title": "Business Data Repository" },
            "metricValue": 12.0,
            "periodStart": "2026-08-01T00:00:00Z",
            "periodEnd": "2026-09-01T00:00:00Z",
            "submittedAt": "2026-09-01T00:02:11Z",
            "submittedBy": "system",
            "invoiceId": "019a6b2f-793e-807b-8001-42494e560002",
            "invoicedAt": "2026-09-01T00:05:03Z"
          },
          {
            "id": "019a6b2f-793e-807b-8001-42554d530003",
            "metric": { "code": "registered_assets", "title": "Registered assets", "unit": "count" },
            "productDomain": { "code": "bdr", "title": "Business Data Repository" },
            "metricValue": 9.0,
            "periodStart": "2026-08-01T00:00:00Z",
            "periodEnd": "2026-09-01T00:00:00Z",
            "submittedAt": "2026-09-01T00:02:11Z",
            "submittedBy": "system",
            "invoiceId": "019a6b2f-793e-807b-8001-42494e560002",
            "invoicedAt": "2026-09-01T00:05:03Z"
          },
          {
            "id": "019a6b2f-793e-807b-8001-42554d530002",
            "metric": { "code": "registered_devices", "title": "Registered devices", "unit": "count" },
            "productDomain": { "code": "bdr", "title": "Business Data Repository" },
            "metricValue": 11.0,
            "periodStart": "2026-07-01T08:12:40Z",
            "periodEnd": "2026-08-01T00:00:00Z",
            "submittedAt": "2026-08-01T00:02:07Z",
            "submittedBy": "system",
            "invoiceId": "019a6b2f-793e-807b-8001-42494e560001",
            "invoicedAt": "2026-08-01T00:05:02Z"
          },
          {
            "id": "019a6b2f-793e-807b-8001-42554d530001",
            "metric": { "code": "registered_assets", "title": "Registered assets", "unit": "count" },
            "productDomain": { "code": "bdr", "title": "Business Data Repository" },
            "metricValue": 9.0,
            "periodStart": "2026-07-01T08:12:40Z",
            "periodEnd": "2026-08-01T00:00:00Z",
            "submittedAt": "2026-08-01T00:02:07Z",
            "submittedBy": "system",
            "invoiceId": "019a6b2f-793e-807b-8001-42494e560001",
            "invoicedAt": "2026-08-01T00:05:02Z"
          }
        ],
        "pageInfo": {
          "hasNextPage": false,
          "endCursor": "dXNhZ2U6MDAwMQ=="
        },
        "total": { "count": 6 }
      }
    }
  }
}
```

The workspace has 6 submissions: 2 metrics for each of July, August, and September. October isn't there, because the month isn't over. The platform reported 11 devices at the end of July, 12 at the end of August, and 13 at the end of September. The July submissions start at 08:12:40, the moment the subscription started.

Each month's counts arrived at 00:02 UTC on the first day of the next month. The daily invoicing run at 00:05 the same day put them on that day's invoice, so `invoicedAt` is a few minutes after `submittedAt`. The September counts are on `INV-2026-000132`, issued on October 1. [Reading invoices](reading-invoices.md) shows that invoice line by line.

Request the next page with `after: endCursor` while `hasNextPage` is true. See [Pagination](../../pagination.md) for the cursor rules.
{% endstep %}

{% step %}

### Read a submission

These fields describe a submission:

- `metric` names what was measured. Use `code`, such as `registered_devices`, to match the submission to your plan: the items of a plan in [myAvailablePlans](../api-reference/plan.md#myavailableplans) name their metric with the same code. Show `title` and `unit` to people: `title` is the metric's name, such as "Registered devices", and `unit` is what the value counts, such as `count`.
- `metricValue` is the reported value, in the metric's `unit`. For the 2 platform metrics, it's a count taken at the end of the period.
- `periodStart` and `periodEnd` limit the period. The start is inclusive and the end is exclusive, so `2026-08-01T00:00:00Z` to `2026-09-01T00:00:00Z` is all of August.
- `submittedAt` is the time when Billing accepted the submission, from the server clock. For a monthly count, it's shortly after the month is over.
- `submittedBy` is the actor that sent the submission, or `system` when no person did. On the monthly counts of devices and assets, it's `system`, because the Navixy platform reports them automatically.

`invoiceId` is the ID of the invoice that charged the submission. Read that invoice with [myInvoice](../api-reference/invoice.md#myinvoice). `invoicedAt` is when Billing put the submission on the invoice. Both are null until an invoice charges the submission.
{% endstep %}

{% step %}

### Show one metric month by month

To follow one metric over time, filter by its ID and order by the period start. `metricId` is the metric's `id`. The list in the first step returns it when you ask for `metric { id }`. Run this query:

```graphql
query DevicesByMonth {
  billing {
    myUsageMetricSubmissions(
      filter: { metricId: "019a6b2f-793e-807b-8001-424d45540001" }
      orderBy: { field: PERIOD_START, direction: ASC }
      first: 12
    ) {
      nodes {
        periodStart
        metricValue
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
      "myUsageMetricSubmissions": {
        "nodes": [
          { "periodStart": "2026-07-01T08:12:40Z", "metricValue": 11.0 },
          { "periodStart": "2026-08-01T00:00:00Z", "metricValue": 12.0 },
          { "periodStart": "2026-09-01T00:00:00Z", "metricValue": 13.0 }
        ]
      }
    }
  }
}
```

The filter has 9 fields, and they combine with AND: `metricId`, `productDomainId`, `accounted`, `submittedAtFrom`, `submittedAtTo`, `periodStartFrom`, `periodStartTo`, `metricValueMin`, and `metricValueMax`. All the `From`, `To`, `Min`, and `Max` fields are inclusive, so `metricValueMax: 9` keeps the whole counts below 10. A reversed range, such as `periodStartFrom` after `periodStartTo`, isn't an error: it matches nothing and returns an empty page. `accounted: true` keeps the submissions that are on an invoice, and `accounted: false` keeps the submissions that no invoice charged. See [Finding usage that no invoice charged](#finding-usage-that-no-invoice-charged) for an example. The list can be sorted by `SUBMITTED_AT`, `PERIOD_START`, `VALUE`, or `METRIC`.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You listed the usage reported for your workspace, read what each submission says, and followed one metric month by month. Everything acted in the workspace named by your token.
{% endhint %}

## Finding usage that no invoice charged

`accounted: false` keeps the submissions that no invoice charged, so their `invoiceId` is null. Use it to show the usage that Billing charges on a later invoice. Run this query:

```graphql
query UsageNotInvoiced {
  billing {
    myUsageMetricSubmissions(filter: { accounted: false }, first: 20) {
      nodes {
        metric {
          code
        }
        metricValue
        periodStart
        periodEnd
        submittedAt
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
      "myUsageMetricSubmissions": {
        "nodes": [],
        "total": { "count": 0 }
      }
    }
  }
}
```

The list is empty, because the daily invoicing run put each of TransLog's monthly counts on an invoice a few minutes after the count arrived. A submission stays in this list until the first daily invoicing run after the end of the billing period that contains the submission's end. For example, a product that reports usage for each day has its submissions in this list until the billing period ends.

## Invoice line items

Billing also accepts a second kind of submission, the invoice line item. It's a ready-made line with a description, a quantity, and a unit price for a one-off charge such as a service. On an invoice, it's a reported charge line: see [What the lines are](reading-invoices.md#what-the-lines-are). [myInvoiceLineItemSubmissions](../api-reference/submission.md#myinvoicelineitemsubmissions) lists them for your workspace, with the same paging and the same `accounted` and `submittedAt` filters.

Run this query:

```graphql
query LineItems {
  billing {
    myInvoiceLineItemSubmissions(first: 20) {
      nodes {
        description
        quantity
        unitPrice {
          amount
          currency
        }
        periodStart
        periodEnd
        submittedAt
      }
      total {
        count
      }
    }
  }
}
```

The Navixy platform reports no invoice line items, so for a workspace on the standard plans, the response contains an empty list and a count of zero.

## Error handling

The Billing operations return errors in the shared format. See [Error handling](../../error-handling.md) for the response structure and the general codes.

| Code                | When it occurs                                                                     | Resolution                                                                                            |
| ------------------- | ---------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `PERMISSION_DENIED` | The token has no `workspace_id` claim, or the claim isn't a workspace ID.          | Request a token issued for the workspace. The Billing `my*` operations don't use your memberships.    |
| `VALIDATION_ERROR`  | `metricId` or `productDomainId` isn't an ID of that type.                          | Read `field` and `detail`. Take the IDs from the `metric` and `productDomain` of a listed submission. |
| `VALIDATION_ERROR`  | `first` is above 100.                                                              | Send `first` of 100 or less.                                                                          |

A request without a workspace claim returns the same error as on every Billing field. See [How to handle a missing workspace claim](reading-balance-and-statement.md#how-to-handle-a-missing-workspace-claim) in the balance guide.

## See also

- [Submissions](../api-reference/submission.md): Learn how the 2 submission types, their filters, and the queries work.
- [Choosing a plan and subscribing](choosing-a-plan-and-subscribing.md): See which metrics a plan prices and what usage it includes.
- [Reading balance and statement](reading-balance-and-statement.md): Read the workspace's prepaid balance and the entries that changed it.
- [Billing errors](../errors.md): Learn how the Billing operations report a missing entity and which fields their errors have.
- [Authentication](../../authentication.md): Learn how the token names the workspace that every operation acts in.
