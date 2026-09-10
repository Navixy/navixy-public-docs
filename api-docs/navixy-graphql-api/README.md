---
description: >-
  Navixy GraphQL API is the programming interface of the Navixy platform: which
  products it serves, how requests are shaped, and where to start.
---

# Overview

{% include ".gitbook/includes/navixy-graphql-api-is-a-....md" %}

{% hint style="warning" %}
Navixy GraphQL API is separate from the RESTful [Navixy API](https://navixy.com/docs/navixy-api/). The two APIs have different endpoints, authentication, and data models. Use the REST documentation for the Platform API and the Admin Panel API, and this space for the GraphQL API.
{% endhint %}

**Navixy GraphQL API** is the programming interface of the Navixy platform. One endpoint and one schema serve every platform product: business data, tracking, IoT Logic, alerts, and reports. You describe the data that you need in a single query, and the API returns exactly that shape.

The API is based on GraphQL. Every request is an HTTP POST to one endpoint, authenticated with an OpenID Connect access token. Navixy provides the endpoint URL together with your credentials. To send your first request, see [Getting started](getting-started.md). For the token, see [Authentication](authentication.md). If you're new to GraphQL, start with [GraphQL basics](graphql-basics/).

## Products

Each product in the platform has its own entry point in the schema: a top-level field of the `Query` and `Mutation` types that groups the operations of that product. The documentation is published product by product as each one becomes available.

<table><thead><tr><th width="567">Product</th><th>Documentation</th></tr></thead><tbody><tr><td><a href="business-data-repository/overview.md">Business Data Repository</a>: assets, devices and inventories, geo objects, schedules, custom fields, catalogs, and the audit log.</td><td>Available for preview</td></tr><tr><td><a href="tracking/overview.md">Tracking</a>: the live state of assets and their devices, the streams that follow it, and trips and stops.</td><td>Available for preview</td></tr><tr><td>IoT Logic. Updated GraphQL version of <a href="https://navixy.com/docs/iot-logic-api/">IoT Logic API</a>.</td><td>Coming soon</td></tr><tr><td>Alerts</td><td>Coming soon</td></tr><tr><td>Reports</td><td>Coming soon</td></tr></tbody></table>

Navixy Console has a separate API that isn't public yet. Console owns organizations and workspaces, so those entities are read-only in Navixy GraphQL API.

## How requests work

Every operation is nested under the field of its product, and the response has the same shape. The following query reads the title of a workspace through the Business Data Repository product:

```graphql
query {
  bdr {
    workspace(id: "019d48ea-0752-8000-801f-444556437ab1") {
      title
    }
  }
}
```

```json
{
  "data": {
    "bdr": {
      "workspace": {
        "title": "Northwind Logistics"
      }
    }
  }
}
```

Send the query in the `query` field of a JSON body, with any variables in the `variables` field. [Getting started](getting-started.md) shows the full HTTP request.

## Key concepts

These terms apply to every product. Each product section defines its own entities.

<table><thead><tr><th width="200">Term</th><th>Definition</th></tr></thead><tbody><tr><td>Organization</td><td>The Navixy Console account that owns one or more workspaces.</td></tr><tr><td>Workspace</td><td>The tenant that owns the business data of one customer: members, assets, devices, geo objects, and schedules. Most queries and mutations take a <code>workspaceId</code> argument. Navixy Console creates workspaces, so they are read-only in the API.</td></tr><tr><td>Product</td><td>A group of related operations under one entry point, such as <code>bdr</code>.</td></tr><tr><td>Actor</td><td>The party that an access token represents: a user or an integration.</td></tr><tr><td>Access token</td><td>An OpenID Connect token issued by the Navixy identity service. Every request sends one in the <code>Authorization</code> header.</td></tr></tbody></table>

## Navigation

The pages at the top of this space apply to every product:

* [GraphQL basics](graphql-basics/): GraphQL for developers who know REST, plus [tips and patterns](graphql-basics/graphql-tips-and-patterns.md).
* [Getting started](getting-started.md): Your first authenticated requests.
* [Authentication](authentication.md): How to obtain and send an access token.
* [Pagination](pagination.md) and [Filtering and sorting](filtering-and-sorting/): How to page through list results and narrow them down.
* [Error handling](error-handling.md): Error structure, codes, and common error scenarios.
* [Optimistic locking](optimistic-locking.md): How the API prevents concurrent updates from overwriting each other.
* [Limits](limits.md): Request, pagination, and input size limits.

Each product has its own section with an overview, guides, and the API reference:

* [Business Data Repository](business-data-repository/overview.md)
* [Tracking](tracking/overview.md)

## Developer resources

* [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox): Run queries and mutations interactively against a demo environment.
* [Public GraphQL schema](developer-resources/public-graphql-schema.md): The full type system, in one file for the shared types and one per product.
* [Introspection](graphql-basics/#introspection): Explore the schema from your own GraphQL client.
