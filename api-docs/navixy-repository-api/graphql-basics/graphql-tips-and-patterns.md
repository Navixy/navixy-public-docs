---
description: >-
  Practical GraphQL patterns for cleaner requests: variables, operation names,
  fragments, aliases, and directives.
---

# GraphQL tips and patterns

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

This article covers patterns that make your GraphQL code cleaner, more maintainable, and easier to debug. If you're new to GraphQL, start with [GraphQL basics ](README.md)first.

## Variables

When you're testing queries, it's easy to write values directly into the query string. But real applications need to pass different values each time: a user might click on another device, select a different filter, or navigate to the next page.

Variables solve this by separating what you're asking for (the query) from the specific values (the variables). Think of it like a function: the query is the function definition, and variables are the arguments you pass in.

Here's a query without variables:

```graphql
query GetDevice {
  bdr {
    device(id: "550e8400-e29b-41d4-a716-446655440001") {
      title
    }
  }
}
```

And here's the same query with a variable:

```graphql
query GetDevice($deviceId: ID!) {
  bdr {
    device(id: $deviceId) {
      title
    }
  }
}
```

The variable `$deviceId` is declared in parentheses after the operation name. The `ID!` part specifies the type (must match what the schema expects). Then you use `$deviceId` wherever you need that value in the query.

The actual value is passed separately as JSON:

```json
{
  "deviceId": "550e8400-e29b-41d4-a716-446655440001"
}
```

When you send the request, include both the query and the variables. GraphQL tools and client libraries handle this for you — you just provide the variables object.

### Multiple variables

You can declare as many variables as you need:

```graphql
query ListDevices($workspaceId: ID!, $statusIds: [ID!], $limit: Int = 20) {
  bdr {
    devices(
      workspaceId: $workspaceId
      filter: { statusIds: $statusIds }
      first: $limit
    ) {
      nodes {
        id
        title
      }
    }
  }
}
```

In this example:

* `$workspaceId: ID!` is required (`!` means it cannot be null)
* `$statusIds: [ID!]` is an optional array of IDs (no `!` after the brackets)
* `$limit: Int = 20` is optional with a default value of 20

Variables in JSON:

```json
{
  "workspaceId": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
  "statusIds": ["a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11"],
  "limit": 50
}
```

## Operation names

You can give your operations custom names to make them easier to identify in logs, debugging tools, and network traces.

The name `GetDevice` appears after the operation type (`query`, `mutation`, or `subscription`). It doesn't affect execution, as the API ignores it, but helps in several ways:

* Debugging: Error messages and server logs reference the operation name
* Tooling: GraphQL clients use names for caching and request deduplication
* Analytics: Track which operations are called most frequently
* Readability: Self-documenting code, especially with multiple operations

Mutations benefit from naming too:

```graphql
mutation UpdateDeviceTitle($id: ID!, $version: Int, $title: String) {
  bdr {
    deviceUpdate(input: { id: $id, version: $version, title: $title }) {
      device { id version title }
    }
  }
}
```

Common naming conventions use PascalCase with a verb prefix: `GetDevice`, `ListDevices`, `UpdateAsset`, `CreateGeoObject`, `DeleteSchedule`.

## Fragments

Fragments let you define reusable sets of fields. They're useful when you need the same fields in multiple queries.

Define a fragment on a specific type:

```graphql
fragment DeviceFields on Device {
  id
  version
  title
  status { code title }
  workspace { id title }
}
```

Use it in queries with the spread operator (`...`):

```graphql
query GetDevice($id: ID!) {
  bdr {
    device(id: $id) {
      ...DeviceFields
      identifiers { type value }
    }
  }
}

query ListDevices($workspaceId: ID!) {
  bdr {
    devices(workspaceId: $workspaceId, first: 10) {
      nodes {
        ...DeviceFields
      }
    }
  }
}
```

Both queries now share the same field selection for core device data.

### Inline fragments for interfaces

When working with interfaces like [Node](../common.md#node), use inline fragments to request type-specific fields:

```graphql
query GetNode($id: ID!) {
  node(id: $id) {
    id
    ... on Device {
      title
      status { code }
    }
    ... on Asset {
      title
      type { code title }
    }
    ... on Workspace {
      title
      isActive
    }
  }
}
```

The `node` query returns any entity by ID. The inline fragments (`... on Type`) tell GraphQL what fields to return based on the actual type.

## Aliases

Aliases let you rename fields in the response or fetch the same field multiple times with different arguments.

### Renaming fields

```graphql
query GetTwoDevices {
  bdr {
    truck: device(id: "device-uuid-1") {
      title
    }
    trailer: device(id: "device-uuid-2") {
      title
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "truck": { "title": "Truck 42" },
      "trailer": { "title": "Trailer A7" }
    }
  }
}
```

### Comparing entities

Fetch the same entity at different points or with different parameters:

```graphql
query CompareWorkspaces {
  bdr {
    berlin: workspace(id: "019d48ea-0752-8000-801f-444556437ab1") {
      title
      isActive
      devices(first: 5) {
        total { count }
      }
    }
    munich: workspace(id: "019d48ea-0752-8000-801f-4445564380f2") {
      title
      isActive
      devices(first: 5) {
        total { count }
      }
    }
  }
}
```

### Batch mutations

Aliases enable batch operations in a single request. In a mutation, you can select only one field inside a single `bdr` block, so alias the `bdr` namespace itself, one block per call:

```graphql
mutation BatchUpdateDevices {
  device1: bdr {
    deviceUpdate(input: {
      id: "uuid-1"
      version: 3
      title: "Truck 42 - Updated"
    }) {
      device { id version title }
    }
  }
  device2: bdr {
    deviceUpdate(input: {
      id: "uuid-2"
      version: 5
      title: "Truck 43 - Updated"
    }) {
      device { id version title }
    }
  }
}
```

Selecting two mutation fields inside one `bdr` block returns a validation error instead. This rule applies to mutations only: a query can select as many fields inside one `bdr` block as it needs, like the examples above.

The aliased blocks execute one after another, in the order they appear, and each mutation runs independently. If one fails, the others can still succeed — check the `errors` array for partial failures.

There are no dedicated bulk mutations, so aliased blocks are also how you update many entities in one request. For large programmatic batches, you can omit `version` from each input so that a concurrent change doesn't fail part of the run — see [Optimistic locking](../optimistic-locking.md) for what that trades away.

## Directives

Directives modify how fields are executed. The API supports the standard GraphQL directives plus one custom directive; see [Directives](../directives.md) for the full list. The two you use directly in queries are `@include` and `@skip`, which turn a field on or off based on a variable.

Directives can be used to:

* Hide fields the user can't access
* Skip expensive fields when not needed
* Fetch fewer fields on mobile devices

```graphql
query ListDevices(
  $workspaceId: ID!
  $includeDetails: Boolean!
  $includeAudit: Boolean!
) {
  bdr {
    devices(workspaceId: $workspaceId, first: 20) {
      nodes {
        id
        title
        status @include(if: $includeDetails) { code title }
        workspace @include(if: $includeDetails) { title }
        type @include(if: $includeAudit) { code }
        version @include(if: $includeAudit)
      }
    }
  }
}
```

## See also

* [GraphQL basics](README.md): Learn GraphQL fundamentals, from queries and mutations to the type system
* [Directives](../directives.md): Complete reference for the standard and custom directives
* [Common resources](../common.md): Complete reference for shared scalars, interfaces, and types
* [Error handling](../error-handling.md): Understand error structure, codes, and common error scenarios
