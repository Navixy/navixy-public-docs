---
description: >-
  Billing stores what a workspace pays for: its plan, the usage reported for
  it, the invoices that charge for both, and the prepaid balance.
tags:
  - overview
  - beta
---

# Billing overview

**Billing API** is the product that stores what a workspace pays for. It's one of the products served by [Navixy GraphQL API](../). This documentation is for developers who call the API for their own workspace to show its plans, subscriptions, invoices, balance, and usage in an app or to subscribe it to a plan. For a workspace, Billing shows which plans it may take, which plan it's on, what usage was reported for it, what each billing period cost, and how much it has on balance and why.

All Billing queries are nested under the `billing` field of the `Query` type and Billing mutations under the `billing` field of the `Mutation` type. Authentication, pagination, and error handling work the same way for every product of Navixy GraphQL API, so see [Authentication](../authentication.md), [Pagination](../pagination.md), and [Error handling](../error-handling.md) to learn that. For the endpoint and request headers, see [Getting started](../getting-started.md).

## Purpose and capabilities

Billing API enables a workspace to:

- List the plans it may subscribe to, with the price and the usage limits of each one
- Subscribe to a plan and list its current and past subscriptions
- Read its prepaid balance
- Read the statement of its balance: every payment, correction, and invoice charge, with the balance after each one
- List the usage that Navixy products reported for it
- List its invoices and read the lines of each one: the fee and the priced usage of every billing period, and whether the invoice is paid

The operations behind these tasks are the `my*` operations, named for the prefix `my`, such as `myAvailablePlans` and `mySubscribe`. The [Billing API reference](api-reference/) documents each one. Every `my*` operation acts in the workspace named by your access token. None of them takes a workspace ID, so a request can't read or change the billing of another workspace. Navixy products report usage to Billing outside this API, and [myUsageMetricSubmissions](api-reference/submission.md#myusagemetricsubmissions) lists what they reported for your workspace.

## Key concepts

<table><thead><tr><th width="147">Term</th><th>Definition</th></tr></thead><tbody><tr><td><strong>Plan</strong></td><td>What a workspace pays for: a fee per billing period, plus a price per unit of usage for each metered product. A plan covers one or more products.</td></tr><tr><td><strong>Plan branch</strong></td><td>A variant of a plan with its own prices, offered to some workspaces. Billing lists a branch as its own row, next to the base plan or in its place, so a workspace chooses a row.</td></tr><tr><td><strong>Subscription</strong></td><td>The link between a workspace and one plan or branch. A subscription is active until cancelled. A workspace has one active subscription per product.</td></tr><tr><td><strong>Trial</strong></td><td>A number of days at the start of a subscription during which the plan is free. A workspace gets at most one trial per plan, counting its branches.</td></tr><tr><td><strong>Product</strong></td><td>A billable product of the Navixy platform, such as <a href="../business-data-repository/README.md">Business Data Repository</a> or <a href="../tracking/README.md">Tracking</a>. A plan prices the usage of a product based on its metrics. The schema calls it a <strong>product domain</strong>.</td></tr><tr><td><strong>Metric</strong></td><td>One measured quantity of a product, such as the number of registered devices. A plan sets, per metric, the usage included in the fee and the price on top of it.</td></tr><tr><td><strong>Usage submission</strong></td><td>A value of a metric for a period, reported by a Navixy product. Before it stores the value, Billing checks that the workspace subscription covers the period and the metric.</td></tr><tr><td><strong>Invoice</strong></td><td>A charge for a workspace's billing periods that ended and that no earlier invoice covered. It charges the period fee of each product and the priced usage of each metric. Billing issues invoices itself and charges the total of each one to the balance.</td></tr><tr><td><strong>Balance</strong></td><td>The money a workspace paid to Navixy, minus the charges against it, in USD. A negative balance means the workspace owes money. A balance belongs to one workspace, and whether a negative balance stops the workspace is decided outside Billing.</td></tr><tr><td><strong>Statement entry</strong></td><td>One line in the balance history: a payment, a correction, or a charge. The balance changes only through entries, never by being set.</td></tr></tbody></table>

