---
description: >-
  How the IoT Logic reference is organized: one page for flows, with their
  node types, transitions, inputs, queries, and mutations.
tags:
  - reference
  - beta
---

# IoT Logic API reference

The IoT Logic API reference documents every operation and type of IoT Logic. All of them are on one page, [Flows](flows.md), because every IoT Logic type belongs to a flow. The page covers the queries and mutations, the object and input types, and the enums, with a field table for every type.

The reference states what each operation and field is. For how to use them together, see the [IoT Logic guides](../guides/README.md), and for the GraphQL fundamentals behind the notation, see [GraphQL basics](../../graphql-basics/), including the [type syntax](../../graphql-basics/#type-syntax) that the field tables use.

## Where the operations live

All IoT Logic queries are nested under the `iotLogic` field of the `Query` type, and all mutations under the `iotLogic` field of the `Mutation` type. Every response has the same shape under `data.iotLogic`. Every operation acts in the workspace that your access token names, so no query or mutation takes a workspace ID. For what the product covers and how to start, see the [product overview](../README.md).

A flow is also a `Node`, so the common [`node` and `nodes`](../../core-api-reference/common.md) queries return flows by ID. The nodes in the graph of a flow are a different type, `IotLogicFlowNode`, and you read them only through their flow.

## Pages

<table><thead><tr><th width="186">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="flows.md">Flows</a></td><td><code>IotLogicFlow</code>, the <code>IotLogicFlowNode</code> interface with its node types, <code>IotLogicFlowTransition</code>, the <code>flow</code> and <code>flows</code> queries with their filter and order inputs, the <code>deviceFlowLinks</code> query with <code>IotLogicDeviceFlowLink</code> and its filter and order inputs, the <code>flowDebugValues</code> query with <code>IotLogicFlowDebugValues</code>, and the <code>flowCreate</code>, <code>flowUpdate</code>, and <code>flowDelete</code> mutations with their inputs.</td></tr></tbody></table>

## Shared types

The scalars, interfaces, and pagination types that IoT Logic shares with the other products are on the [Common resources](../../core-api-reference/common.md) page. The devices of a data source are `Device` records from Business Data Repository, documented on the [Devices](../../business-data-repository/api-reference/devices/) page. The [Core API reference](../../core-api-reference/README.md) indexes the reference pages of every product.

## How the reference pages are structured

The page follows the same order as every reference page: queries, then mutations, then objects, inputs, and enums, with pagination types in a section of their own at the end. Field tables use the GraphQL type notation, where `!` marks a value that is never null and `[...]` marks a list: see [Type syntax](../../graphql-basics/#type-syntax).

## Other ways to explore the API

* [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox): Run queries and mutations interactively.
* [Public GraphQL schema](../../developer-resources/public-graphql-schema.md): The full type system split into several files.
* [Introspection](../../graphql-basics/#introspection): Explore the schema from your own GraphQL client.

## See also

* [IoT Logic overview](../README.md): Learn what IoT Logic covers and how its concepts fit together.
* [IoT Logic node types](../node-types.md): Read the fields, the behavior, and the rules of each node type in one place.
* [IoT Logic guides](../guides/README.md): Build a flow from these operations, and handle the errors that a write returns.
* [Optimistic locking](../../optimistic-locking.md): Learn about the version check that every flow update and delete makes.
* [Error handling](../../error-handling.md): See the error structure, the codes, and the common error scenarios.
