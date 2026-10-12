---
description: Cursor-based pagination in Navixy GraphQL API
tags:
  - fundamentals
  - beta
---

# Pagination

When querying lists of entities (devices, assets, users, etc.), the API returns paginated results. This prevents overwhelming responses when you have thousands of records.

To control which results appear and in what order, see [Filtering and sorting](filtering-and-sorting/).

The rules on this page apply to every product of the API, with one exception. Console and IAM lists have a different default page size and page backward differently. See the end of [Pagination parameters](pagination.md#pagination-parameters).

## Where pagination applies

The API has two types of queries for fetching data:

* Single-entity queries return one item by ID. They use singular names: `device`, `asset`, `schedule`.
* List queries return multiple items matching your criteria. They use plural names: `devices`, `assets`, `schedules`.

Pagination applies to all list queries. They return connection types following a consistent pattern: `devices` returns `DeviceConnection`, `assets` returns `AssetConnection`, `auditEvents` returns `AuditEventConnection`, and so on.

Many nested fields are also paginated, for example `Workspace.devices` or `AssetGroup.currentAssets`. You can identify paginated fields by their return type (anything ending in `Connection`) or by the presence of `first`, `after`, `last`, and `before` arguments.

Pagination types are described in separate sections of each entity's page. See [Asset pagination types](business-data-repository/api-reference/assets/#pagination-types) as an example. You can also use [introspection](graphql-basics/#introspection) to explore them in your GraphQL client.

## The Connection pattern

Navixy GraphQL API uses [Relay Cursor Connections](https://relay.dev/graphql/connections.htm), a pagination standard from the GraphQL community. Instead of traditional page numbers, they use opaque cursors that point to specific positions in the result set.

Using cursors instead of page numbers enables the following:

* Stable results: If new items are added while you're paginating, you won't see duplicates or miss items.
* Efficient: The database can resume from exact positions without re-scanning.
* Flexible: Works well with real-time data that changes frequently.

## Paginated query structure

{% hint style="info" %}
Examples on this page use Business Data Repository queries. The same pattern applies to every product served by Navixy GraphQL API.
{% endhint %}

Every paginated query returns a **Connection** type with this structure (we're using the `device` entity as an example, but the principle is the same):

```graphql
type DeviceConnection {
  edges: [DeviceEdge!]!    # List of results with cursors
  nodes: [Device!]!        # Direct access to entities (shorthand)
  pageInfo: PageInfo!      # Pagination metadata
  total: CountInfo         # Total matching items (may be null)
}

type DeviceEdge {
  node: Device!            # The actual entity
  cursor: String!          # Position marker for this item
}

type PageInfo {
  hasNextPage: Boolean!    # More items after this page?
  hasPreviousPage: Boolean! # More items before this page?
  startCursor: String      # Cursor of first item in this page
  endCursor: String        # Cursor of last item in this page
}

type CountInfo {
  count: Int!              # The number of matching items
  precision: CountPrecision! # How far the count can be trusted
}
```

## Reading results

You can access entities in two ways:

* `edges[].node`: Includes a cursor for each item (useful when you need cursors)
* `nodes[]`: Direct array of entities (simpler when you only need the data)

Check `pageInfo.hasNextPage` to determine if more data is available.

## Pagination parameters

Pagination arguments are passed directly to the query:

<table><thead><tr><th width="175.4444580078125">Parameter</th><th width="165.4444580078125">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>first</code></td><td>Int</td><td>Number of items to fetch from the start (default: 20, max: 100)</td></tr><tr><td><code>after</code></td><td>String</td><td>Cursor: fetch items after this position</td></tr><tr><td><code>last</code></td><td>Int</td><td>Number of items to fetch from the end</td></tr><tr><td><code>before</code></td><td>String</td><td>Cursor: fetch items before this position</td></tr></tbody></table>

Use `first`/`after` for forward pagination (most common). Use `last`/`before` for backward pagination.

Requesting more than 100 items doesn't cap the page at 100: it returns a [validation error](error-handling.md#validation-error-400).

The Console connections, `accessibleOrganizations` and `accessibleWorkspaces`, follow Console's own rules: a default page size of 50, backward paging only from a cursor, and a total that can be approximate. See [Console API reference](console/api-reference/README.md#how-these-connections-paginate). The IAM connections use the same page size and backward paging. `roleHolders` pages forward only and returns no `total`. See [IAM API reference](iam/api-reference/README.md#how-these-lists-paginate).

## Forward pagination

Start by requesting the first page, then use `endCursor` to get subsequent pages.

### First page

{% code overflow="wrap" %}
```graphql
query {
  bdr {
    devices(
      first: 20
    ) {
      nodes {
        id
        title
        status { code title }
      }
      pageInfo {
        hasNextPage
        endCursor
      }
      total {
        count
        precision
      }
    }
  }
}
```
{% endcode %}

Response:

{% code overflow="wrap" %}
```json
{
  "data": {
    "bdr": {
      "devices": {
        "nodes": [
          { "id": "018f1a2b-0000-7000-8000-000000000001", "title": "Truck 01", "status": { "code": "active", "title": "Active" } },
          { "id": "018f1a2b-0000-7000-8000-000000000002", "title": "Truck 02", "status": { "code": "active", "title": "Active" } },
          ...
        ],
        "pageInfo": {
          "hasNextPage": true,
          "endCursor": "VElUTEU6QVNDOlRydWNrIDIwfDAxOGYxYTJiLTAwMDAtNzAwMC04MDAwLTAwMDAwMDAwMDAyMA"
        },
        "total": {
          "count": 142,
          "precision": "EXACT"
        }
      }
    }
  }
}
```
{% endcode %}

### Next page

To fetch the next page, pass `endCursor` from the previous response as the `after` argument:

```graphql
query {
  bdr {
    devices(
      first: 20
      after: "VElUTEU6QVNDOlRydWNrIDIwfDAxOGYxYTJiLTAwMDAtNzAwMC04MDAwLTAwMDAwMDAwMDAyMA" # endCursor from the previous response
    ) {
      nodes {
        id
        title
      }
      pageInfo {
        hasNextPage
        endCursor
      }
    }
  }
}
```

Repeat this pattern, using each response's `endCursor` as the next request's `after` until `hasNextPage` is `false`.

## Backward pagination

Use `last` and `before` to paginate from the end of the result set. This is useful for showing the most recent items first.

```graphql
query {
  bdr {
    auditEvents(
      last: 20
    ) {
      nodes {
        eventType
        occurredAt
      }
      pageInfo {
        hasPreviousPage
        startCursor
      }
    }
  }
}
```

### Previous page

To get the previous page, use `startCursor` as the `before` argument:

```graphql
query {
  bdr {
    auditEvents(
      last: 20
      before: "T0NDVVJSRURfQVQ6REVTQzoyMDI2LTA5LTMwVDEwOjQ1OjAwWnwwMTlhNmIyZi03OTNlLTgwN2ItODAwMS00MTU1NDQ1NDAwMjE" # startCursor from the previous response
    ) {
      nodes {
        eventType
        occurredAt
      }
      pageInfo {
        hasPreviousPage
        startCursor
      }
    }
  }
}
```

## How total count works

The `total` field returns information about how many items match your query. Because counting can be expensive for large datasets, the API provides a `precision` field that indicates how the count was determined:

```graphql
query {
  bdr {
    devices(
      first: 50
    ) {
      nodes {
        id
        title
      }
      total {
        count      # e.g., 1523
        precision  # e.g., EXACT or APPROXIMATE
      }
    }
  }
}
```

<table><thead><tr><th width="163.20001220703125">Precision</th><th>Meaning</th></tr></thead><tbody><tr><td><code>EXACT</code></td><td>The count is exact.</td></tr><tr><td><code>APPROXIMATE</code></td><td>The count isn't exact. The description of the connection's <code>total</code> field says how it can differ from the number of items you can page through.</td></tr></tbody></table>

`total` counts only the entities that your roles let you read. Every count is `EXACT`, except `accessibleWorkspaces` of Console, which is `APPROXIMATE`: its count can exceed the number of workspaces that the connection returns. Handle `APPROXIMATE` in your UI, for example by displaying "About 10,000 results", and treat a precision that you don't know as `APPROXIMATE`.

## Best practices

Choose an appropriate page size. Use 20–50 items for UI lists, up to 100 for background sync. The default is 20 if not specified, and the maximum is 100 per request.

Keep sort parameters consistent. A cursor encodes a position in one sort order. Changing `orderBy` between requests returns no error, but the next page is then cut at a position that means nothing in the new order, so items are skipped or repeated. Always use the same `orderBy` value when paginating through a result set. If you need a different sort order, start pagination from the beginning.

Don't store cursors long-term. Cursors are meant for immediate pagination within a session. They may become invalid after data changes affecting sort order, server updates, or extended time periods.

Handle empty results. Always check for empty `nodes` or `edges` arrays before processing results.

No random page access. Cursor-based pagination doesn't support "jump to page 50": you can only navigate sequentially through results. This is a deliberate trade-off for stability and performance. If you need random access to pages, consider limiting the result set with filters first.

## See also

* [Filtering and sorting](filtering-and-sorting/): Narrow list queries and control result order
* [Error handling](error-handling.md): Understand error structure, codes, and common error scenarios
