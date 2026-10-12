---
description: >-
  How the Console reference is organized: one page each for the organizations
  and the workspaces a caller can reach and for the products of a workspace,
  and how Console's connections paginate.
tags:
  - reference
  - beta
---

# Console API reference

The Console API reference documents the operations and types that Navixy GraphQL API exposes from Navixy Console: the organizations and workspaces the caller can reach, and the products a workspace has. Each page covers its queries and mutations, its object and input types, and its enums, with a field table for every type.

The reference states what each operation and field is. For the GraphQL fundamentals behind the notation, see [GraphQL basics](../../graphql-basics/), including the [type syntax](../../graphql-basics/#type-syntax) that the field tables use.

## Where the operations live

All Console queries are nested under the `console` field of the `Query` type, and the Console mutations under the `console` field of the `Mutation` type. Every response has the same shape under `data.console`. A `console { ... }` mutation block runs one mutation at a time.

Navixy Console owns organizations, workspaces, and the products a workspace runs. This API relays what Console reports for the caller: the values of `status`, `orgType`, `roles`, `operationType`, the operation `status`, and a member's `principalType`, `principalSubtype`, `rolePreset`, and `status` are Console's own vocabulary, returned as plain strings. The `roles` of an organization record your membership, such as `owner` or `member`, and don't decide what you may do: that comes from the roles set up in Navixy Console. The `accessibleWorkspaces` query leaves out any workspace whose ID isn't a workspace ID of this deployment.

Enabling or disabling a product is asynchronous. The mutation answers as soon as Console accepts the request, with the operation that Console tracks for it. Read the outcome with `workspaceOperation` or `workspaceProducts`. A request that Console refuses comes back as an error that names Console: see [Refusals relayed from Navixy Console](../../error-handling.md#refusals-relayed-from-navixy-console).

## Pages

<table><thead><tr><th width="186">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="organizations.md">Organizations</a></td><td><code>ConsoleOrganization</code>, with the caller's own roles in it, and the <code>accessibleOrganizations</code> query with its connection and edge types.</td></tr><tr><td><a href="workspaces.md">Workspaces</a></td><td><code>ConsoleWorkspace</code>, with the organization and the zone it belongs to, and the <code>accessibleWorkspaces</code> query with its connection and edge types.</td></tr><tr><td><a href="members.md">Members</a></td><td><code>ConsoleMember</code>, a person or agent of your organization with what you may do to them, and the <code>members</code> query with its connection and edge types.</td></tr><tr><td><a href="products.md">Products</a></td><td>The products a workspace may or does have enabled (<code>ConsoleWorkspaceProduct</code>), Console's product catalog (<code>ConsoleProductCatalogEntry</code>), and the operation Console tracks for a toggle (<code>ConsoleWorkspaceOperation</code>), with the <code>workspaceProducts</code>, <code>productCatalog</code>, and <code>workspaceOperation</code> queries and the <code>workspaceProductEnable</code> and <code>workspaceProductDisable</code> mutations.</td></tr></tbody></table>

## How these connections paginate

The Console connections follow Console's own pagination, which differs from the [pagination](../../pagination.md) the other products use:

* Backward paging needs a cursor. `first` and `after` page forward. `last` pages backward only together with `before`, and `before` is only valid together with `last`. `first` doesn't combine with `last` or `before`, and `last` doesn't combine with `after`. The API checks these rules before it calls Console and answers `VALIDATION_ERROR` when they are broken.
* Every edge carries its own cursor. `pageInfo.startCursor` and `pageInfo.endCursor` are the first and the last cursor of the page, and they are null only when Console's own page had no items. A page whose rows the API left out still carries them.
* `total` is exact on `accessibleOrganizations` and approximate on `accessibleWorkspaces`, because Console counts every workspace, including the ones this API leaves out.
* A cursor is valid only for the query that returned it. Passing it to the other query returns an unrelated page, not an error.
* `accessibleWorkspaces` can return fewer items than `first`, or none, while `pageInfo.hasNextPage` is still `true`: it leaves out any workspace whose ID isn't a workspace ID of this deployment without adjusting the page size. Keep paging past a short or empty page. `accessibleOrganizations` doesn't leave out rows this way.
* `members` is paged by the API, not by Console, because Console answers the whole member list at once. The page is ordered by `actorId`, the cursor of each edge is the actor ID, and `total` is exact. A cursor that isn't an actor ID returns `VALIDATION_ERROR`.
* The default page size is 50, and `first` and `last` accept 1 to 100 on every query. The default of 20 on the general [Pagination](../../pagination.md) page doesn't apply here.

A short page is what the last rule looks like. This request asks for 50 workspaces after a cursor:

```graphql
query NextWorkspaces {
  console {
    accessibleWorkspaces(
      first: 50
      after: "d3M6MDE5YTZiMmYtNzkzZS04MDdiLTgwMDEtNTc1MzUwNDMwMDAy"
    ) {
      nodes {
        id
        workspaceName
      }
      pageInfo {
        hasNextPage
        endCursor
      }
    }
  }
}
```

The response has no nodes and still has a next page:

```json
{
  "data": {
    "console": {
      "accessibleWorkspaces": {
        "nodes": [],
        "pageInfo": {
          "hasNextPage": true,
          "endCursor": "d3M6MDE5YTZiMmYtNzkzZS04MDdiLTgwMDEtNTc1MzUwNDMwMDM0"
        }
      }
    }
  }
}
```

Send `endCursor` as `after` again. Stop only when `hasNextPage` is `false`.

## Identifiers

`ConsoleOrganization.id` is an `ID` that Console issues, the same value as `Workspace.organizationId` in Business Data Repository. `ConsoleOrganization` is a `Node`. `node(id:)` and `nodes(ids:)` return an organization only when it's one of your own organizations, the organizations that `accessibleOrganizations` lists, and return `null` for any other organization ID. When Console doesn't respond, the organization is `null` and an error names its path. See [When Console doesn't respond](../errors.md#when-console-doesnt-respond).

`ConsoleWorkspace.id` is an `ID`, the same value as `Workspace.id` in Business Data Repository. `ConsoleWorkspace` isn't a `Node`: for a workspace ID, `node(id:)` returns the Business Data Repository `Workspace` of the workspace that your token names. `ConsoleMember.actorId` is a `UUID` of Console's own: the `actorId` that the IAM role assignment operations take.

Product IDs are Billing's. `ConsoleWorkspaceProduct.productId`, `ConsoleProductCatalogEntry.id`, and the `productId` that `workspaceProductDisable` takes are the same values as `BillingProductDomain.id`, and `coveredBySubscriptionId` is a Billing subscription ID.

An operation ID is an `ID` that Console issues: `ConsoleWorkspaceOperation.id`, `ConsoleWorkspaceOperationReceipt.id`, and the `id` that `workspaceOperation` takes. No type of the API is an operation, so `node(id:)` returns `null` for an operation ID. The `organizationId` of an operation is the organization ID described above. The IDs of the user or agent behind an operation, `actorUserId` and `actorAgentId`, are `UUID` values of Console's own.

The API checks the kind of every organization ID and operation ID that you send. The `organizationId` of `accessibleWorkspaces` must be an organization ID, and the `id` of `workspaceOperation` must be an operation ID. Any other value returns `VALIDATION_ERROR`: see [Errors before Console is called](../errors.md#errors-before-console-is-called).

## Shared types

The interfaces and pagination types that Console shares with the other products are on the [Common resources](../../core-api-reference/common.md) page. The [Core API reference](../../core-api-reference/README.md) indexes the reference pages of every product.

## Other ways to explore the API

* [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox): Run queries and mutations interactively.
* [Public GraphQL schema](../../developer-resources/public-graphql-schema.md): The full type system split into several files.
* [Introspection](../../graphql-basics/#introspection): Explore the schema from your own GraphQL client.

## See also

* [Authentication](../../authentication.md): Learn how the access token names the workspace that every request acts in.
* [Error handling](../../error-handling.md): Understand error structure, codes, and common error scenarios.
