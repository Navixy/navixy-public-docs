---
description: >-
  Console shows which products your workspace has, allows you to enable or
  disable a product, and lists the available organizations and workspaces.
tags:
  - overview
  - beta
---

# Console overview

**Console** is the product that administers your organizations: their workspaces, their members, and which products each workspace runs. It's one of the products served by [Navixy GraphQL API](../). This documentation is for Navixy users who call the API for their own workspace to see which products it has, to turn a product on or off, and to see which organizations and workspaces they can reach. Navixy Console decides what you may see and do, exactly as it decides in its own interface, and the API relays its answer.

All Console queries are nested under the `console` field of the `Query` type and Console mutations under the `console` field of the `Mutation` type. Authentication and error handling work the same way for every product of Navixy GraphQL API, so see [Authentication](../authentication.md) and [Error handling](../error-handling.md) to learn that. Console's lists page by rules of their own: see [How these connections paginate](api-reference/README.md#how-these-connections-paginate). For the endpoint and request headers, see [Getting started](../getting-started.md).

## Purpose and capabilities

Console API enables you to:

* View the organizations you belong to and the workspaces you can reach
* View the members of your organization, people and agents alike, and what you may do to them
* View the products of a workspace: which ones your plan covers, which are enabled, and what you can do with them
* Enable or disable a product for a workspace and check that the change went through
* View the product catalog of Navixy Console

Your access token names one of your organizations and one of its workspaces. The organization and workspace lists cover every organization you belong to, and you can pass an organization ID to narrow them. The product operations and the member list act only in the organization named by your token, so to change products in another organization, you need a token issued for it.

## Key concepts

<table><thead><tr><th width="147">Term</th><th>Definition</th></tr></thead><tbody><tr><td><strong>Organization</strong></td><td>A company's account in Navixy Console, with its own workspaces, members, and roles. You can belong to several organizations, and each one is separate.</td></tr><tr><td><strong>Workspace</strong></td><td>The space that holds the business data of one customer, with its own plan and products. A workspace belongs to one organization. It's the same workspace that <a href="../business-data-repository/README.md">Business Data Repository</a> and the other products work in.</td></tr><tr><td><strong>Product</strong></td><td>A product of the Navixy platform that a workspace can run, such as Business Data Repository or Tracking.</td></tr><tr><td><strong>Coverage</strong></td><td>Whether the plan of the workspace includes a product. Only a covered product can be enabled. A new subscription counts at once.</td></tr><tr><td><strong>Product state</strong></td><td>Where a product stands in a workspace: not covered, covered but unavailable, enabled or disabled, in the process of being enabled or disabled, or failed. Enabling or disabling a product takes a while and can fail, so check the state until the change is done, as Console doesn't notify you. A disabled product keeps its data.</td></tr><tr><td><strong>Allowed actions</strong></td><td>What you may do with a product right now, enable it or disable it, and why not when you can't.</td></tr></tbody></table>

## Who does what

Three parties decide what happens to the products of a workspace:

<table><thead><tr><th width="200">Who</th><th>Does</th></tr></thead><tbody><tr><td><strong>You</strong></td><td>View your organizations and workspaces, view the products of a workspace with what you may do with each one, enable or disable a product, and check that the change went through.</td></tr><tr><td><strong>The Owner of your organization</strong></td><td>Sets up the roles in Navixy Console that decide who may enable and disable products. Anyone with the <strong>Console administration</strong> feature can do the same.</td></tr><tr><td><strong>Navixy</strong></td><td>Offers the plans in Billing API that decide which products a workspace may enable. Sets up a product after Console accepts the request.</td></tr></tbody></table>

To enable or disable products, you need a role with the **Console administration** feature in the workspace's organization. The Owner role always has it. Viewing your organizations and workspaces doesn't require a role.

Two limits apply: a product that your plan doesn't cover can't be enabled through the API, and a product that the plan requires can't be disabled. To cover a product, subscribe the workspace to a plan that includes it. See [Choosing a plan and subscribing](../billing/guides/choosing-a-plan-and-subscribing.md).

## Navigation

The Console documentation has one guide, a page on errors, and an API reference with one page per topic.

The guide covers the task that you can do with the Console API for your workspace:

* [Enabling and disabling a product](guides/enabling-and-disabling-a-product.md) views the products of a workspace, enables one, checks that the change went through, and disables a product again.

[Console errors](errors.md) documents the errors that the Console operations return, including the refusals that come from Navixy Console.

The [Console API reference](api-reference/) documents the operations and types, one page per topic:

* [Organizations](api-reference/organizations.md) documents the organizations you belong to.
* [Workspaces](api-reference/workspaces.md) documents the workspaces you can reach.
* [Members](api-reference/members.md) documents the members of your organization.
* [Products](api-reference/products.md) documents the products of a workspace, the product catalog, and the changes that enable and disable a product.

For the types that Console shares with the other products and for an index of the whole API, see [Core API reference](../core-api-reference/).

{% hint style="warning" %}
The API supports [GraphQL introspection](../graphql-basics/#introspection) for authenticated users. You can also check the public [GraphQL schema](../developer-resources/public-graphql-schema.md).
{% endhint %}