## How billing works for a workspace

1. Navixy Console creates the workspace, and Billing subscribes it to the default plan. From then on, the workspace has a plan, and Console enables the products that the plan turns on by default.
2. A workspace may subscribe to another plan or branch offered by Billing as long as no active subscription of the workspace already covers a product belonging to that plan. To move a workspace from one plan to another, contact Navixy at [sales@navixy.com](mailto:sales@navixy.com).
3. Navixy products report workspace usage to Billing as submissions against the plan's metrics, outside this API. A submission is accepted only for the period covered by the workspace's subscription. You can list the submissions of your workspace.
4. Once a day, shortly after midnight UTC, Billing invoices the billing periods that ended: the period fee and the usage reported for each period, priced by the plan. The invoice total is charged to the balance and appears on the statement. Usage reported after its period was invoiced is charged in the next daily invoicing run.
5. The balance is prepaid. Navixy enters each payment from the bank transfer, and you see it as an entry on the statement dated the day the money arrived. A correction by Navixy appears the same way, in either direction. Payments cover the oldest unpaid invoices first. An invoice that stays unpaid after its grace period becomes overdue.

## Who does what

Billing splits the work between you, calling the API for your own workspace, and Navixy, running the catalog and the money:

<table><thead><tr><th width="147">Who</th><th>Does</th></tr></thead><tbody><tr><td><strong>You</strong></td><td>Lists the plans open to the workspace, subscribes the workspace to one, reads the invoices, the balance, and its statement, and lists the usage reported for the workspace. Every call acts in the workspace named by the token.</td></tr><tr><td><strong>Navixy</strong></td><td>Sets up the products, metrics, and plans offered by Billing. Moves a workspace to another plan or cancels a subscription. Enters each payment from the bank transfer and files corrections. Billing issues the invoices. You can see the results of each of these on the statement, in the invoices, or in the catalog.</td></tr></tbody></table>

Two limits apply to every plan: prices are in US dollars, and the billing period is a month.

## Coming soon

**Quota warnings**: A plan sets a quota and warning thresholds per metric. Billing notifies the product when usage crosses a threshold, so you learn about it before the quota is reached.

## Navigation

The Billing documentation has 4 guides, a page on errors, and an API reference with one page per topic.

The guides cover the tasks that you can do with the Billing API for your workspace:

- [Choosing a plan and subscribing](guides/choosing-a-plan-and-subscribing.md) lists the plans available to your workspace with the price and the limits of each one, subscribes your workspace, and confirms the subscription.
- [Reading balance and statement](guides/reading-balance-and-statement.md) reads the balance and lists the entries that moved it, with the balance after each one.
- [Reading invoices](guides/reading-invoices.md) lists your workspace's invoices, reads the lines of one invoice, matches it to the statement, and finds the invoices that are still unpaid.
- [Reading usage submissions](guides/reading-usage-submissions.md) lists the usage that Navixy products reported for your workspace and follows one metric month by month.

[Billing errors](errors.md) documents how the Billing operations use the shared error codes: what a missing entity returns, and which fields a Billing error has.

The [Billing API reference](api-reference/) documents the operations and types, one page per topic:

- [Plans](api-reference/plan.md) documents the rows a workspace may take, their items, and the pricing vocabulary.
- [Subscriptions](api-reference/subscription.md) documents subscribing and listing subscriptions.
- [Balances](api-reference/balance.md) documents the balance, the statement entry, and the queries that read them.
- [Submissions](api-reference/submission.md) documents the usage reported for your workspace and the queries that list it.
- [Invoices](api-reference/invoice.md) documents your workspace's invoices, their lines, and the queries that read them.

For the types that Billing shares with the other products and for an index of the whole API, see [Core API reference](../core-api-reference/).

{% hint style="warning" %}
The API supports [GraphQL introspection](../graphql-basics/#introspection) for authenticated users. You can also check the public [GraphQL schema](../developer-resources/public-graphql-schema.md).
{% endhint %}
