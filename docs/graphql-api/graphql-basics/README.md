---
description: >-
  Learn GraphQL fundamentals for Navixy GraphQL API: how it differs from REST,
  queries, mutations, subscriptions, record IDs, and core type concepts.
tags:
  - fundamentals
  - beta
---

# GraphQL basics

This article introduces GraphQL for developers who are new to it. If you've worked with REST APIs before, you'll find GraphQL takes a different approach to requesting and modifying data.

For detailed information, see [the official GraphQL documentation](https://graphql.org/learn/) and [GraphQL specification](https://spec.graphql.org/).

## What is GraphQL?

GraphQL is a query language for APIs. The key idea behind it is that you specify exactly what data you need, and the API returns only that data. This is different from REST APIs, where each endpoint returns a fixed structure, and you often get more data than you need (or have to make multiple requests to get everything you need).

## How GraphQL differs from REST

If you've used REST APIs, you're familiar with this pattern:

* Different endpoints for different resources (`/devices`, `/devices/123`, `/assets`)
* HTTP methods determine the action (GET to read, POST to create, PUT to update, DELETE to remove)
* Each endpoint returns a predefined data structure

GraphQL works differently:

| Aspect             | REST                                       | GraphQL                                     |
| ------------------ | ------------------------------------------ | ------------------------------------------- |
| Endpoints          | Multiple (`/devices`, `/assets`, `/users`) | Single (`/graphql`)                         |
| Data returned      | Fixed structure per endpoint               | You specify exactly what fields you want    |
| Multiple resources | Multiple requests needed                   | One request can fetch related data          |
| HTTP methods       | GET, POST, PUT, DELETE                     | Always POST (with query or mutation inside) |

### Sample request

Imagine you need to display a device's title, its status name, and the workspace it belongs to.

With REST, you might need multiple requests:

```bash
GET /devices/123     # returns device data including status_id and workspace_id

GET /statuses/456    # returns status data

GET /workspaces/789  # returns workspace data
```

With GraphQL, you make one request and specify exactly what you need:

```graphql
query {
  bdr {
    device(id: "123") {
      title
      status {
        title
      }
      workspace {
        title
      }
    }
  }
}
```

The response contains only what you asked for:

```json
{
  "data": {
    "bdr": {
      "device": {
        "title": "Truck 42",
        "status": {
          "title": "Active"
        },
        "workspace": {
          "title": "Berlin Fleet"
        }
      }
    }
  }
}
```

## Single endpoint

Navixy GraphQL API has one endpoint: `https://nvx4.eu.navixy.com/api/graphql`.

Every query and mutation goes to the same URL, and the request body tells the API what you want to do. A subscription uses the same URL over server-sent events, or a WebSocket connection with its own path. See [Subscriptions](./#subscriptions).

## Product namespaces

Navixy GraphQL API serves more than one product through this endpoint, and each product has its own entry point. All Business Data Repository operations are nested under the `bdr` field, so every query and mutation in these docs starts the same way:

```graphql
query {
  bdr {
    devices(first: 10) {
      nodes { id title }
    }
  }
}
```

The response mirrors the same structure: your data sits under `data.bdr`.

The `bdr` block sets one rule for mutations: a single block may select only one mutation field. To run several mutations in one request, alias the `bdr` field itself, one block per call. See [Batch mutations](graphql-tips-and-patterns.md#batch-mutations). Queries have no such rule: one `bdr` block can select as many query fields as you need.

Two lookup queries stay at the top level, outside any product namespace: `node` and `nodes`. They return a record of any product by its ID. See [Identifiers](./#identifiers).

## Identifiers

Every record of the API has an `id` of the type `ID`, which is a string. All products use one ID format, so an ID never repeats across products. Because IDs never repeat, the top-level `node` query can return a record of any product:

```graphql
query {
  node(id: "019a6b2f-793e-807b-8001-444556430011") {
    id
    ... on Device { title }
  }
}
```

The `... on Device { }` block is an inline fragment: it selects `title` only when the record is a device. The response has the device, or `null` when the API can't return it:

```json
{
  "data": {
    "node": {
      "id": "019a6b2f-793e-807b-8001-444556430011",
      "title": "Truck 42 tracker"
    }
  }
}
```

`node` returns `null` for an ID that doesn't exist, that belongs to another workspace, or that names a record your role can't read. `nodes(ids: [...])` does the same for a list: it returns `null` in place of each ID it can't return. Lookups inside a product, such as `bdr { device(id: ...) }`, also return `null` for these IDs. The one difference: when your roles allow no `read` at all on the kind of record, the lookup inside a product returns a `PERMISSION_DENIED` error instead.

Some IDs work differently with `node` and with ID arguments:

* `node` returns a Console organization only when it's one of your own organizations: the organizations that `console { accessibleOrganizations }` lists. An organization ID from another Navixy region or deployment returns a `VALIDATION_ERROR`.
* The IDs of Console workspace operations and of tracking commands don't identify records of this API. `node` returns `null` for them.
* An argument that expects the ID of one kind of record returns a `VALIDATION_ERROR` for the ID of another kind. For example, `device(id:)` returns this error for an asset ID. An organization, workspace, operation or role ID issued in another Navixy region or deployment returns the same error.

Treat an ID as a string that you store and send back unchanged. Don't build IDs, and don't read meaning from their characters.

A few fields have the type `UUID` instead of `ID`. Their values don't identify records of this API. Examples are `actorId`, the ID of the user or integration that made a change, and `idempotencyKey`, a key that you choose yourself. `node` can't return a record for these values.

## Operations

GraphQL supports three types of operations:

| Operation    | Purpose                        | Similar to REST    |
| ------------ | ------------------------------ | ------------------ |
| Query        | Read data                      | GET                |
| Mutation     | Create, update, or delete data | POST, PUT, DELETE  |
| Subscription | Receive real-time updates      | No REST equivalent |

### Queries

A **query** retrieves data without changing anything. It's the GraphQL equivalent of a REST GET request.

Here's a simple query that fetches a device:

```graphql
query {
  bdr {
    device(id: "550e8400-e29b-41d4-a716-446655440001") {
      id
      title
    }
  }
}
```

Let's break this down:

* `query` tells GraphQL you want to read data
* `device` is the name of the query (what you're fetching)
* `(id: "550e8400-...")` is an argument that specifies which device
* `{ id title }` lists the fields you want returned

The response mirrors your query structure:

```json
{
  "data": {
    "bdr": {
      "device": {
        "id": "550e8400-e29b-41d4-a716-446655440001",
        "title": "Truck 42"
      }
    }
  }
}
```

Notice that the response contains only `id` and `title`, exactly what you requested. If you also need the device's status, add it to your query:

```graphql
query {
  bdr {
    device(id: "550e8400-e29b-41d4-a716-446655440001") {
      id
      title
      status {
        code
        title
      }
    }
  }
}
```

The `status` field returns an object, so you specify which fields of that object you want (`code` and `title`).

### Mutations

A **mutation** modifies data, creating, updating, or deleting something. It combines what REST does with POST, PUT, and DELETE into one operation type.

Here's a mutation that updates a device's title:

```graphql
mutation {
  bdr {
    deviceUpdate(input: {
      id: "550e8400-e29b-41d4-a716-446655440001"
      version: 5
      title: "Truck 42 - Berlin"
    }) {
      device {
        id
        version
        title
      }
    }
  }
}
```

Let's break this down:

* `mutation` tells GraphQL you want to change data
* `deviceUpdate` is the name of the mutation
* `input: { ... }` provides the data for the update, including the current `version` for optimistic locking
* `{ device { ... } }` specifies what to return after the update

The response shows the updated data:

```json
{
  "data": {
    "bdr": {
      "deviceUpdate": {
        "device": {
          "id": "550e8400-e29b-41d4-a716-446655440001",
          "version": 6,
          "title": "Truck 42 - Berlin"
        }
      }
    }
  }
}
```

This is useful because you can confirm the change and get the new version number without making a separate query.

Here's a mutation that creates a new asset:

```graphql
mutation {
  bdr {
    assetCreate(input: {
      typeId: "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11"
      title: "Delivery Van 7"
    }) {
      asset {
        id
        title
      }
    }
  }
}
```

### Subscriptions

A **subscription** keeps a connection open and receives data every time it changes. A query returns one response and ends. A subscription returns a new message on the same connection whenever the data changes. Your app doesn't have to send the same query again and again.

Navixy GraphQL API has one subscription, `trackingAssetStates`. It sends the state of assets every time their devices report.

Here's a subscription that follows the connectivity of every asset in your workspace:

```graphql
subscription {
  trackingAssetStates(input: { readingCodes: [] }) {
    changes {
      assetId
      state {
        primaryDevice {
          connectivity { status }
        }
      }
    }
  }
}
```

Let's break this down:

* `subscription` tells GraphQL that you want to receive updates
* `trackingAssetStates` is the name of the subscription
* `input: { ... }` sets what to follow, here with no readings
* `{ changes { ... } }` lists the fields of every message

Every message has the same shape as a query response. The API first sends the current state of every asset that has a linked device, then a new message whenever devices report:

```json
{
  "data": {
    "trackingAssetStates": {
      "changes": [
        {
          "assetId": "019d48ea-0752-8000-801f-415353540a11",
          "state": {
            "primaryDevice": {
              "connectivity": { "status": "ONLINE" }
            }
          }
        }
      ]
    }
  }
}
```

A subscription isn't sent as a plain POST request that gets one response. The API accepts it over two transports:

* A WebSocket connection with the `graphql-transport-ws` protocol, at `wss://nvx4.eu.navixy.com/api/graphql/ws`. The WebSocket accepts subscriptions only.
* Server-sent events: a POST request to the usual endpoint with the `Accept: text/event-stream` header. The response stays open and delivers each message as an event.

These rules apply to every WebSocket connection. They use the message types of the `graphql-transport-ws` protocol. The client sends `connection_init` to authenticate and `subscribe` to start a subscription. The server sends `next` with data, and `complete` when a subscription ends.

* The client must send the access token in the payload of `connection_init`, as `{"Authorization": "Bearer <ACCESS_TOKEN>"}`, within 10 seconds of opening the connection. Otherwise the server closes the connection with code 4408. The server closes the connection with code 4401 when `connection_init` has no valid token.
* The server sends a `ping` every 15 seconds and closes a connection whose client has sent nothing for 45 seconds, with code 1001. Client libraries such as `graphql-ws` reply to every `ping` with a `pong` message automatically.
* A query or a mutation sent over the WebSocket gets one `next` message with a `VALIDATION_ERROR` error, then `complete`. A second `subscribe` with an ID that is already in use closes the connection with code 4409.
* One caller can rely on keeping up to 12 connections open; each API server counts them on its own. The server closes a connection over that number with code 4403 after its `connection_init`. `graphql-ws` then reconnects after a delay that grows with each attempt.
* Every subscription that you send counts as one request toward your [request rate](../limits.md#request-rate), even one that the server then refuses. A subscription over the limit gets one `next` message with a `RATE_LIMITED` error, then `complete`, and the connection stays open.
* A subscription ends with an `UNAUTHORIZED` error when its access token expires. Get a new token and subscribe again.

[Subscription limits](../limits.md#subscription-limits) lists every limit with its default value. [Building a live fleet map](../tracking/guides/building-a-live-fleet-map.md#connect-to-the-subscription-endpoint) shows how to connect, authenticate, and handle errors on both transports. [State streams](../tracking/api-reference/state/streams.md) documents every field of the subscription.

## Fields

Fields are the pieces of data you can request from an object. When you write a query, you're selecting which fields to include in the response.

Every object type in the API has a defined set of fields. For example, a `Device` has fields like:

* `id`: the unique identifier
* `title`: the display name
* `status`: the current status (which is itself an object with its own fields)
* `workspace`: the workspace that owns it
* `identifiers`: the hardware identifiers, such as IMEI or serial number

You only get the fields you ask for. If you don't need `identifiers`, don't include it in your query, and it won't be in the response.

### Nested fields

Some fields return objects rather than simple values. When a field returns an object, you must specify which fields of that object you want.

```graphql
query {
  bdr {
    device(id: "550e8400-e29b-41d4-a716-446655440001") {
      title
      status {
        code
        title
      }
      workspace {
        title
        isActive
      }
    }
  }
}
```

Here, `status` and `workspace` are objects, so their fields go inside curly braces. The `title` and `code` fields return simple values (strings), so they don't need nested fields. `isActive` returns a boolean.

## Arguments

Arguments allow you to filter or configure what data a query returns. You pass them in parentheses after the field name.

The `device` query requires an `id` argument to know which device to fetch:

```graphql
query {
  bdr {
    device(id: "550e8400-e29b-41d4-a716-446655440001") {
      title
    }
  }
}
```

The `devices` query (plural) can take optional arguments to filter results:

```graphql
query {
  bdr {
    devices(
      filter: {
        statusIds: ["a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11"]
      }
      first: 10
    ) {
      nodes {
        id
        title
      }
    }
  }
}
```

This requests the first 10 devices in your workspace with a specific status. Every argument here is optional: `filter` narrows the list, and `first` sets the page size.

## Types

Every field in GraphQL has a type that defines what kind of data it holds. It can be predefined, like string or boolean, or API-specific, like `DeviceIdType`. Understanding types helps you read the API reference and write correct queries.

### Scalar types

Scalars are primitive values, the basic building blocks. When calling a GraphQL API, you must specify nested subfields until you return only scalars.

GraphQL has the following built-in scalar types:

| Type      | Description                              | Example                                  |
| --------- | ---------------------------------------- | ---------------------------------------- |
| `String`  | Text                                     | `"Truck 42"`                             |
| `Int`     | Whole number                             | `42`                                     |
| `Float`   | Decimal number                           | `3.14`                                   |
| `Boolean` | True or false                            | `true`                                   |
| `ID`      | Unique identifier (serialized as string) | `"019a6b2f-793e-807b-8001-444556430011"` |

Navixy GraphQL API adds these custom scalar types:

| Type           | Description                                                                                                                                                      | Example                           |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------- |
| `DateTime`     | Date and time with timezone (ISO 8601)                                                                                                                           | `"2025-01-15T14:30:00.000Z"`      |
| `Date`         | Date without time (ISO 8601)                                                                                                                                     | `"2025-01-15"`                    |
| `JSON`         | Arbitrary JSON data                                                                                                                                              | `{"color": "red", "priority": 1}` |
| `Code`         | Machine-readable identifier: letters, digits, `_`, `.`, and `-`, starting with a letter or digit, up to 100 characters. Case is ignored when codes are compared. | `"truck"`, `"device_type"`        |
| `HexColorCode` | Color in hexadecimal format                                                                                                                                      | `"#FF5733"`                       |

### Object types

Object types represent the things you can fetch from the API: devices, workspaces, assets, users, and so on. Each object type defines a set of fields you can request.

Here's what the `Device` type looks like in the schema:

```graphql
type Device {
  id: ID!
  version: Int!
  title: String!
  status: DeviceStatus!
  workspace: Workspace!
  identifiers: [DeviceIdentifier!]!
  # ... more fields
}
```

This tells you:

* `Device` is an object type
* It has fields like `id`, `version`, `title`, `status`, etc.
* Each field has its own type (`ID!`, `Int!`, `String!`, `DeviceStatus!`)

Some fields return scalar types (`id` returns `ID!`), while others return other object types (`status` returns `DeviceStatus!`). When a field returns an object type, you must specify which of its fields you want:

```graphql
query {
  bdr {
    device(id: "...") {
      title              # String — scalar, no nested fields needed
      status {           # DeviceStatus — object type
        code             # String
        title            # String
      }
    }
  }
}
```

In the API reference, you'll see object types like `Device`, `Workspace`, `DeviceStatus`, and many others.

### Enums

Enums are fixed sets of values. Unlike strings, you can only use the predefined values.

For example, `OrderDirection` is an enum with these values:

* `ASC`
* `DESC`

When using an enum in a query, write the value without quotes:

```graphql
query {
  bdr {
    devices(
      orderBy: { field: TITLE, direction: DESC }
    ) {
      nodes { id title }
    }
  }
}
```

### Input types

Input types are used for mutation arguments. They look similar to object types but can only be used as inputs, not outputs.

In the API reference, you'll see input types like `DeviceCreateInput`, `AssetUpdateInput`, and `DeviceFilter`. They define what fields you can (or must) provide:

```graphql
mutation {
  bdr {
    assetCreate(input: {           # AssetCreateInput
      typeId: "..."                # Required
      title: "Delivery Van"        # Required
      customFields: {              # Optional
        set: [{ code: "cf_vin", value: { string: "1HGBH41JXMN109186" } }]
      }
    }) {
      asset {
        id
      }
    }
  }
}
```

### Interfaces

Interfaces define a set of fields shared by multiple types. If a type implements an interface, it guarantees those fields exist.

For example, the `Node` interface requires the `id` field. Many types implement it: `Device`, `Asset`, `Workspace`, and others. This means you can always request `id` from any of these types.

The `Versioned` interface provides the `version` field, so you can always request `version` on types that implement it and include it when you update them. See [Optimistic locking](../optimistic-locking.md) for how the field is used.

## Type syntax

The API reference uses special notation to indicate whether fields are required and whether they return single values or lists.

### Required vs nullable

The `!` symbol means a value is required (non-null):

| Syntax    | Meaning                              |
| --------- | ------------------------------------ |
| `String`  | May be null                          |
| `String!` | Never null: always returns a string |

For arguments, `!` means you must provide a value:

```graphql
# id is required (ID!), you must provide it
device(id: "...")

# filter is optional (DeviceFilter without !), you can omit it
devices(filter: { titleContains: "Truck" })
```

### Arrays

Square brackets `[]` indicate a list of values:

<table><thead><tr><th width="100">Syntax</th><th>Meaning</th></tr></thead><tbody><tr><td><code>[String]</code></td><td>A list of strings (list and items may be null)</td></tr><tr><td><code>[String!]</code></td><td>A list of non-null strings (list may be null)</td></tr><tr><td><code>[String]!</code></td><td>A non-null list of strings (items may be null)</td></tr><tr><td><code>[String!]!</code></td><td>A non-null list of non-null strings</td></tr></tbody></table>

The most common pattern is `[Type!]!`: a guaranteed list where every item exists:

```graphql
# nodes is [Device!]! — always returns a list, every item is a valid device
devices(first: 10) {
  nodes {        # Never null, never contains null items
    title
  }
}
```

### Reading complex types

When you see a type like `[DeviceEdge!]!` in the API reference:

1. Start from the inside: `DeviceEdge`, which is an object type
2. Add `!`: `DeviceEdge!`: each item is never null
3. Add `[]`: `[DeviceEdge!]`: it's a list
4. Add outer `!`: `[DeviceEdge!]!`: the list itself is never null

So `[DeviceEdge!]!` means "you'll always get a list, and every item in that list will be a valid DeviceEdge object."

## How responses work

GraphQL responses are JSON objects with a consistent structure.

### Successful responses

A successful response has a `data` field containing your requested data:

```json
{
  "data": {
    "bdr": {
      "device": {
        "id": "550e8400-e29b-41d4-a716-446655440001",
        "title": "Truck 42"
      }
    }
  }
}
```

The structure inside `data` matches your query structure exactly.

### Error responses

If something goes wrong, the response includes an `errors` array:

```json
{
  "data": { "bdr": { "deviceUpdate": null } },
  "errors": [
    {
      "message": "Device not found: 550e8400-e29b-41d4-a716-446655440001",
      "path": ["bdr", "deviceUpdate"],
      "extensions": {
        "code": "NOT_FOUND",
        "status": 404,
        "detail": "Device not found: 550e8400-e29b-41d4-a716-446655440001",
        "field": "input.id"
      }
    }
  ]
}
```

The `extensions` object contains machine-readable information for handling the error in your code. See [Error handling](../error-handling.md) for more information.

## Schema

A GraphQL API has a **schema** that defines all available types, fields, queries, and mutations. The schema is like a contract: it tells you exactly what you can request and what you'll get back.

The [Navixy GraphQL API schema](../developer-resources/public-graphql-schema.md) is public and available to developers, split into one file for the shared types and one per product.

## Introspection

GraphQL APIs are self-describing. You can explore the schema using GraphQL tools or by querying the API directly. This is called **introspection**. GraphQL has special built-in fields that start with `__` (double underscore) for this purpose.

Introspection needs an access token, like any other query: send it in the `Authorization` header, or set it in your GraphQL tool. Without a token, the API returns `UNAUTHORIZED` and no part of the schema.

For example, to see all fields available on the `Device` type:

```graphql
query {
  __type(name: "Device") {
    name
    fields {
      name
      type { name }
    }
  }
}
```

## See also

* [GraphQL tips and patterns](graphql-tips-and-patterns.md): Write cleaner requests with variables, fragments, aliases, and directives
* [Error handling](../error-handling.md): Understand error structure, codes, and common error scenarios
