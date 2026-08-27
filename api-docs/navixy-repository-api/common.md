---
description: >-
  Foundational types, scalars, and interfaces shared across the API, including
  Node, Titled, Versioned, pagination types, and common scalars like DateTime
  and Decimal.
---

# Common resources

{% include ".gitbook/includes/navixy-repository-api-is-a-....md" %}

Foundational types, scalars, and interfaces used throughout the API.

## Queries

### node (query)

Retrieves any entity by its globally unique identifier.

```graphql
node(id: ID!): Node
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The ID of the entity to retrieve. |

**Output types:**

<details>

<summary>Node</summary>

An object with a globally unique identifier.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |

</details>

---

### nodes

Retrieves multiple entities by their globally unique identifiers. Returns items in the same order as the input IDs.

```graphql
nodes(ids: [ID!]!): [Node]!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `ids` | `[ID!]!` | The IDs of the entities to retrieve. |

**Output types:**

<details>

<summary>Node</summary>

An object with a globally unique identifier.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |

</details>

---

## Objects

<a id="pageinfo"></a>

### PageInfo

Information about the current page in a paginated connection.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `hasNextPage` | `Boolean!` | Whether more items exist after the current page. |
| `hasPreviousPage` | `Boolean!` | Whether more items exist before the current page. |
| `startCursor` | `String` | The cursor pointing to the first item in the current page. |
| `endCursor` | `String` | The cursor pointing to the last item in the current page. |

---

<a id="countinfo"></a>

### CountInfo

