---
description: >-
  How the Billing reference is organized: one page per topic for plans,
  subscriptions, balances, and submissions.
tags:
  - reference
  - beta
---

# Billing API reference

The Billing API reference documents every public operation and type of Billing, grouped into one page per topic. Each page covers its queries and mutations, its object and input types, and its enums, with a field table for every type.

The reference states what each operation and field is. For the GraphQL fundamentals behind the notation, see [GraphQL basics](../../graphql-basics/), including the [type syntax](../../graphql-basics/#type-syntax) that the field tables use.

## Where the operations live

All Billing queries and mutations are nested under the `billing` field of the `Query` and `Mutation` types, and every response has the same shape under `data.billing`.

Every operation is one of the [my\* operations](../README.md#purpose-and-capabilities), named with the prefix `my`: it acts in the workspace named by your access token and takes no workspace argument. Navixy products report usage to Billing outside this API, and the submission queries list what they reported for your workspace.

For a workspace token, `node(id:)` returns `null` for billing types other than the two submission types, and for a submission of another workspace. Read them through the queries on these pages.

## Pages

<table><thead><tr><th width="186">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="plan.md">Plans</a></td><td>The plans and branches your workspace may subscribe to (<code>BillingAvailablePlan</code>, <code>BillingAvailablePlanItem</code>), the <code>myAvailablePlans</code> query that lists them with the subscription you already hold on each, and the catalog and pricing vocabulary a plan is built from: <code>BillingProductDomain</code>, <code>BillingMetric</code>, <code>BillingPricingModel</code>, <code>BillingAggregationFunction</code>, <code>BillingUsagePricing</code>, and <code>BillingUsagePricingTier</code>.</td></tr><tr><td><a href="subscription.md">Subscriptions</a></td><td>The <code>mySubscribe</code> mutation and the <code>mySubscriptions</code> query, with <code>BillingWorkspaceSubscription</code>, its filter and order inputs, and its pagination types.</td></tr><tr><td><a href="balance.md">Balances</a></td><td>The prepaid balance of your workspace (<code>BillingBalance</code>), the statement entries that moved it (<code>BillingBalanceEntry</code>), and the <code>myBalance</code> and <code>myBalanceEntries</code> queries.</td></tr><tr><td><a href="submission.md">Submissions</a></td><td>The usage-metric and invoice-line-item submissions reported for your workspace (<code>BillingUsageMetricSubmission</code>, <code>BillingInvoiceLineItemSubmission</code>), and the <code>myUsageMetricSubmissions</code> and <code>myInvoiceLineItemSubmissions</code> queries that list them, with their filter and order inputs.</td></tr><tr><td><a href="invoice.md">Invoices</a></td><td>The invoices of your workspace (<code>BillingWorkspaceInvoice</code>) with their lines (<code>BillingWorkspaceInvoiceLineItem</code>), and the <code>myInvoice</code> and <code>myInvoices</code> queries that read them, with their filter and order inputs.</td></tr></tbody></table>

## Shared types

The scalars, interfaces, and pagination types that Billing shares with the other products are on the [Common resources](../../core-api-reference/common.md) page. Billing also uses the `Money` value type and the `Currency` scalar from that page. The usage prices of a plan (`BillingUsagePricing.price` and the `price` of a tier) are `Long` values in minor currency units; the period fee, balances, and invoice line items use `Money`. The [Core API reference](../../core-api-reference/README.md) indexes the reference pages of every product.

## How the reference pages are structured

Every page follows the same order: queries, then mutations, then the page's objects, inputs, and enums, with pagination types in a section of their own at the end. Field tables use the GraphQL type notation, where `!` marks a value that is never null and `[...]` marks a list: see [Type syntax](../../graphql-basics/#type-syntax).

## Other ways to explore the API

* [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox): Run queries and mutations interactively.
* [Public GraphQL schema](../../developer-resources/public-graphql-schema.md): The full type system split into several files.
* [Introspection](../../graphql-basics/#introspection): Explore the schema from your own GraphQL client.

## See also

* [Pagination](../../pagination.md): Learn how the connection types on these pages page through results.
* [Error handling](../../error-handling.md): Understand error structure, codes, and common error scenarios.
