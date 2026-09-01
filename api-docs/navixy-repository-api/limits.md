---
description: >-
  Request, pagination, and input size limits enforced by Navixy Repository API,
  and the errors the API returns when a limit is exceeded.
---

# Limits

Navixy Repository API enforces limits on request size, request rate, pagination, and input values. This page lists every limit and the error you get when you exceed it. All limits apply per request unless stated otherwise.

The request limits (complexity, depth, and rate) are deployment settings. The values on this page are the defaults, so a specific installation can use different ones.

## Request limits

| Limit | Default value | Error when exceeded |
| --- | --- | --- |
| Query complexity | 200 | [`QUERY_TOO_COMPLEX` (400)](error-handling.md#error-codes) |
| Query depth | 15 levels | [`QUERY_TOO_DEEP` (400)](error-handling.md#error-codes) |
| Request rate | 100 requests per 60 seconds | [`RATE_LIMITED` (429)](error-handling.md#error-codes) |
| Mutation fields per `bdr` block | 1 | [Validation error (400)](error-handling.md#validation-error-400) |

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

Each authenticated caller can send 100 requests per 60-second window. Going over the limit returns a [`RATE_LIMITED` (429)](error-handling.md#error-codes) error with a `Retry-After: 60` response header. Wait for the number of seconds in the header, then retry.

### One mutation per bdr block

A single `bdr` block in a mutation may select only one mutation field. To run several mutations in one request, alias the `bdr` field itself, one block per call. See [Batch mutations](graphql-basics/graphql-tips-and-patterns.md#batch-mutations) for the pattern and its partial-success behavior.

## Pagination limits

The `first` and `last` arguments default to 20 items and accept at most 100. Requesting more than 100 items doesn't cap the page: it returns a [validation error](error-handling.md#validation-error-400). See [Pagination](pagination.md) for how to page through larger result sets with cursors.

## Input value limits

Exceeding any of the limits below returns a [validation error](error-handling.md#validation-error-400) that names the failing field.

### Length limits

| Input | Limit |
| --- | --- |
| `title` (all entities) | 255 characters |
| [Code](common.md#code) values (type codes, custom field codes, identifier `namespace`) | 100 characters |
| `externalId` | 100 characters |
| Email addresses | 254 characters |
| Name fields on users (`givenNames`, `familyNames`) | 255 characters |
| `STRING` custom field values | 255 characters, restricted further by the definition's `maxLength` |
| `TEXT` custom field values | 65,535 characters, restricted further by the definition's `maxLength` |


### Value ranges and formats

| Input | Rule |
| --- | --- |
| `DECIMAL` custom field values | Up to 28 significant digits: at most 18 before and 10 after the decimal point |
| `INTEGER` custom field values | Signed 64-bit integer range |
| `HexColorCode` | `#RGB` or `#RRGGBB` |
| `CountryCode` | Exactly 2 uppercase letters |
| Latitude and longitude | −90 to 90 and −180 to 180 |

### Batch size limits

The `assetIds` list in asset group mutations (`assetGroupItemsAdd`, `assetGroupItemsRemove`, and the `assetIds` replacement in `assetGroupUpdate`) accepts at most 2,000 items per call.

## What has no fixed limit

The API sets no cap on the following, so the practical bound is the query complexity limit and your own restraint:

- The number of identifiers per device
- The number of custom field definitions per type
- The number of entries in `customFields.set` and `unset`
- The number of conditions in the `customFields` filter
- The number of IDs in one `nodes(ids: [...])` lookup
- The size of a GeoJSON geometry
- The number of events in one `scheduleData` value

There is also no random page access: pagination is cursor-only, with no offset argument. See [Pagination](pagination.md#best-practices) for the reasoning.

## See also

* [Error handling](error-handling.md): Understand error structure, codes, and common error scenarios
* [Pagination](pagination.md): Page through large result sets with cursors
* [GraphQL tips and patterns](graphql-basics/graphql-tips-and-patterns.md): Write cleaner requests with variables, fragments, aliases, and directives
