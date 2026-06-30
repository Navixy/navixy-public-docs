---
description: >-
  Filter and sort assets, geo objects, and schedules by their custom field
  values in Navixy Repository API.
---

# Custom field filtering and sorting

Entities that support custom fields — assets, geo objects, and schedules — can be filtered and sorted by their custom field values. This is in addition to the standard `filter` and `orderBy` arguments described in [Filtering and sorting](filtering-and-sorting.md).

For an overview of custom fields themselves, see [Custom fields](custom-fields.md).

## Filtering by custom fields

Pass a `customFields` array inside the `filter` argument:

```graphql
query {
  assets(
    organizationId: "019d48ea-0752-8000-801f-444556437ab1"
    filter: {
      customFields: [
        { code: "fuel_type", operator: EQ, value: { string: "diesel" } }
      ]
    }
  ) {
    nodes {
      id
      title
    }
  }
}
```

Each condition in the `customFields` array has three parts:

<table><thead><tr><th width="200.39996337890625">Field</th><th>Description</th></tr></thead><tbody><tr><td>code</td><td>The custom field's code, as defined in its CustomFieldDefinition</td></tr><tr><td>operator</td><td>How to compare the value</td></tr><tr><td>value</td><td>The value to compare against, using a typed variant matching the <a href="#value-formats">field's type</a></td></tr></tbody></table>

### Operators

| Operator      | Description                             |
| ------------- | --------------------------------------- |
| EQ            | Equals                                  |
| NE            | Not equals                              |
| GT            | Greater than                            |
| GTE           | Greater than or equal                   |
| LT            | Less than                               |
| LTE           | Less than or equal                      |
| CONTAINS      | String contains (case-insensitive)      |
| IN            | Matches any value in the provided array |
| IS\_NULL      | Field has no value                      |
| IS\_NOT\_NULL | Field has a value                       |

### Value formats

The `value` field uses a typed `@oneOf` input. Provide exactly one variant matching your field's type:

| Variant      | Field types                            | Example                                    |
| ------------ | -------------------------------------- | ------------------------------------------ |
| `string`     | STRING, TEXT, OPTIONS, CATALOG, TAG    | `{ string: "diesel" }`                     |
| `decimal`    | DECIMAL                                | `{ decimal: 42.50 }`                       |
| `integer`    | INTEGER                                | `{ integer: 42 }`                          |
| `boolean`    | BOOLEAN                                | `{ boolean: true }`                        |
| `date`       | DATE                                   | `{ date: "2024-01-15" }`                   |
| `datetime`   | DATETIME                               | `{ datetime: "2024-01-15T10:30:00Z" }`     |
| `id`         | DEVICE, REFERENCE                      | `{ id: "019a6a3f-..." }`                   |
| `stringList` | IN operator on string-based fields     | `{ stringList: ["option_a", "option_b"] }` |
| `idList`     | IN operator on DEVICE/REFERENCE fields | `{ idList: ["uuid1", "uuid2"] }`           |

For `IS_NULL` and `IS_NOT_NULL` operators, omit `value` or set it to `null`.

### Multiple conditions

Multiple custom field conditions are combined with AND logic:

```graphql
filter: {
  customFields: [
    { code: "fuel_type", operator: EQ, value: { string: "diesel" } },
    { code: "year", operator: GTE, value: { integer: 2020 } }
  ]
}
```

This returns assets where fuel type is diesel AND year is 2020 or later.

### Multi-value fields

For custom fields configured to hold multiple values (`isMulti: true`), the filter matches if any stored value satisfies the condition:

```graphql
# Asset has colors: ["red", "blue", "white"]
filter: {
  customFields: [
    { code: "colors", operator: EQ, value: { string: "red" } }
  ]
}
# Matches — "red" is one of the values
```

### Combining standard and custom filters

You can use standard filter fields and custom field conditions together:

```graphql
query {
  assets(
    organizationId: "019d48ea-0752-8000-801f-444556437ab1"
    filter: {
      typeIds: ["type-truck-uuid"]
      titleContains: "north"
      customFields: [
        { code: "region", operator: EQ, value: { string: "northwest" } }
      ]
    }
  ) {
    nodes {
      id
      title
    }
  }
}
```

This returns trucks with "north" in their title that are assigned to the northwest region.

## Sorting by custom fields

Assets and geo objects support sorting by custom field values. Only the following field types are sortable: `STRING`, `TEXT`, `DECIMAL`, `INTEGER`, `DATE`, `DATETIME`.

```graphql
query {
  assets(
    organizationId: "019d48ea-0752-8000-801f-444556437ab1"
    orderBy: { customFieldCode: "priority", direction: DESC }
  ) {
    nodes {
      id
      title
    }
  }
}
```

Use either `field` or `customFieldCode` in your `orderBy` input, not both — they are mutually exclusive.
