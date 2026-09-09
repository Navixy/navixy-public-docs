---
description: >-
  The public GraphQL schema of Navixy GraphQL API, split into one file for the
  shared types and one per product.
---

# Public GraphQL schema

{% include "../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

The GraphQL schema for Navixy GraphQL API is public and split into several parts:

* **Common resources** ([`nvx4-common.graphql`](https://raw.githubusercontent.com/Navixy/navixy-api/refs/heads/master/docs/navixy-graphql-api/developer-resources/graphql-schema/nvx4-common.graphql)): Types and operations [shared by the entire API](../core-api-reference/).
* **Business Data Repository** ([`nvx4-api.graphql`](https://raw.githubusercontent.com/Navixy/navixy-api/refs/heads/master/docs/navixy-graphql-api/developer-resources/graphql-schema/nvx4-api.graphql)): Types and operations specific to [Business Data Repository](../business-data-repository/overview.md). This file also declares the `Query`, `Mutation`, and `Subscription` roots for the whole API.
* **Tracking** ([`nvx4-tracking.graphql`](https://raw.githubusercontent.com/Navixy/navixy-api/refs/heads/master/docs/navixy-graphql-api/developer-resources/graphql-schema/nvx4-tracking.graphql)): Types and operations specific to [Tracking](../tracking/overview.md).

## What the files are for

The files are GraphQL schema definition language, the same text that introspection returns. Download them to generate typed client code, to feed a GraphQL plugin in your editor, or to compare two versions of the API and see what changed. Read them together rather than one at a time: each product file uses types that the common file declares.

To explore the API without downloading anything, use [introspection](../graphql-basics/#introspection) from your own GraphQL client, or run queries in the [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox).

## Versions

Each product file states its schema version in a comment on the third line, for example `# Version: 0.9.1`. The common file carries no version of its own, because it belongs to no product: it follows the version in `nvx4-api.graphql`. Check the version before you report a difference between the schema and this documentation.
