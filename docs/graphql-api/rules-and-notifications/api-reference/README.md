---
description: >-
  How the Rules and notifications reference is organized: rules and their
  history, the notification stream, and the stored notifications.
tags:
  - reference
  - beta
---

# Rules and notifications API reference

The Rules and notifications API reference documents every operation and type of the `rules` and `notifications` namespaces, on four pages. Each page covers the queries, mutations, or subscription of one topic, then the object and input types and the enums, with a field table for every type.

The reference states what each operation and field is. See the [Rules and notifications guides](../guides/README.md) to learn how to use them together. See [GraphQL basics](../../graphql-basics/) to learn the notation, including the [type syntax](../../graphql-basics/#type-syntax) that the field tables use.

## Where the operations are in the schema

All rules queries are nested under the `rules` field of the `Query` type, and all rules mutations under the `rules` field of the `Mutation` type. The notifications query and mutation are under the `notifications` field of the same types. Every response returns its result under `data.rules` or `data.notifications`.

The notification stream is the `notificationsEvents` field of the `Subscription` type. A subscription can't be nested under a namespace, so the stream has its own top-level field, like `trackingAssetStates` of Tracking.

Every operation reads or changes the data of the workspace named by your access token, so no operation has a workspace ID argument. For what both products cover and how to start, see the [overview](../README.md).

A rule, a rule event, and a notification are also a `Node`, so the common [`node` and `nodes`](../../core-api-reference/common.md) queries return them by ID.

## Pages

<table><thead><tr><th width="186">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="rules/rules.md">Rules</a></td><td><code>RulesRule</code> with <code>RulesEquation</code>, the <code>RulesCondition</code> union of the three condition types, <code>RulesThreshold</code> and the <code>RulesValue</code> union, the <code>rule</code>, <code>rules</code>, and <code>parameterFields</code> queries with their filter and order inputs and <code>RulesParameterField</code>, and the <code>ruleCreate</code>, <code>ruleUpdate</code>, and <code>ruleDelete</code> mutations with their inputs.</td></tr><tr><td><a href="rules/history.md">Rule history</a></td><td><code>RulesRuleEvent</code> with <code>RulesEvaluatedValue</code>, and the <code>events</code> query with its filter and order inputs.</td></tr><tr><td><a href="notifications/stream.md">Notification stream</a></td><td>The <code>notificationsEvents</code> subscription, <code>NotificationsEvent</code>, and <code>NotificationsSeverity</code>.</td></tr><tr><td><a href="notifications/history.md">Stored notifications</a></td><td>The <code>events</code>, <code>readUpTo</code>, and <code>unreadCount</code> fields of the <code>notifications</code> query with the filter and order inputs, and the <code>eventsMarkRead</code> mutation with its input and payload.</td></tr></tbody></table>

## Shared types

The scalars, interfaces, and pagination types that Rules and Notifications share with the other products are on the [Common resources](../../core-api-reference/common.md) page. A rule refers to assets, geo objects, and schedules of Business Data Repository by ID. They're documented on the [Assets](../../business-data-repository/api-reference/assets/), [Geo objects](../../business-data-repository/api-reference/geo-objects.md), and [Schedules](../../business-data-repository/api-reference/schedules.md) pages. The [Core API reference](../../core-api-reference/README.md) indexes the reference pages of every product.

## How the reference pages are structured

Every page lists its content in the same order as the other reference pages. Queries come first, then mutations or subscriptions, then objects, inputs, enums, interfaces, and unions. Pagination types have their own section at the end. Field tables use the GraphQL type notation, where `!` marks a value that is never null and `[...]` marks a list: see [Type syntax](../../graphql-basics/#type-syntax).

## Other ways to explore the API

* [Public GraphQL schema](../../developer-resources/public-graphql-schema.md): The full type system split into several files.
* [Introspection](../../graphql-basics/#introspection): Explore the schema from your own GraphQL client.

## See also

* [Rules and notifications overview](../README.md): Learn what both products cover and how their concepts fit together.
* [How rules work](../how-rules-work.md): Learn which messages a rule checks and when it sends a notification.
* [Rule errors](../rule-errors.md): Read every violation code that a rule save can return.
* [Optimistic locking](../../optimistic-locking.md): Learn about the version check that every rule update and delete makes.
* [Error handling](../../error-handling.md): See the error structure, the codes, and the common error scenarios.