Information about the total count of items in a connection.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `count` | `Int!` | The count of items matching the filter. |
| `precision` | [CountPrecision](#countprecision)! | The precision level of the count value. |

---

<a id="money"></a>

### Money

A monetary amount in minor units (cents) with currency.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `amount` | [Long](#long)! | Amount in minor currency units (for example, 1050 = $10.50). |
| `currency` | [Currency](#currency)! | [ISO 4217](https://www.iso.org/standard/4217.html) currency. |

---

<a id="deletepayload"></a>

### DeletePayload

The result of a delete mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deletedId` | `ID!` | The ID of the deleted entity. |

---

## Enums

<a id="orderdirection"></a>

### OrderDirection

The direction for sorting query results.

| Value | Description |
| ----- | ----------- |
| `ASC` | Sort in ascending order (A→Z, 0→9, oldest→newest). NULL values appear last. |
| `DESC` | Sort in descending order (Z→A, 9→0, newest→oldest). NULL values appear first. |

---

<a id="countprecision"></a>

### CountPrecision

The precision level of a total count value.

| Value | Description |
| ----- | ----------- |
| `EXACT` | The count is exact, calculated using `COUNT(*)`. |
| `APPROXIMATE` | The count is approximate, derived from table statistics. |
| `AT_LEAST` | At least this many items exist. Counting stopped early for performance reasons. |

---

## Interfaces

<a id="node"></a>

### Node

An object with a globally unique identifier.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |

---

<a id="versioned"></a>

### Versioned

An object that supports [optimistic locking](optimistic-locking.md) for concurrency control.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |

---

<a id="edge"></a>

### Edge

An edge in a paginated connection.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge, used for pagination. |

---

<a id="connection"></a>

### Connection

A paginated connection following the Relay Cursor Connections specification.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `pageInfo` | [PageInfo](#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](#countinfo) | The total count of items matching the filter. |

---

<a id="titled"></a>

### Titled

An object with a human-readable display name.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `title` | `String!` | The human-readable display name. |

---

<a id="customizable"></a>

### Customizable

An object that supports custom field values.

Implies `Node`: values are stored on the holder and indexed by its id, so every holder is an
addressable entity. Selecting `id` on a `Customizable` fragment therefore needs no concrete type.

**Implements:** [Node](#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `customFields` | [[CustomFieldValue](custom-fields.md#customfieldvalue)!]! | Typed custom field values, one entry per set field code. Each element is a concrete `CustomFieldValue` implementation matching the field's `FieldType` — select fields via inline fragments. The system-reserved code `geojson_data` is excluded — it is exposed through `GeoObject.geojsonData` instead. |

---

<a id="multivalue"></a>

### MultiValue

An interface for field parameters that support selecting multiple values.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isMulti` | `Boolean!` | Whether multiple values can be selected for this field. |

---

## Scalars

<a id="datetime"></a>

### DateTime

An [ISO 8601](https://www.iso.org/standard/8601.html) datetime string with timezone ([RFC 3339](https://www.rfc-editor.org/rfc/rfc3339.html)). Example: `2024-01-15T10:30:00Z`.

| Property | Value |
| -------- | ----- |
| Format | `YYYY-MM-DDTHH:mm:ss.sssZ` |
| Example | `2025-01-15T14:30:00.000Z` |
| Specification | [https://scalars.graphql.org/chillicream/date-time.html](https://scalars.graphql.org/chillicream/date-time.html) |

---

<a id="json"></a>

### JSON

An arbitrary JSON value. Can be an object, array, string, number, boolean, or null.

| Property | Value |
| -------- | ----- |
| Format | `Any valid JSON` |
| Example | `{"key": "value", "count": 42}` |
| Specification | [https://www.rfc-editor.org/rfc/rfc8259](https://www.rfc-editor.org/rfc/rfc8259) |

---

<a id="code"></a>

### Code

A machine-readable identifier code.

Constraints:
- Allowed characters: ASCII letters (a-z, A-Z), digits (0-9), underscore (_), dot (.), hyphen (-)
- Must start with a letter or digit
- Case-preserving: the original casing is stored as provided
- Case-insensitive for uniqueness checks and comparisons
- Maximum length: 64 characters

Uniqueness:
- For catalog items: unique within the same catalog and workspace scope
- For custom field definitions: unique per owner catalog item and workspace
- For field options (OPTIONS type): unique within a single field definition
- Additional uniqueness requirements may apply depending on context (see individual fields)

Examples: DEVICE_TYPE, vehicle_car, status.active, sensor-v2, ABC123

| Property | Value |
| -------- | ----- |
| Format | `UPPER_SNAKE_CASE`, `lower_snake_case` |
| Example | `DEVICE_TYPE`, `vehicle_type` |
| Specification | [https://api.navixy.com/spec/scalars/code](https://api.navixy.com/spec/scalars/code) |

---

<a id="long"></a>

### Long

Signed 64-bit integer in the range [-9223372036854775808, 9223372036854775807].
Encoded as a JSON number.

| Property | Value |
| -------- | ----- |
| Format | `64-bit signed integer` |
| Example | `1234567890123456789` |
| Specification | [https://www.navixy.com/docs/navixy-repository-api/core-api-reference/common#long](https://www.navixy.com/docs/navixy-repository-api/core-api-reference/common#long) |

---

<a id="date"></a>

### Date

An [ISO 8601](https://www.iso.org/standard/8601.html) date string without time component ([RFC 3339](https://www.rfc-editor.org/rfc/rfc3339.html)). Example: `2024-01-15`.

| Property | Value |
| -------- | ----- |
| Format | `YYYY-MM-DD` |
| Example | `2025-01-15` |
| Specification | [https://scalars.graphql.org/chillicream/date.html](https://scalars.graphql.org/chillicream/date.html) |

---

<a id="geojson"></a>

### GeoJSON

A GeoJSON geometry object ([RFC 7946](https://www.rfc-editor.org/rfc/rfc7946.html)). Supports Point, LineString, Polygon, and other geometry types.

| Property | Value |
| -------- | ----- |
| Format | `GeoJSON geometry object` |
| Example | `{"type": "Point", "coordinates": [125.6, 10.1]}` |
| Specification | [https://www.rfc-editor.org/rfc/rfc7946](https://www.rfc-editor.org/rfc/rfc7946) |

---

<a id="decimal"></a>

### Decimal

Arbitrary-precision decimal value encoded as a JSON string (e.g. `"5.25"`).
String encoding avoids the precision loss inherent in JSON `Float`.

Values exceeding the declared `scale` of a decimal custom field are rounded
server-side using HALF_UP (round half away from zero for positive values,
round half toward zero for negative — matches Java `RoundingMode.HALF_UP`).
The rounded value is persisted in both the CFV table and the
`custom_fields_data` JSONB blob; raw un-rounded input is never stored.

Storage is `numeric(28, 10)` — up to 28 significant digits, of which at
most 10 follow the decimal point (so up to 18 digits before). Values
exceeding this envelope (e.g. more than 18 integer digits) are rejected
as `VALIDATION_ERROR` at the API layer.

| Property | Value |
| -------- | ----- |
| Format | `String-encoded decimal number` |
| Example | `"123.456"` |
| Specification | [https://www.navixy.com/docs/navixy-repository-api/core-api-reference/common#decimal](https://www.navixy.com/docs/navixy-repository-api/core-api-reference/common#decimal) |

---

<a id="currency"></a>

### Currency

[ISO 4217](https://www.iso.org/standard/4217.html) three-letter currency code (for example: USD, EUR, JPY).

---

<a id="uuid"></a>

### UUID

A UUID ([RFC 9562](https://www.rfc-editor.org/rfc/rfc9562.html)) owned by a system outside this API — unlike `ID`, which is always a platform
UUIDv8 carrying an entity-type discriminator. Never accepted by `node(id:)`.

| Property | Value |
| -------- | ----- |
| Specification | [https://www.rfc-editor.org/rfc/rfc9562](https://www.rfc-editor.org/rfc/rfc9562) |

---

<a id="latitude"></a>

### Latitude

A geographic latitude coordinate in decimal degrees. Valid range: -90.0 to 90.0.

| Property | Value |
| -------- | ----- |
| Format | `-90.0 to 90.0` |
| Example | `37.7749` |
| Specification | [https://the-guild.dev/graphql/scalars/docs/scalars/latitude](https://the-guild.dev/graphql/scalars/docs/scalars/latitude) |

---

<a id="longitude"></a>

### Longitude

A geographic longitude coordinate in decimal degrees. Valid range: -180.0 to 180.0.

| Property | Value |
| -------- | ----- |
| Format | `-180.0 to 180.0` |
| Example | `-122.4194` |
| Specification | [https://the-guild.dev/graphql/scalars/docs/scalars/longitude](https://the-guild.dev/graphql/scalars/docs/scalars/longitude) |

---

<a id="locale"></a>

### Locale

A BCP 47 language tag identifying a user locale. Example: `en-US`, `es-MX`, `fr-CA`.

| Property | Value |
| -------- | ----- |
| Format | `language-REGION` |
| Example | `en-US` |
| Specification | [https://the-guild.dev/graphql/scalars/docs/scalars/locale](https://the-guild.dev/graphql/scalars/docs/scalars/locale) |

---

<a id="emailaddress"></a>

### EmailAddress

An email address conforming to [RFC 5322](https://www.rfc-editor.org/rfc/rfc5322.html). Example: `user@example.com`.

| Property | Value |
| -------- | ----- |
| Format | `user@domain` |
| Example | `user@example.com` |
| Specification | [https://the-guild.dev/graphql/scalars/docs/scalars/email-address](https://the-guild.dev/graphql/scalars/docs/scalars/email-address) |

---

<a id="hexcolorcode"></a>

### HexColorCode

A hexadecimal color code. Supports 3-digit (`#RGB`) or 6-digit (`#RRGGBB`) format.

| Property | Value |
| -------- | ----- |
| Format | `#RRGGBB` |
| Example | `#FF5733` |
| Specification | [https://the-guild.dev/graphql/scalars/docs/scalars/hex-color-code](https://the-guild.dev/graphql/scalars/docs/scalars/hex-color-code) |

---

<a id="countrycode"></a>

### CountryCode

An [ISO 3166](https://www.iso.org/standard/3166.html)-1 alpha-2 country code. Example: `US`, `GB`, `ES`.

| Property | Value |
| -------- | ----- |
| Format | `Two uppercase letters` |
| Example | `US` |
| Specification | [https://the-guild.dev/graphql/scalars/docs/scalars/country-code](https://the-guild.dev/graphql/scalars/docs/scalars/country-code) |

---
