---
description: >-
  Request, rate, subscription, pagination, and input size limits enforced by
  Navixy GraphQL API, and the errors the API returns when a limit is exceeded.
tags:
  - reference
  - beta
---

# Limits

Navixy GraphQL API enforces limits on request size, request rate, subscriptions, pagination, and input values. This page lists every limit and the error you get when you exceed it.

The request limits (complexity, depth, and rate) and the subscription limits apply to every product of the API. They are deployment settings: the values on this page are the defaults, so a specific installation can use different ones. Each input limit names the operations it applies to.

## Request limits

| Limit                           | Default value               | Error when exceeded                                              |
| ------------------------------- | --------------------------- | ---------------------------------------------------------------- |
| Query complexity                | 200                         | [`QUERY_TOO_COMPLEX` (400)](error-handling.md#error-codes)       |
| Query depth                     | 15 levels                   | [`QUERY_TOO_DEEP` (400)](error-handling.md#error-codes)          |
| Request rate                    | 100 requests per 60 seconds | [`RATE_LIMITED` (429)](error-handling.md#error-codes)            |
| Mutation fields per `bdr` block | 1                           | [Validation error (400)](error-handling.md#validation-error-400) |

### Query complexity

A query can request at most 200 fields. Every field costs 1 plus the cost of its children, so the complexity of a query is its total field count. The count doesn't depend on page size: a list query that returns 100 items costs the same as one that returns a single item.

When a query goes over the limit, the API rejects it before execution:

```json
{
  "errors": [{
    "message": "Query complexity 240 exceeds the maximum allowed 200",
    "extensions": {
      "type": "https://api.navixy.com/errors/query-too-complex",
      "title": "Query Too Complex",
      "status": 400,
      "code": "QUERY_TOO_COMPLEX"
    }
  }]
}
```

#### How to handle query complexity errors

Request fewer fields, or split the query into several smaller requests. [Fragments](graphql-basics/graphql-tips-and-patterns.md#fragments) don't reduce complexity, because each use of a fragment counts its fields again.

### Query depth

Selections can nest at most 15 levels deep. The count includes the `bdr` level, so your own selections inside `bdr` can nest 14 levels. Deeply nested queries usually walk entity relationships back and forth (asset → device → asset → device), so a depth error is often a sign to split the query instead of nesting further.

### Request rate

Each caller can send 100 requests per 60-second period. The caller is the user or API key that the access token names, so all tokens of one user count toward the same limit. Requests without a token count per IP address, so all callers without a token that use the same IP address share one limit. Each API server keeps its own count, and more than one server answers requests, so the number is what you can always rely on, not a ceiling: a caller sometimes gets more requests through in a period.

The limit counts more than HTTP requests:

* Every request to the API endpoint counts toward the limit of its token, including a subscription over server-sent events.
* Every subscription that you send over a WebSocket counts toward the limit of the connection's token, even one that the server then refuses.
* Every WebSocket handshake counts toward the limit of your IP address. The handshake has no token: the token arrives later, in `connection_init`.

Going over the limit on an HTTP request returns a [`RATE_LIMITED` (429)](error-handling.md#error-codes) error with a `Retry-After: 60` response header. The header gives the length of the period, not the time left in it. Wait for the number of seconds in the header, then retry. A WebSocket handshake over the limit gets HTTP 429 in the same way. A subscription over the limit gets one `next` message with a `RATE_LIMITED` error, then `complete`. The connection stays open, so send the subscription again after a delay that grows with each attempt.

### One mutation per bdr block

A single `bdr` block in a mutation may select only one mutation field. To run several mutations in one request, alias the `bdr` field itself, one block per call. See [Batch mutations](graphql-basics/graphql-tips-and-patterns.md#batch-mutations) for the pattern and its partial-success behavior.

## Subscription limits

A subscription keeps a connection open, so it has limits of its own. The transports are described in [Subscriptions](graphql-basics/#subscriptions). The connection and subscription counts are kept on each API server, like the request rate, so the values are what you can always rely on.

| Limit | Default value | What happens when it's exceeded |
| --- | --- | --- |
| Open WebSocket connections per caller | 12 | The server closes the new connection with code 4403 after its `connection_init` |
| Subscriptions per WebSocket connection | 10 | The 11th subscription gets one `next` message with a [validation error](error-handling.md#validation-error-400), then `complete` |
| Open subscriptions to `trackingAssetStates` per caller, over the WebSocket and server-sent events together | 12 | The new subscription ends with a [`RATE_LIMITED`](error-handling.md#error-codes) error |
| Assets that one `trackingAssetStates` subscription follows | 1,000 | The subscription ends with a validation error |
| Time to send `connection_init` after the handshake | 10 seconds | The server closes the connection with code 4408 |
| Time without any message from the client | 45 seconds | The server closes the connection with code 1001 |

The server sends a `ping` every 15 seconds. Client libraries reply to it with a `pong` message automatically, so a connection whose client reads its messages stays open. A closed connection no longer counts toward the limit of 12. A connection that dropped without a close message counts until the server closes it, about 45 seconds after the client's last message.

## Pagination limits

The `first` and `last` arguments default to 20 items and accept at most 100. The Console and IAM lists default to 50 items, also with at most 100. Requesting more than 100 items doesn't cap the page: it returns a [validation error](error-handling.md#validation-error-400). See [Pagination](pagination.md) for how to page through larger result sets with cursors.

## Input value limits

Exceeding any of the limits below returns a [validation error](error-handling.md#validation-error-400) that names the failing field.

### Count limits

| Input                                | Limit                                       |
| ------------------------------------ | ------------------------------------------- |
| Custom field definitions per entity type | 50 active, 100 including archived        |

### Length limits

| Input                                                                                                     | Limit                                                                 |
| --------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| `title` (all entities)                                                                                    | 255 characters                                                        |
| [Code](core-api-reference/common.md#code) values (type codes, custom field codes, identifier `namespace`) | 100 characters                                                        |
| `externalId`                                                                                              | 100 characters                                                        |
| Device identifier `value`                                                                                 | 64 characters                                                         |
| Email addresses                                                                                           | 254 characters                                                        |
| Name fields on users (`givenNames`, `familyNames`)                                                        | 255 characters                                                        |
| `STRING` custom field values                                                                              | 255 characters, restricted further by the definition's `maxLength`    |
| `TEXT` custom field values                                                                                | 65,535 characters, restricted further by the definition's `maxLength` |

### Value ranges and formats

| Input                         | Rule                                                                          |
| ----------------------------- | ----------------------------------------------------------------------------- |
| `DECIMAL` custom field values | Up to 28 significant digits: at most 18 before and 10 after the decimal point |
| `INTEGER` custom field values | Signed 64-bit integer range                                                   |
| `HexColorCode`                | `#RGB` or `#RRGGBB`                                                           |
| `CountryCode`                 | Exactly 2 uppercase letters                                                   |
| Latitude and longitude        | −90 to 90 and −180 to 180                                                     |

### Batch size limits

| Input | Limit |
| --- | --- |
| `assetIds` in `assetGroupCreate`, `assetGroupUpdate`, `assetGroupItemsAdd`, and `assetGroupItemsRemove` | 2,000 IDs per call |
| `filter.deviceIds` in the IoT Logic `deviceFlowLinks` query | 1,000 IDs |
| `deviceIds` in the IoT Logic `flowDebugValues` query | 1 to 10 IDs |

## What has no fixed limit

The API sets no cap on the following. Only the query complexity limit applies to them, so keep them as small as your task allows:

* The number of identifiers per device
* The number of entries in `customFields.set` and `unset`
* The number of conditions in the `customFields` filter
* The number of IDs in one `nodes(ids: [...])` lookup
* The size of a GeoJSON geometry
* The number of events in one `scheduleData` value

There is also no random page access: pagination is cursor-only, with no offset argument. See [Pagination](pagination.md#best-practices) for the reasoning.

## See also

* [Error handling](error-handling.md): Understand error structure, codes, and common error scenarios
* [Pagination](pagination.md): Page through large result sets with cursors
* [GraphQL tips and patterns](graphql-basics/graphql-tips-and-patterns.md): Write cleaner requests with variables, fragments, aliases, and directives
