---
description: >-
  IAM holds the roles of your organization. The API lets you view, create, and
  change roles, and assign them to members or revoke them.
tags:
  - overview
  - beta
---

# IAM overview

**IAM API** (identity and access management) is the product that decides who may do what in your organization. It's one of the products served by [Navixy GraphQL API](../). IAM stores the organization's roles, what each role gives access to, and who holds each role. This documentation is for Navixy users who call the API for their own organization to set up roles and assign them to members.

All IAM queries are nested under the `iam` field of the `Query` type and IAM mutations under the `iam` field of the `Mutation` type. Authentication and error handling work the same way for every product of Navixy GraphQL API, so see [Authentication](../authentication.md) and [Error handling](../error-handling.md) to learn that. IAM lists are paged differently from the other products: see [How these lists paginate](api-reference/README.md#how-these-lists-paginate). For the endpoint and request headers, see [Getting started](../getting-started.md).

## Purpose and capabilities

IAM API enables you to:

* View the roles of your organization and the roles you can assign in your workspace
* Create a role, change what it gives access to, and delete it
* See which roles a member holds and who holds a role
* Assign a role to a member, permanently or until a set time, and revoke it

Your access token names one of your organizations and one of its workspaces. IAM works with the roles of that organization. To work with the roles of another organization, you need a token issued for it.

## Key concepts

<table><thead><tr><th width="160">Term</th><th>Definition</th></tr></thead><tbody><tr><td><strong>Role</strong></td><td>A named set of access rights in one organization. It grants no access rights in the organization's child organizations. A role is active or inactive. An inactive role continues to exist but grants no access. Its assignments remain and resume working once the role is active.</td></tr><tr><td><strong>Owner role</strong></td><td>The role that IAM creates for every organization. It gives access to everything in the organization. It can't be changed, deleted, or assigned through the API, and IAM won't revoke it from its last holder.</td></tr><tr><td><strong>Admin role</strong></td><td>A role that IAM creates together with the Owner role. It gives the same access to everything in the organization, but it's an ordinary role: you can change it, delete it, and assign it. IAM assigns it to nobody.</td></tr><tr><td><strong>Workspace reach</strong></td><td>The workspaces of the organization where a role applies: all of them, including the ones created later, or only the ones you pick.</td></tr><tr><td><strong>Product access</strong></td><td>The products a role gives access to. For each product, a role gives access to all of it, or only to some of its features and data resources.</td></tr><tr><td><strong>Data resource</strong></td><td>A kind of data inside a product. A role can give access to all items of that kind or only to some, and set which actions are allowed on each item.</td></tr><tr><td><strong>Member</strong></td><td>A person or an API key that belongs to the organization in Navixy Console. Only a member can be assigned a role. The API identifies a member by an actor ID issued by Navixy Console. Your own actor ID is in your access token, and the actor IDs of the other members come from the <code>members</code> query of <a href="../console/api-reference/members.md">Console</a>.</td></tr><tr><td><strong>Role assignment</strong></td><td>A role that a member holds. An assignment is permanent or ends at a set time.</td></tr></tbody></table>

## How roles work in an organization

1. Navixy Console creates the organization. IAM creates its Owner role and assigns it to the person who created the organization. IAM also creates an Admin role with the same access, which nobody holds yet.
2. Someone with access to IAM creates the roles the organization needs. Each role sets the workspaces where it applies and the products it gives access to.
3. They assign roles to members. A role can be assigned only while it's active, and only in a workspace where it applies.
4. To change a role, you send the whole role again. If someone else changed the role after you read it, IAM refuses the change, so read the role again and repeat the change.
5. Revoking a role ends the assignment. Deleting a role revokes it from everyone who holds it.

## Who does what

Three parties share the work on roles:

<table><thead><tr><th width="200">Who</th><th>Does</th></tr></thead><tbody><tr><td><strong>You</strong></td><td>View the roles and who holds them, create, change, and delete roles, and assign or revoke roles. You can't give anyone more access than your own roles give you, or for longer than your own access lasts.</td></tr><tr><td><strong>IAM</strong></td><td>Creates the Owner role of each organization and protects it. Checks every request against your own roles before it makes a change.</td></tr><tr><td><strong>Navixy Console</strong></td><td>Keeps the list of members of each organization, which you can read through its <code>members</code> query. IAM refuses to assign a role to anyone who isn't on that list.</td></tr></tbody></table>

To use the IAM API, you need a role that gives access to IAM in your organization. The Owner role always has it, and so does the Admin role unless someone changed it.

When a member leaves the organization or is removed from it, IAM revokes all their roles, so they drop out of the holder lists. A role that you have just created or changed can take about a second to appear among the roles you can assign.

## Navigation

The IAM documentation has an API reference with one page per topic.

The [IAM API reference](api-reference/) documents the operations and types, explains how IAM lists are paged, and lists the refusals that come from IAM:

* [Roles](api-reference/roles.md) documents the roles of your organization and the changes that create, replace, and delete a role.
* [Role access](api-reference/role-access.md) documents what a role gives access to: workspaces, products, features, and data resources.
* [Role assignments](api-reference/role-assignments.md) documents who holds which role and the changes that assign and revoke a role.

For the types that IAM shares with the other products and for an index of the whole API, see [Core API reference](../core-api-reference/).

{% hint style="warning" %}
The API supports [GraphQL introspection](../graphql-basics/#introspection) for authenticated users. You can also check the public [GraphQL schema](../developer-resources/public-graphql-schema.md).
{% endhint %}
