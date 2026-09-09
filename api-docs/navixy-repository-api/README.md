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

| Product                                                                                                                                                                                    | Documentation |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------- |
| [Business Data Repository](business-data-repository/overview.md): assets, devices and inventories, geo objects, schedules, custom fields, catalogs, and the audit log. Entry point: `bdr`. | Available     |
| [Tracking](tracking/)                                                                                                                                                                      | Coming soon   |
| IoT Logic. Updated GraphQL version of [IoT Logic API](https://navixy.com/docs/iot-logic-api/).                                                                                             | Coming soon   |
| Alerts                                                                                                                                                                                     | Coming soon   |
| Reports                                                                                                                                                                                    | Coming soon   |

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

| Term         | Definition                                                                                                                                                                                                                                   |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Organization | The Navixy Console account that owns one or more workspaces.                                                                                                                                                                                 |
| Workspace    | The tenant that owns the business data of one customer: members, assets, devices, geo objects, and schedules. Most queries and mutations take a `workspaceId` argument. Navixy Console creates workspaces, so they are read-only in the API. |
| Product      | A group of related operations under one entry point, such as `bdr`.                                                                                                                                                                          |
| Actor        | The party that an access token represents: a user or an integration.                                                                                                                                                                         |
| Access token | An OpenID Connect token issued by the Navixy identity service. Every request sends one in the `Authorization` header.                                                                                                                        |

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
* [Tracking](tracking/)

## Developer resources

* [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox): Run queries and mutations interactively against a demo environment.
* [Public GraphQL schema](https://raw.githubusercontent.com/Navixy/navixy-api/refs/heads/master/docs/navixy-repository-api/graphql-schema/schema.graphql): The full type system in one file.
* [Introspection](graphql-basics/#introspection): Explore the schema from your own GraphQL client.
