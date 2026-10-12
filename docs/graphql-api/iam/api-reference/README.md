---
description: >-
  How the IAM reference is organized: one page each for roles, for what a role
  grants, and for who holds a role, and how the IAM lists paginate.
tags:
  - reference
  - beta
---

# IAM API reference

The IAM API reference documents the operations and types that Navixy GraphQL API exposes from IAM, the identity and access management service of the platform: the roles of your organization, what each role grants, and who holds it. Each page covers its queries and mutations, its object and input types, and its enums, with a field table for every type.

The reference states what each operation and field is. For the GraphQL fundamentals behind the notation, see [GraphQL basics](../../graphql-basics/), including the [type syntax](../../graphql-basics/#type-syntax) that the field tables use.

## Where the operations live

All IAM queries are nested under the `iam` field of the `Query` type, and the IAM mutations under the `iam` field of the `Mutation` type. Every response has the same shape under `data.iam`.

Every operation acts in the organization and the workspace that your [access token](../../authentication.md) names, so no field takes an organization ID or a workspace ID. A token that names no organization or no workspace is refused with `PERMISSION_DENIED`. A role of another organization is out of reach: `role` returns null for it.

IAM decides what you may do. To call any `iam` operation, you need a role that grants product `iam` or every product. The Owner role grants every product. You can't create, change, assign, or revoke a role that grants more than your own roles do. IAM refuses such a request with `PERMISSION_DENIED` and `dependencyCode` `GRANT_CEILING_EXCEEDED`. A role that grants every workspace, every product, or every feature or data resource of a product needs a caller whose own roles grant the same. A list that happens to name every item doesn't count as "every".

## Pages

<table><thead><tr><th width="186">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="roles.md">Roles</a></td><td><code>IamRole</code> with its connection and edge types, the <code>roles</code>, <code>role</code>, and <code>workspaceRoles</code> queries, and the <code>roleCreate</code>, <code>roleUpdate</code>, and <code>roleDelete</code> mutations with their inputs and payloads.</td></tr><tr><td><a href="role-access.md">Role access</a></td><td>What a role grants: <code>IamSelection</code> for the workspaces it reaches, and the product scopes, features, and data resources it covers (<code>IamRoleProductScopes</code>, <code>IamRoleProductScope</code>, <code>IamRoleDataResources</code>, <code>IamRoleDataResourceGrant</code>, <code>IamRoleItemActions</code>), with the input type for each.</td></tr><tr><td><a href="role-assignments.md">Role assignments</a></td><td><code>IamMemberRoleAssignment</code>, <code>IamRoleHolder</code>, and <code>IamRolePrincipalType</code>, the <code>memberRoles</code> and <code>roleHolders</code> queries, and the <code>memberRoleAssign</code> and <code>memberRoleRevoke</code> mutations with their inputs and payloads.</td></tr></tbody></table>

## How these lists paginate

The two IAM connections page differently, and both differ from the [pagination](../../pagination.md) the other products use:

* `roles` pages in both directions. `first` and `after` page forward. `last` pages backward only together with `before`, and `before` is only valid together with `last`. The API answers `VALIDATION_ERROR` when these rules are broken.
* `roles` is ordered by role ID, and the cursor of each edge is the role ID. `total` is exact and counts inactive roles too.
* `roleHolders` pages forward only. `last` and `before` are refused with `VALIDATION_ERROR`, and `total` is always null. For the number of holders, read `IamRole.holderCount`.
* The default page size is 50, and `first` and `last` accept 1 to 100. The default of 20 on the general [Pagination](../../pagination.md) page doesn't apply here.

`workspaceRoles` and `memberRoles` return plain lists, not connections.

## Identifiers

`IamRole.id` and every `roleId` are `ID` values that IAM issues. `IamRole` isn't a `Node`, so `node(id:)` doesn't return it. Read a role through `role` or `roles`. Role titles aren't unique, so identify a role by its ID.

`IamRole.organizationId` is an `ID`, the same value as `ConsoleOrganization.id` in [Console](../../console/api-reference/organizations.md). For this ID, `node(id:)` returns the organization when it's one of your own organizations. If IAM returns a role whose `organizationId` isn't an organization ID, the operation that returns the role fails with `SERVICE_UNAVAILABLE` and `dependency: "iam"`.

`actorId` is a `UUID` that Navixy Console issues for a person or an integration. Your own actor ID is the `actor_id` claim of your [access token](../../authentication.md#access-token-claims). To find the actor ID of another member, read [members](../../console/api-reference/members.md#members) in Console.

`productCode` in `IamRoleProductScope` and `IamRoleProductScopeInput`, and `resourceType` in `IamRoleDataResourceGrant` and `IamRoleDataResourceGrantInput`, are [`Code`](../../core-api-reference/common.md#code) values. An input with a value that isn't a valid code is refused before the request runs, as a `VALIDATION_ERROR` without a `field`.

## Refusals relayed from IAM

The `iam` operations call IAM, and IAM checks every request itself. When IAM refuses a request, the API answers with the matching [error code](../../error-handling.md#error-codes) and marks the error with `dependency: "iam"`. `dependencyCode` holds IAM's own reason, such as `GRANT_CEILING_EXCEEDED`, `NOT_A_MEMBER`, or `LAST_OWNER`. `entityType` is `IamMemberRoleAssignment` on `memberRoles` and `memberRoleRevoke`, and `IamRole` on the other operations. `entityId` holds the role ID when the request named a role.

## Shared types

The interfaces and pagination types that IAM shares with the other products are on the [Common resources](../../core-api-reference/common.md) page. The [Core API reference](../../core-api-reference/README.md) indexes the reference pages of every product.

## Other ways to explore the API

* [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox): Run queries and mutations interactively.
* [Public GraphQL schema](../../developer-resources/public-graphql-schema.md): The full type system split into several files.
* [Introspection](../../graphql-basics/#introspection): Explore the schema from your own GraphQL client.

## See also

* [Authentication](../../authentication.md): Learn how the access token names the organization and the workspace that every request acts in.
* [Error handling](../../error-handling.md): Understand error structure, codes, and common error scenarios.
* [Optimistic locking](../../optimistic-locking.md): Pass the `version` you last read to `roleUpdate` and `roleDelete`.
