---
description: >-
  Read your workspace's prepaid balance, then list the payments, corrections,
  and invoice charges that changed it.
tags:
  - guide
  - beta
---

# Reading balance and statement

This guide shows how to read your workspace's balance and its change history through the Billing API. You will read the balance, then list its statement: every payment, correction, and invoice charge, newest first, each with the balance after it.

Every operation on this page reads the data of the workspace named by your access token. You never pass a workspace ID, so a request can't read the balance of another workspace.

## Prerequisites

You need an access token whose `workspace_id` claim names the workspace. The Billing [my\* operations](../README.md#purpose-and-capabilities), the operations named with the prefix `my` such as [myBalance](../api-reference/balance.md#mybalance) and [myBalanceEntries](../api-reference/balance.md#mybalanceentries), read only that claim. Without it, they refuse the request and don't fall back to the workspace of your single membership. See [Authentication](../../authentication.md#how-the-api-picks-your-workspace) to learn how the API identifies the workspace.

To check that the token names a workspace and that Billing accepts it, read the amount on the balance with this query:

```graphql
query BalanceCheck {
  billing {
    myBalance {
      amount {
        amount
        currency
      }
    }
  }
}
```

Response for a workspace that paid at least once:

```json
{
  "data": {
    "billing": {
      "myBalance": {
        "amount": { "amount": 1667, "currency": "USD" }
      }
    }
  }
}
```

An amount of zero is a valid response: it means that no entry has changed the balance. A `PERMISSION_DENIED` error instead means that the token has no workspace claim. See [Error handling](#error-handling).

## How balance and its statement work

This guide uses 2 concepts: the balance and its entries. [Balances](../api-reference/balance.md) document every field.

A workspace has exactly one balance, in USD. Billing opens it at zero when Console reports the new workspace, so a read never returns null: if no earlier event opened the balance, the first read opens it. The balance is prepaid: its `amount` is the sum of all payments and corrections so far, less the invoices charged to it. The amount can be negative, and a negative amount means that the workspace owes money.

An entry is one line of the statement: one payment, one correction, or one invoice charge, with the balance after it. A `TOP_UP` entry is money that you paid, and a `CORRECTION` entry is an adjustment by Navixy in either direction. An `INVOICE` entry is the total of one invoice, charged when Billing issues the invoice. See [Reading invoices](reading-invoices.md). Billing stores each entry as a posting and adds its text and the balance after it when you read the statement. No query fetches an entry by ID.

`balanceAfter` isn't stored. Billing calculates it when you read the statement. When Navixy enters a payment whose value date is earlier than existing lines, the `balanceAfter` of every later line changes.

`myBalance` and `myBalanceEntries` take no currency. Every amount they return is in USD, and `amount.currency` says so on each value.

## Example scenario: An operator checks fleet balance

TransLog GmbH runs delivery trucks out of Hamburg. Their workspace has been on the Fleet Pro plan since July 1, 2026. On October 9, Navixy entered TransLog's bank transfer of October 8. Their administrator reads the current balance and the statement to see what was paid, what was charged, and what was corrected. To check the balance, follow these steps:

{% stepper %}
{% step %}
### Read the current balance

[myBalance](../api-reference/balance.md#mybalance) returns the workspace's balance: its amount, when it was opened, and when it last changed.

Run this query:

```graphql
query Balance {
  billing {
    myBalance {
      id
      amount {
        amount
        currency
      }
      createdAt
      updatedAt
    }
  }
}
```

Response:

```json
{
  "data": {
    "billing": {
      "myBalance": {
        "id": "019a6b2f-793e-807b-8001-4242414c0001",
        "amount": { "amount": 1667, "currency": "USD" },
        "createdAt": "2026-06-15T09:30:12Z",
        "updatedAt": "2026-10-09T10:14:31Z"
      }
    }
  }
}
```

The workspace has $16.67 on its balance. Render `amount` in the currency's units. The value is in minor units, so $16.67 is returned as `1667`. A negative value means that the workspace owes that amount, so view it as a debt, not as a payment.

`createdAt` is when Billing opened the balance: when Console reported the workspace, or at the first read or the first payment if that came earlier. `updatedAt` is when the balance last changed: here, when Navixy entered the October 8 payment.
{% endstep %}

{% step %}
### List the statement

[myBalanceEntries](../api-reference/balance.md#mybalanceentries) returns the balance entries, newest first by default. Each entry contains its type, its amount, the balance right after it, and the text of the payment or correction behind it.

Run this query:

```graphql
query Statement {
  billing {
    myBalanceEntries(first: 20) {
      nodes {
        id
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
        authoredBy
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
      "myBalanceEntries": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-4254524e0006",
            "createdAt": "2026-10-08T00:00:00Z",
            "type": "TOP_UP",
            "amount": { "amount": 5000, "currency": "USD" },
            "balanceAfter": { "amount": 1667, "currency": "USD" },
            "description": "Wire transfer PO-5208",
            "authoredBy": "019a6b2f-793e-807b-8001-555345520001"
          },
          {
            "id": "019a6b2f-793e-807b-8001-4254524e0005",
            "createdAt": "2026-10-01T00:05:02Z",
            "type": "INVOICE",
            "amount": { "amount": -5800, "currency": "USD" },
            "balanceAfter": { "amount": -3333, "currency": "USD" },
            "description": "INV-2026-000132",
            "authoredBy": "00000000-0000-8000-8001-535953540000"
          },
          {
            "id": "019a6b2f-793e-807b-8001-4254524e0004",
            "createdAt": "2026-09-10T14:22:45Z",
            "type": "CORRECTION",
            "amount": { "amount": 900, "currency": "USD" },
            "balanceAfter": { "amount": 2467, "currency": "USD" },
            "description": "Goodwill credit for the September 8 outage",
            "authoredBy": "019a6b2f-793e-807b-8001-555345520002"
          },
          {
            "id": "019a6b2f-793e-807b-8001-4254524e0003",
            "createdAt": "2026-09-01T00:05:03Z",
            "type": "INVOICE",
            "amount": { "amount": -5500, "currency": "USD" },
            "balanceAfter": { "amount": 1567, "currency": "USD" },
            "description": "INV-2026-000087",
            "authoredBy": "00000000-0000-8000-8001-535953540000"
          },
          {
            "id": "019a6b2f-793e-807b-8001-4254524e0002",
            "createdAt": "2026-08-01T00:05:02Z",
            "type": "INVOICE",
            "amount": { "amount": -2933, "currency": "USD" },
            "balanceAfter": { "amount": 7067, "currency": "USD" },
            "description": "INV-2026-000041",
            "authoredBy": "00000000-0000-8000-8001-535953540000"
          },
          {
            "id": "019a6b2f-793e-807b-8001-4254524e0001",
            "createdAt": "2026-07-03T00:00:00Z",
            "type": "TOP_UP",
            "amount": { "amount": 10000, "currency": "USD" },
            "balanceAfter": { "amount": 10000, "currency": "USD" },
            "description": "Wire transfer PO-4471",
            "authoredBy": "019a6b2f-793e-807b-8001-555345520001"
          }
        ],
        "pageInfo": {
          "hasNextPage": false,
          "endCursor": "YmFsYW5jZS1lbnRyeTowMDAx"
        },
        "total": { "count": 6 }
      }
    }
  }
}
```

The statement starts with the newest line. Read it from the bottom to follow the balance over time:

* TransLog paid $100.00 with value date July 3.
* Billing charged the invoices for July, August, and September on the first day of the next month.
* Navixy filed a $9.00 credit on September 10.
* The October 1 invoice left the balance at -$33.33.
* The $50.00 payment with value date October 8 brought the balance to $16.67, the amount that [myBalance](../api-reference/balance.md#mybalance) returned.

The October 8 payment covered `INV-2026-000132`, the October 1 invoice. Billing marked that invoice `PAID` when Navixy entered the payment, on October 9 at 10:14:31 UTC. See [Reading invoices](reading-invoices.md) to read the invoice.

Request the next page with `after: endCursor` while `hasNextPage` is true. Pass the cursor back exactly as received: it encodes the sort field and direction as well as the position. See [Pagination](../../pagination.md) for the cursor rules.
{% endstep %}

{% step %}
### Render each line

Use these fields to show a line:

* The label of a line comes from `type`. Read `TOP_UP` as a payment, `CORRECTION` as an adjustment, `INVOICE` as a charge. `description` isn't a label. It's the text of the payment, correction, or invoice behind the entry: the note on a payment, for example "Wire transfer PO-4471", the reason entered by a staff member for a correction, or an invoice number, for example `INV-2026-000132`. It's null when a payment has no note. A correction always has its reason.
* The direction of a line comes from the sign of `amount`. A positive amount was credited into the balance, and a negative amount was debited. Don't infer the direction from `type`: a correction goes either way.
* `balanceAfter` is the balance right after the entry. It's the same value no matter in which direction you read the statement, so a page ordered oldest first and a page ordered newest first show the same totals.
* `createdAt` of a payment line is 00:00 UTC on the payment's value date: the day the bank says the money arrived, not the moment the payment was entered. The October 8 payment was entered on October 9, and it appears on the statement on October 8. A correction is dated when it was filed, and an invoice charge when the invoice was issued.

`authoredBy` is the ID of the person who entered the payment or the correction: an accountant for a payment, a staff member for a correction. On an invoice charge, it's the fixed ID that the Navixy platform uses for actions that no person took, because Billing issues invoices itself. In the example, that ID is `00000000-0000-8000-8001-535953540000`. It's an ID, not a name. Billing doesn't store actors, so a page that shows a name looks it up in [Users](../../business-data-repository/api-reference/actors/users.md) or shows the ID as it is.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You read your workspace's balance and the statement that explains it, line by line, with the balance after each line. Everything acted in the workspace named by your token.
{% endhint %}

## Showing one statement period

To show one month, filter the entries by time. `createdAtFrom` and `createdAtTo` are inclusive, and time is the only filter. Run this query:

```graphql
query SeptemberStatement {
  billing {
    myBalanceEntries(
      filter: {
        createdAtFrom: "2026-09-01T00:00:00Z"
        createdAtTo: "2026-09-30T23:59:59Z"
      }
      orderBy: { field: CREATED_AT, direction: ASC }
      first: 50
    ) {
      nodes {
        createdAt
        type
        amount {
          amount
        }
        balanceAfter {
          amount
        }
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
            "createdAt": "2026-09-01T00:05:03Z",
            "type": "INVOICE",
            "amount": { "amount": -5500 },
            "balanceAfter": { "amount": 1567 }
          },
          {
            "createdAt": "2026-09-10T14:22:45Z",
            "type": "CORRECTION",
            "amount": { "amount": 900 },
            "balanceAfter": { "amount": 2467 }
          }
        ]
      }
    }
  }
}
```

The response has the 2 September entries, oldest first, with the same `balanceAfter` as in the full statement. A narrower period doesn't restart the totals: `balanceAfter` still counts every entry since the balance was opened. To get the balance before a period, subtract the `amount` of the period's first line from its `balanceAfter`. In September, the first line has a `balanceAfter` of $15.67 and an `amount` of -$55.00, so the balance before September was $70.67. To get the change over the period, subtract the balance before the period from the `balanceAfter` of the period's last line. In September, the change is $24.67 minus $70.67, which is -$46.00.

You can filter the statement only by time, so that every line of a period is present. `balanceAfter` counts every entry, including the entries that a filter by type would hide. A statement that showed only corrections would show totals that include the hidden payments, and the lines on the page wouldn't add up to them. To show one type only, read the whole period and filter the lines in your own code.

## What happens outside the API

The Billing API reads the balance and its statement. It doesn't take payments: a top-up is entered by Navixy from the bank transfer, and you see it as a `TOP_UP` entry once Navixy enters it. Billing issues the charges itself: each invoice appears as an `INVOICE` entry when Billing issues it. To see what an invoice charged for, find it by the number in `description`, as [Reading invoices](reading-invoices.md) shows.

## Error handling

The Billing operations return errors in the shared format. See [Error handling](../../error-handling.md) for the response structure and the general codes.

| Code                | When it occurs                                                            | Resolution                                                                                         |
| ------------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `PERMISSION_DENIED` | The token has no `workspace_id` claim, or the claim isn't a workspace ID. | Request a token issued for the workspace. The Billing `my*` operations don't use your memberships. |
| `VALIDATION_ERROR`  | `first` is above 100.                                                     | Send `first` of 100 or less.                                                                       |

A request without a workspace claim returns this error on every Billing field it selects. `myBalance` can't be null, so the error empties the whole `data` object:

```json
{
  "errors": [{
    "message": "the token carries no workspace, so there is nothing for this operation to act on",
    "path": ["billing", "myBalance"],
    "extensions": {
      "type": "https://api.navixy.com/errors/forbidden",
      "title": "Permission Denied",
      "status": 403,
      "detail": "the token carries no workspace, so there is nothing for this operation to act on",
      "instance": "/graphql",
      "code": "PERMISSION_DENIED",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-28T10:45:00Z"
    }
  }],
  "data": null
}
```

### How to handle a missing workspace claim

Request a token issued for the workspace whose billing you read. The Billing `my*` operations don't fall back to the workspace of your single membership, so a token that works for Business Data Repository can still be refused here. See [How the API picks your workspace](../../authentication.md#how-the-api-picks-your-workspace).

## See also

* [Balances](../api-reference/balance.md): Learn how the balance, the entry, the filter, and the queries work.
* [Reading invoices](reading-invoices.md): Read the invoices behind the `INVOICE` entries, line by line.
* [Choosing a plan and subscribing](choosing-a-plan-and-subscribing.md): List the plans a workspace may take and subscribe to one.
* [Billing errors](../errors.md): Learn how the Billing operations report a missing entity and which fields their errors have.
* [Authentication](../../authentication.md): Learn how the token names the workspace that every operation acts in.
* [Pagination](../../pagination.md): Page through a long statement with cursors.
