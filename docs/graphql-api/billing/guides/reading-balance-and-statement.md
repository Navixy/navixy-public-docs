---
description: >-
  Read the prepaid balance of your workspace, then list the payments and
  corrections that moved it.
tags:
  - guide
  - beta
---

# Reading balance and statement

This guide shows how to read the balance of your workspace and its change history through the Billing API. You will read the balance, then list its statement: every payment and correction, newest first, each with the balance after it.

Every operation on this page acts in the workspace named by your access token. You never pass a workspace ID, so a request can't read the balance of another workspace.

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
        "amount": { "amount": 20900, "currency": "USD" }
      }
    }
  }
}
```

An amount of zero is a valid answer: nothing has moved the balance yet. A `PERMISSION_DENIED` error instead means that the token has no workspace claim. See [Error handling](reading-balance-and-statement.md#error-handling) below.

## How balance and its statement work

Balance and its entries are the two concepts that the steps below use. [Balances](../api-reference/balance.md) document every field.

A workspace holds exactly one balance, in USD. Billing opens it at zero when Console reports the new workspace, so a read never returns null: if nothing has opened the balance yet, the first read opens it. The balance is prepaid: its `amount` is the sum of all payments and corrections so far. The amount can be negative, and a negative amount means that the workspace owes money.

An entry is one line of the statement: one payment or one correction, with the balance after it. A `TOP_UP` entry is money that you paid, and a `CORRECTION` entry is an adjustment by Navixy in either direction. Billing stores each entry as a posting and adds its text and the balance after it when you read the statement. No query fetches an entry by ID.

`balanceAfter` isn't stored. Billing calculates it when you read the statement. When Navixy enters a payment whose value date is earlier than existing lines, the `balanceAfter` of every later line changes.

`myBalance` and `myBalanceEntries` take no currency. Every amount they return is in USD, and `amount.currency` says so on each value.

## Example scenario: An operator checks fleet balance

TransLog GmbH runs delivery trucks out of Hamburg. Their workspace is on a paid plan. Their administrator reads the current balance and the statement of the last month to see what was paid and what was corrected.

{% stepper %}
{% step %}
### Read the current balance

[myBalance](../api-reference/balance.md#mybalance) returns the balance of the workspace: its amount, when it was opened, and when it last moved.

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
        "amount": { "amount": 20900, "currency": "USD" },
        "createdAt": "2026-09-01T09:30:12Z",
        "updatedAt": "2026-09-10T14:22:45Z"
      }
    }
  }
}
```

The workspace has $209.00 on its balance. Render `amount` in the units of the currency. The value is in minor units, so $209.00 arrives as `20900`. A negative value means that the workspace owes that amount, so view it as a debt, not as a payment.

`createdAt` is when Billing opened the balance: when Console reported the workspace, or at the first read or the first payment if that came earlier. `updatedAt` is when the balance last changed.
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
            "id": "019a6b2f-793e-807b-8001-4254524e0002",
            "createdAt": "2026-09-10T14:22:45Z",
            "type": "CORRECTION",
            "amount": { "amount": 900, "currency": "USD" },
            "balanceAfter": { "amount": 20900, "currency": "USD" },
            "description": "Goodwill credit for the September 8 outage",
            "authoredBy": "019a6b2f-793e-807b-8001-555345520001"
          },
          {
            "id": "019a6b2f-793e-807b-8001-4254524e0001",
            "createdAt": "2026-09-01T00:00:00Z",
            "type": "TOP_UP",
            "amount": { "amount": 20000, "currency": "USD" },
            "balanceAfter": { "amount": 20000, "currency": "USD" },
            "description": "Wire transfer PO-4471",
            "authoredBy": "019a6b2f-793e-807b-8001-555345520001"
          }
        ],
        "pageInfo": {
          "hasNextPage": false,
          "endCursor": "YmFsYW5jZS1lbnRyeTowMDAx"
        },
        "total": { "count": 2 }
      }
    }
  }
}
```

The statement reads from the newest line down. TransLog paid $200.00 with value date September 1, and got a $9.00 credit on September 10. The balance after the newest line is the $209.00 that [myBalance](../api-reference/balance.md#mybalance) returned.

Request the next page with `after: endCursor` while `hasNextPage` is true. Pass the cursor back exactly as received: it encodes the sort field and direction as well as the position. See [Pagination](../../pagination.md) for the cursor rules.
{% endstep %}

{% step %}
### Render each line

Four fields decide how a line looks:

* The label of a line comes from `type`. Read `TOP_UP` as a payment, `CORRECTION` as an adjustment, `INVOICE` as a charge. `description` isn't a label. It's the text of the payment or correction behind the entry: the note on a payment, for example "Wire transfer PO-4471", or the reason entered by a staff member for a correction. It's null when a payment has no note. A correction always carries its reason.
* The direction of a line comes from the sign of `amount`. A positive amount was credited into the balance, and a negative amount was debited. Don't infer the direction from `type`: a correction goes either way.
* `balanceAfter` is the balance right after the entry. It's the same value no matter in which direction you read the statement, so a page ordered oldest first and a page ordered newest first show the same totals.
* `createdAt` of a payment line is midnight UTC of the payment's value date: the day the bank says the money arrived, not the moment the payment was entered. A transfer entered three days late appears on the statement where it belongs. A correction is dated when it was filed.

`authoredBy` is the ID of the person who entered the payment or the correction: an accountant for a payment, a staff member for a correction. It's an ID, not a name. Billing doesn't store actors, so a page that shows a name looks it up in [Users](../../business-data-repository/api-reference/actors/users.md) or shows the ID as it is.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You read the balance of your workspace and the statement that explains it, line by line, with the balance after each line. Everything acted in the workspace named by your token.
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

The response holds the same two entries, oldest first, with the same `balanceAfter` on each. A narrower period doesn't restart the totals. `balanceAfter` still counts every entry since the balance was opened, so the first line of a period shows the balance brought into the period, not the period subtotal. To get the movement across a period, subtract the `balanceAfter` of its first line from that of its last.

You can filter the statement only by time, so that every line of a period is present. `balanceAfter` counts every entry, including the entries that a filter by type would hide. A statement that showed only corrections would show totals that include the hidden payments, and the lines on the page wouldn't add up to them. To show one type only, read the whole period and filter the lines in your own code.

## What happens outside the API

The Billing API reads the balance and its statement. It doesn't take payments: a top-up is entered by Navixy from the bank transfer, and you see it as a `TOP_UP` entry once it lands. At the moment, no charge appears on the statement either: no `INVOICE` entry is issued, and the balance moves only through payments and corrections.

## Error handling

The Billing operations return errors in the shared format. See [Error handling](../../error-handling.md) for the response structure and the general codes.

| Code                | When it occurs                                                            | Resolution                                                                                         |
| ------------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `PERMISSION_DENIED` | The token has no `workspace_id` claim, or the claim isn't a workspace ID. | Request a token issued for the workspace. The Billing `my*` operations don't use your memberships. |
| `VALIDATION_ERROR`  | `first` is above 100.                                                     | Page in requests of up to 100 entries.                                                             |

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
* [Choosing a plan and subscribing](choosing-a-plan-and-subscribing.md): List the plans a workspace may take and subscribe to one.
* [Billing errors](../errors.md): Learn how the Billing operations report a missing entity and which fields their errors have.
* [Authentication](../../authentication.md): Learn how the token names the workspace that every operation acts in.
* [Pagination](../../pagination.md): Page through a long statement with cursors.
