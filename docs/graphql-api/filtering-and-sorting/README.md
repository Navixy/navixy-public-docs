---
description: >-
  Use filter and orderBy on list queries to control which results come back and
  in what order, including text search and sort rules that affect pagination
  cursors.
tags:
  - fundamentals
  - beta
---

# Filtering and sorting

List queries (those returning multiple items, like `devices`, `assets`, or `schedules`) accept `filter` and `orderBy` arguments that let you control which results come back and in what order. Instead of fetching everything and processing it client-side, you can request exactly what you need.

For navigating through large result sets, see [Pagination](../pagination.md).

## Filtering

Pass a `filter` argument to any list query to narrow down results:

```graphql
query {
  bdr {
    devices(
      filter: {
        statusIds: ["status-active-uuid"]
      }
    ) {
      nodes {
        id
        title
        status { title }
      }
    }
  }
}
```

This returns only devices with the specified status.

Each entity type has its own filter input with different available fields. For example, `DeviceFilter` supports filtering by type, model, status, vendor, and inventory, while `ScheduleFilter` only supports a partial match on the title. Use [introspection](../graphql-basics/#introspection) or the entity's reference page to see available filter fields.

### Filtering logic

Filters combine conditions using two types of logic:

| Location       | Logic | Meaning                 |
| -------------- | ----- | ----------------------- |
| Within a field | OR    | Match any of the values |
| Between fields | AND   | Match all conditions    |

Consider this filter:

```graphql
filter: {
  typeIds: ["type-truck-uuid", "type-van-uuid"]
  statusIds: ["status-active-uuid"]
}
```

This translates to:

```
(type = truck OR type = van) AND (status = active)
```

The result includes active trucks and active vans, but not inactive trucks or vehicles of other types.

### Empty values are ignored

If a filter field is `null` or an empty array, the API ignores it entirely:

```graphql
filter: {
  typeIds: []                        # Ignored — no filtering by type
  statusIds: ["status-active-uuid"]  # Applied
}
```

This is useful when building dynamic filters, so you don't need to conditionally omit empty fields from your query.

### Text search

Most filters include a `titleContains` field for partial text matching:

```graphql
query {
  bdr {
    devices(
      filter: {
        titleContains: "delivery"
      }
    ) {
      nodes {
        id
        title
      }
    }
  }
}
```

This returns devices with "delivery" anywhere in their title, like "Delivery Van 1" or "North Delivery Truck". The search is case-insensitive.

Some filters have additional text search fields. For example, `DeviceFilter` includes `identifierContains` for searching hardware identifiers like IMEI numbers. Like `titleContains`, identifier search is case-insensitive.

### Limitations

The API does not support complex boolean expressions with nested AND/OR/NOT operators. If you need more complex filtering logic, apply the most restrictive server-side filter you can, then filter the results further in your application.

For filtering assets and geo objects by their custom field values, see [Custom field filtering and sorting](custom-field-filtering.md).

## Sorting

Use the `orderBy` argument to control the order of results:

```graphql
query {
  bdr {
    devices(
      orderBy: { field: TITLE, direction: ASC }
    ) {
      nodes {
        id
        title
      }
    }
  }
}
```

The `direction` can be ASC (ascending: A→Z, 0→9, oldest→newest) or DESC (descending: Z→A, 9→0, newest→oldest).

Each entity type has its own set of sortable fields. For example, devices can be sorted by TITLE, while audit events can be sorted by OCCURRED\_AT. Use introspection or the API reference to see available sort fields.

### Sort behavior

Text fields use natural sorting with ICU collation:

* Case-insensitive: "apple" and "Apple" sort together
* Locale-aware: accented characters sort correctly (é sorts with e, not after z)
* Numeric-aware: "item2" comes before "item10"

NULL values appear last when sorting ASC, and first when sorting DESC.

For sorting assets and geo objects by custom field values, see [Custom field filtering and sorting](custom-field-filtering.md).

### Sorting and pagination

A cursor encodes a position in one sort order. If you change `orderBy` between pagination requests, the API returns no error, but the next page is cut at a position that means nothing in the new order, so items are skipped or repeated. Always use the same `orderBy` value when paginating through a result set. If you need a different sort order, start from the beginning.

## See also

* [Pagination](../pagination.md): Page through large result sets with cursors
* [Custom field filtering and sorting](custom-field-filtering.md): Filter and sort assets and geo objects by custom field values
