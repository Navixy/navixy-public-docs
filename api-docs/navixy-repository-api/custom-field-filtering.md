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

<table><thead><tr><th width="200.39996337890625">Field</th><th>Description</th></tr></thead><tbody><tr><td>code</td><td>The custom field's code, as defined in its CustomFieldDefinition</td></tr><tr><td>operator</td><td>How to compare the value</td></tr><tr><td>value</td><td>The value to compare against, using a typed variant matching the <a href="custom-field-filtering.md#value-formats">field's type</a></td></tr></tbody></table>

### Operators

<table data-search="false"><thead><tr><th>Operator</th><th>Description</th></tr></thead><tbody><tr><td>EQ</td><td>Equals</td></tr><tr><td>NE</td><td>Not equals</td></tr><tr><td>GT</td><td>Greater than</td></tr><tr><td>GTE</td><td>Greater than or equal</td></tr><tr><td>LT</td><td>Less than</td></tr><tr><td>LTE</td><td>Less than or equal</td></tr><tr><td>CONTAINS</td><td>String contains (case-insensitive)</td></tr><tr><td>IN</td><td>Matches any value in the provided array</td></tr><tr><td>IS_NULL</td><td>Field has no value</td></tr><tr><td>IS_NOT_NULL</td><td>Field has a value</td></tr></tbody></table>

### Value formats

The `value` field uses a typed `@oneOf` input. Provide exactly one variant matching your field's type:

<table data-search="false"><thead><tr><th>Variant</th><th>Field types</th><th>Example</th></tr></thead><tbody><tr><td><code>string</code></td><td>STRING, TEXT, OPTIONS, CATALOG, TAG</td><td><code>{ string: "diesel" }</code></td></tr><tr><td><code>decimal</code></td><td>DECIMAL</td><td><code>{ decimal: 42.50 }</code></td></tr><tr><td><code>integer</code></td><td>INTEGER</td><td><code>{ integer: 42 }</code></td></tr><tr><td><code>boolean</code></td><td>BOOLEAN</td><td><code>{ boolean: true }</code></td></tr><tr><td><code>date</code></td><td>DATE</td><td><code>{ date: "2024-01-15" }</code></td></tr><tr><td><code>datetime</code></td><td>DATETIME</td><td><code>{ datetime: "2024-01-15T10:30:00Z" }</code></td></tr><tr><td><code>id</code></td><td>DEVICE, REFERENCE</td><td><code>{ id: "019a6a3f-..." }</code></td></tr><tr><td><code>stringList</code></td><td>IN operator on string-based fields</td><td><code>{ stringList: ["option_a", "option_b"] }</code></td></tr><tr><td><code>idList</code></td><td>IN operator on DEVICE/REFERENCE fields</td><td><code>{ idList: ["uuid1", "uuid2"] }</code></td></tr></tbody></table>

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
