---
description: >-
  GraphQL directives supported by Navixy GraphQL API: the standard
  directives and the custom @trim, @dataClass, and @effect.
tags:
  - reference
  - beta
---

# Directives

Directives modify the behavior of fields, types, or operations in a GraphQL schema. They are prefixed with `@` and can be applied at various locations.

## Standard directives

These directives are part of the GraphQL specification and available in all queries.

### @skip

Conditionally excludes a field or fragment from the response.

| Argument | Type       | Description                                           |
| -------- | ---------- | ----------------------------------------------------- |
| `if`     | `Boolean!` | When `true`, the field is excluded from the response. |

**Locations:** `FIELD`, `FRAGMENT_SPREAD`, `INLINE_FRAGMENT`

**Example:**

```graphql
query GetDevice($skipIdentifiers: Boolean!) {
  bdr {
    device(id: "123") {
      title
      identifiers @skip(if: $skipIdentifiers) {
        type
        value
      }
    }
  }
}
```

### @include

Conditionally includes a field or fragment in the response.

| Argument | Type       | Description                                         |
| -------- | ---------- | --------------------------------------------------- |
| `if`     | `Boolean!` | When `true`, the field is included in the response. |

**Locations:** `FIELD`, `FRAGMENT_SPREAD`, `INLINE_FRAGMENT`

**Example:**

```graphql
query GetDevice($includeRelations: Boolean!) {
  bdr {
    device(id: "123") {
      title
      relationsFrom @include(if: $includeRelations) {
        id
        type { code }
      }
    }
  }
}
```

### @deprecated

Marks a field or enum value as deprecated, signaling that it should no longer be used.

| Argument | Type     | Description                                                 |
| -------- | -------- | ----------------------------------------------------------- |
| `reason` | `String` | Explanation of why it's deprecated and what to use instead. |

**Locations:** `FIELD_DEFINITION`, `ARGUMENT_DEFINITION`, `INPUT_FIELD_DEFINITION`, `ENUM_VALUE`

**Example:**

```graphql
type Device {
  legacyId: String @deprecated(reason: "Use `id` instead.")
  id: ID!
}
```

### @specifiedBy

Provides a URL to a specification that defines the behavior of a custom scalar.

| Argument | Type      | Description                               |
| -------- | --------- | ----------------------------------------- |
| `url`    | `String!` | URL to the scalar specification document. |

**Locations:** `SCALAR`

**Example:**

```graphql
scalar DateTime @specifiedBy(url: "https://scalars.graphql.org/chillicream/date-time.html")
```

### @oneOf

Indicates that an input object requires exactly one of its fields to be provided. This is useful for creating union-like input types.

**Locations:** `INPUT_OBJECT`

**Example:**

```graphql
input FieldParamsInput @oneOf {
  string: StringFieldParamsInput
  text: TextFieldParamsInput
  decimal: DecimalFieldParamsInput
  integer: IntegerFieldParamsInput
}
```

## Custom directives

These directives are specific to the Navixy GraphQL API.

### @trim

Automatically trims leading and trailing whitespace from string input values.

**Locations:** `INPUT_FIELD_DEFINITION`, `ARGUMENT_DEFINITION`

### @dataClass

Labels the class of data that an object type, a field, an argument, or an input field holds. A field without a label takes the class of the object type that declares it. An argument or an input field without a label holds ordinary data.

**Locations:** `OBJECT`, `FIELD_DEFINITION`, `INPUT_FIELD_DEFINITION`, `ARGUMENT_DEFINITION`

**Arguments:**

| Argument | Type | Description |
| --- | --- | --- |
| `class` | `DataClass!` | `ORDINARY` for data about the fleet and the workspace, such as a device's identifiers. `PERSONAL` for data about a person or data that can be tied to one, such as positions, custom field values, a phone number, and where and how a request was made. |

```graphql
phone: String @dataClass(class: PERSONAL)
```

### @effect

Declares what a mutation does beyond the data it writes. Every mutation in every namespace carries it.

**Locations:** `FIELD_DEFINITION`

**Arguments:**

| Argument | Type | Description |
| --- | --- | --- |
| `kinds` | `[EffectKind!]!` | `NONE` alone, or one or more of the other kinds. |

The kinds are these:

| Kind | Effect |
| --- | --- |
| `NONE` | No effect beyond the workspace's own data, and no data deleted. A change of links between records that the reverse call restores counts as `NONE`. It always stands alone in the list. |
| `DEVICES` | An effect on devices, such as a command sent to a device or an IoT Logic flow that sends commands. |
| `PEOPLE` | An effect on people, such as a message like an activation SMS, or a change to roles and role assignments. |
| `MONEY` | An effect on money, such as a subscription, a plan, a payment, or an invoice. |
| `EXTERNAL` | An effect on a system outside Navixy, such as a webhook call. |
| `DISCLOSURE` | Data leaving the workspace, such as a location-sharing link or a webhook that sends device messages to an address outside Navixy. |
| `DATA_LOSS` | Data deleted, by a delete or by an update that deletes data, such as a flow update that removes the nodes it omits. |

`DATA_LOSS` makes a mutation irreversible. Any other kind except `NONE` makes it consequential. Only `NONE` makes it reversible.

```graphql
deviceDelete(input: DeviceDeleteInput!): DeletePayload @effect(kinds: [DATA_LOSS])
```
