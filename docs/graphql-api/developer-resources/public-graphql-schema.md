---
description: >-
  The public GraphQL schema of Navixy GraphQL API, split into one file for the
  shared types and one per product or platform service.
tags:
  - reference
  - beta
---

# Public GraphQL schema

The GraphQL schema for Navixy GraphQL API is public and split into several parts:

* **Common resources** ([`nvx4-common.graphql`](https://raw.githubusercontent.com/Navixy/navixy-public-docs/refs/heads/main/docs/graphql-api/developer-resources/graphql-schema/nvx4-common.graphql)): Types and operations [shared by the entire API](../core-api-reference/).
* **Business Data Repository** ([`nvx4-bdr.graphql`](https://raw.githubusercontent.com/Navixy/navixy-public-docs/refs/heads/main/docs/graphql-api/developer-resources/graphql-schema/nvx4-bdr.graphql)): Types and operations specific to [Business Data Repository](../business-data-repository/). This file also declares the `Query` and `Mutation` roots and the `schema` block for the whole API. The `Subscription` root is declared in `nvx4-tracking.graphql`, because its only field streams tracking state.
* **Tracking** ([`nvx4-tracking.graphql`](https://raw.githubusercontent.com/Navixy/navixy-public-docs/refs/heads/main/docs/graphql-api/developer-resources/graphql-schema/nvx4-tracking.graphql)): Types and operations specific to [Tracking](../tracking/).
* **IoT Logic** ([`nvx4-iot-logic.graphql`](https://raw.githubusercontent.com/Navixy/navixy-public-docs/refs/heads/main/docs/graphql-api/developer-resources/graphql-schema/nvx4-iot-logic.graphql)): Types and operations specific to [IoT Logic](../iot-logic/). Its entry points, `Query.iotLogic` and `Mutation.iotLogic`, are declared in `nvx4-bdr.graphql` with the other roots.
* **Billing** ([`nvx4-billing-public.graphql`](https://raw.githubusercontent.com/Navixy/navixy-public-docs/refs/heads/main/docs/graphql-api/developer-resources/graphql-schema/nvx4-billing-public.graphql)): The public types and operations of [Billing](../billing/): the ones a workspace calls for itself. Navixy products report usage outside this API, and the staff-facing billing operations are not part of the public schema.
* **Console** ([`nvx4-console.graphql`](https://raw.githubusercontent.com/Navixy/navixy-public-docs/refs/heads/main/docs/graphql-api/developer-resources/graphql-schema/nvx4-console.graphql)): The types and operations of [Console](../console/): the organizations and workspaces you can reach, and the products of a workspace, which you can enable or disable.
* **IAM** ([`nvx4-iam.graphql`](https://raw.githubusercontent.com/Navixy/navixy-public-docs/refs/heads/main/docs/graphql-api/developer-resources/graphql-schema/nvx4-iam.graphql)): The types and operations of [IAM](../iam/): the roles of your organization, what each one grants, and who holds it.

## What the files are for

The files are GraphQL schema definition language. They describe the same type system that introspection returns, plus comments that introspection leaves out. Download them to generate typed client code, to feed a GraphQL plugin in your editor, or to compare two versions of the API and see what changed. Read them together rather than one at a time: each file uses types that the common file declares, and `nvx4-bdr.graphql` declares the root types that refer to all the others.

To explore the API without downloading anything, use [introspection](../graphql-basics/#introspection) from your own GraphQL client, with your access token.

## Versions

Every file except the common one states its schema version in a comment on the third line, for example `# Version: 0.12.0`. Each file is versioned on its own, so the numbers can differ across files. The common file carries no version of its own, because it belongs to no product or platform service. Check the version before you report a difference between the schema and this documentation.

Most changes don't affect an existing client, because a client receives only the fields that its query selects. These are a new field on a type, a new optional argument, a new type, and a new input field that is nullable or has a default. Two changes can break a client that handles every case explicitly: a new member of a union, and a new value of an enum that the API returns. Handle unions with `__typename` or a fallback branch, and give every switch over a returned enum a default branch. For example, treat an unknown `TrackingStreamRemovalReason` as a plain removal and drop the asset from your view.

New [error codes](../error-handling.md) can appear in `extensions.code`, and a failure can move to a different code. Handle an unknown code as a generic failure.
