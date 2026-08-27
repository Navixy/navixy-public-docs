---
description: >-
  Complete reference for geo objects: queries, mutations, and types for
  geofences, points of interest, and routes with GeoJSON geometry.
---

# Geo objects

{% include ".gitbook/includes/navixy-repository-api-is-a-....md" %}

Geo objects define geographic boundaries and points of interest - geofences, routes, and landmarks used for location-based automation.

## Queries

### geoObjectTypes

Lists geo object types for a workspace.

```graphql
geoObjectTypes(
    workspaceId: ID!
    filter: CatalogItemFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: CatalogItemOrder = { field: ORDER, direction: ASC }
  ): GeoObjectTypeConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve geo object types for. |
| `filter` | `CatalogItemFilter` | Filtering options for the returned geo object types. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `CatalogItemOrder` | The ordering options for the returned geo object types. |

**Input types:**

<details>

<summary>CatalogItemFilter</summary>

Filtering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `codes` | [[Code](common.md#code)!] | Match any of these codes. |

</details>

<details>

<summary>CatalogItemOrder</summary>

Ordering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [CatalogItemOrderField](catalogs/catalog-items.md#catalogitemorderfield)! | The field to order by. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>GeoObjectTypeConnection</summary>

A paginated list of GeoObjectType items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[GeoObjectTypeEdge](#geoobjecttypeedge)!]! | A list of edges. |
| `nodes` | [[GeoObjectType](#geoobjecttype)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### geoObject (query)

Retrieves a geo object by its ID.

```graphql
geoObject(id: ID!): GeoObject
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The ID of the geo object to retrieve. |

**Output types:**

<details>

<summary>GeoObject</summary>

A geographic object such as a geofence, point of interest, or route.

**Implements:** [Node](common.md#node), [Titled](common.md#titled), [Customizable](common.md#customizable), [Versioned](common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](workspaces/README.md#workspace)! | The workspace that owns this geo object. |
| `type` | [GeoObjectType](#geoobjecttype)! | The geo object type classification. |
| `geojsonData` | [GeoJSON](common.md#geojson)! | The geographic shape of this object as GeoJSON geometry. |
| `customFields` | [[CustomFieldValue](custom-fields.md#customfieldvalue)!]! | Typed custom field values, one entry per set field code. Each element is a concrete `CustomFieldValue` implementation matching the field's `FieldType` — select fields via inline fragments. The system-reserved code `geojson_data` is excluded — it is exposed through `GeoObject.geojsonData` instead. |
| `containsPoints` | [[PointContainmentResult](#pointcontainmentresult)!]! | Checks if the given points are contained within this geo object's geometry. Returns the containment status for each point. Only applicable to Polygon and MultiPolygon geometries. |

</details>

---

### geoObjects

Lists geo objects for a workspace.

```graphql
geoObjects(
    workspaceId: ID!
    filter: GeoObjectFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: GeoObjectOrder = { field: TITLE, direction: ASC }
  ): GeoObjectConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve geo objects for. |
| `filter` | `GeoObjectFilter` | Filtering options for the returned geo objects. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `GeoObjectOrder` | The ordering options for the returned geo objects. |

**Input types:**

<details>

<summary>GeoObjectFilter</summary>

Filtering options for geo objects.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `typeIds` | `[ID!]` | Filter by geo object types (OR within field). |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `customFields` | [[CustomFieldFilter](custom-fields.md#customfieldfilter)!] | Filter by custom field values. |

</details>

<details>

<summary>CustomFieldFilter</summary>

A filter condition for a custom field value.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The custom field code to filter by. |
| `operator` | [FieldOperator](custom-fields.md#fieldoperator)! | The comparison operator. |
| `value` | [CustomFieldFilterValue](custom-fields.md#customfieldfiltervalue) | The value to compare against. Null for `IS_NULL` and `IS_NOT_NULL` operators. |

</details>

<details>

<summary>CustomFieldFilterValue</summary>

Typed filter value for custom fields. Exactly one field must be set (`@oneOf`).
Choose the variant that matches the custom field's data type:

| FieldType         | Variant      | Example                                |
|-------------------|--------------|----------------------------------------|
| STRING, TEXT      | `string`     | `{ string: "hello" }`                  |
| DECIMAL           | `decimal`    | `{ decimal: "42.50" }`                 |
| INTEGER           | `integer`    | `{ integer: 42 }`                      |
| BOOLEAN           | `boolean`    | `{ boolean: true }`                    |
| DATE              | `date`       | `{ date: "2024-01-15" }`              |
| DATETIME          | `datetime`   | `{ datetime: "2024-01-15T10:30:00Z" }`|
| OPTIONS           | `string`     | `{ string: "option_code" }`            |
| DEVICE, REFERENCE | `id`         | `{ id: "019a6a3f-..." }`              |
| (IN operator)     | `stringList` | `{ stringList: ["a", "b"] }`           |
| (IN operator)     | `idList`     | `{ idList: ["uuid1", "uuid2"] }`       |

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `string` | `String` | String value — for STRING, TEXT, OPTIONS fields. |
| `decimal` | [Decimal](common.md#decimal) | Arbitrary-precision decimal value — for DECIMAL fields. |
| `integer` | [Long](common.md#long) | Signed 64-bit integer value — for INTEGER fields. |
| `boolean` | `Boolean` | Boolean value — for BOOLEAN fields. |
| `date` | [Date](common.md#date) | Date value — for DATE fields. |
| `datetime` | [DateTime](common.md#datetime) | Date-time value — for DATETIME fields. |
| `id` | `ID` | ID value — for DEVICE, REFERENCE fields. |
| `stringList` | `[String!]` | List of strings — for IN operator on string-based fields. |
| `idList` | `[ID!]` | List of IDs — for IN operator on reference fields. |

</details>

<details>

<summary>GeoObjectOrder</summary>

Ordering options for geo objects.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [GeoObjectOrderField](#geoobjectorderfield) | The standard field to order by. Mutually exclusive with `customFieldCode`. |
| `customFieldCode` | [Code](common.md#code) | The custom field code to order by. Mutually exclusive with `field`. Supported field types: STRING, TEXT, DECIMAL, INTEGER, DATE, DATETIME, and the reference types DEVICE and single-value REFERENCE, which sort by the title of the entity they point at rather than by the stored id. OPTIONS, BOOLEAN and GEOJSON are not supported for sorting, nor is a multi-value REFERENCE or a REFERENCE at an entity with no title. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>GeoObjectConnection</summary>

A paginated list of GeoObject items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[GeoObjectEdge](#geoobjectedge)!]! | A list of edges. |
| `nodes` | [[GeoObject](#geoobject)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Mutations

### geoObjectCreate

Creates a new geo object.

```graphql
geoObjectCreate(
    input: GeoObjectCreateInput!
  ): GeoObjectPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `GeoObjectCreateInput!` | The input fields for creating the geo object. |

**Input types:**

<details>

<summary>GeoObjectCreateInput</summary>

Input for creating a new geo object.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the geo object. |
| `typeId` | `ID!` | The geo object type ID. |
| `title` | `String!` | The geo object display name. |
| `geojsonData` | [GeoJSON](common.md#geojson)! | The GeoJSON geometry. |
| `customFields` | [CustomFieldsPatchInput](custom-fields.md#customfieldspatchinput) | The custom field values. |

</details>

<details>

<summary>CustomFieldsPatchInput</summary>

Input for updating custom field values using a patch model.

`set` provides typed values (create/overwrite); `unset` removes fields by code. Primary
status travels with the value on the primary-capable variants (DEVICE/REFERENCE)
via their required `isPrimary` flag — there is no separate setPrimary/unsetPrimary list.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `set` | [[CustomFieldValueInput](custom-fields.md#customfieldvalueinput)!] | Field values to set or overwrite. |
| `unset` | [[Code](common.md#code)!] | Field codes to remove entirely. |

</details>

<details>

<summary>CustomFieldValueInput</summary>

A single custom-field value to set, addressed by code.

Omitting `value` is the same as passing null, so an entry clears the field unless it carries one.
An `isDefault` override cannot be changed on its own: send the current value alongside it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The custom field code. |
| `value` | [CustomFieldValueDataInput](custom-fields.md#customfieldvaluedatainput) | The typed value. Null clears the field but keeps the key (distinct from `unset`). |
| `isDefault` | `Boolean` | Overrides the definition's `isDefault` for this entity, in either direction. Omit to leave any existing override untouched; the override is dropped when the field is `unset` or its value is cleared. |

</details>

<details>

<summary>CustomFieldValueDataInput</summary>

Typed custom-field value. Exactly one variant must be set (`@oneOf`), matching the custom
field's declared FieldType:

| FieldType          | Variant      |
|--------------------|--------------|
| STRING, TEXT       | `string`     |
| DECIMAL            | `decimal`    |
| INTEGER            | `integer`    |
| BOOLEAN            | `boolean`    |
| DATE               | `date`       |
| DATETIME           | `datetime`   |
| GEOJSON            | `geojson`    |
| DEVICE             | `device`     |
| REFERENCE (single) | `reference`  |
| REFERENCE (multi)  | `references` |
| OPTIONS (single)   | `option`     |
| OPTIONS (multi)    | `options`    |

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `string` | `String` | STRING / TEXT value. |
| `decimal` | [Decimal](common.md#decimal) | DECIMAL value (arbitrary precision, string-encoded). |
| `integer` | [Long](common.md#long) | INTEGER value (signed 64-bit). |
| `boolean` | `Boolean` | BOOLEAN value. |
| `date` | [Date](common.md#date) | DATE value. |
| `datetime` | [DateTime](common.md#datetime) | DATETIME value. |
| `geojson` | [GeoJSON](common.md#geojson) | GEOJSON value. |
| `device` | [DeviceValueInput](custom-fields.md#devicevalueinput) | DEVICE value (primary-capable). |
| `reference` | [ReferenceValueInput](custom-fields.md#referencevalueinput) | REFERENCE value for a single-value field (primary-capable). |
| `references` | [ReferenceListValueInput](custom-fields.md#referencelistvalueinput) | REFERENCE value for an isMulti field (primary-capable). |
| `option` | [Code](common.md#code) | OPTIONS value for a single-value field. |
| `options` | [[Code](common.md#code)!] | OPTIONS value for an isMulti field. |

</details>

<details>

<summary>DeviceValueInput</summary>

DEVICE custom-field value.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The device to assign. |
| `isPrimary` | `Boolean!` | Whether this device is the entity's primary DEVICE. Required — prevents silent demotion. |
| `reassign` | `Boolean` | If the device is already assigned to another asset, detach it there and reassign it here, atomically, instead of failing with VALIDATION_ERROR. Asset holders only. A DEVICE field may only be defined on an AssetType since v0.7.0, but definitions written before it stay live, and on such a field `reassign: true` is rejected with VALIDATION_ERROR naming it: only an asset holds a device exclusively, so there is nothing to detach it from. |

</details>

<details>

<summary>ReferenceValueInput</summary>

REFERENCE custom-field value for a single-value field.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The referenced entity ID. |
| `isPrimary` | `Boolean!` | Whether this reference is the entity's primary REFERENCE. Required — prevents silent demotion. |

</details>

<details>

<summary>ReferenceListValueInput</summary>

REFERENCE custom-field value for an isMulti field.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `ids` | `[ID!]!` | The referenced entity IDs, in the order they are stored. |
| `isPrimary` | `Boolean!` | Whether this reference is the entity's primary REFERENCE. Required — prevents silent demotion. |

</details>

**Output types:**

<details>

<summary>GeoObjectPayload</summary>

The result of a geo object mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `geoObject` | [GeoObject](#geoobject)! | The created or updated geo object. |

</details>

<details>

<summary>GeoObject (entity)</summary>

A geographic object such as a geofence, point of interest, or route.

**Implements:** [Node](common.md#node), [Titled](common.md#titled), [Customizable](common.md#customizable), [Versioned](common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](workspaces/README.md#workspace)! | The workspace that owns this geo object. |
| `type` | [GeoObjectType](#geoobjecttype)! | The geo object type classification. |
| `geojsonData` | [GeoJSON](common.md#geojson)! | The geographic shape of this object as GeoJSON geometry. |
| `customFields` | [[CustomFieldValue](custom-fields.md#customfieldvalue)!]! | Typed custom field values, one entry per set field code. Each element is a concrete `CustomFieldValue` implementation matching the field's `FieldType` — select fields via inline fragments. The system-reserved code `geojson_data` is excluded — it is exposed through `GeoObject.geojsonData` instead. |
| `containsPoints` | [[PointContainmentResult](#pointcontainmentresult)!]! | Checks if the given points are contained within this geo object's geometry. Returns the containment status for each point. Only applicable to Polygon and MultiPolygon geometries. |

</details>

---

### geoObjectUpdate

Updates an existing geo object.

```graphql
geoObjectUpdate(
    input: GeoObjectUpdateInput!
  ): GeoObjectPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `GeoObjectUpdateInput!` | The input fields for updating the geo object. |

**Input types:**

<details>

<summary>GeoObjectUpdateInput</summary>

Input for updating an existing geo object.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The geo object ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `geojsonData` | [GeoJSON](common.md#geojson) | The new geometry. |
| `customFields` | [CustomFieldsPatchInput](custom-fields.md#customfieldspatchinput) | The custom field changes. |

</details>

<details>

<summary>CustomFieldsPatchInput</summary>

Input for updating custom field values using a patch model.

`set` provides typed values (create/overwrite); `unset` removes fields by code. Primary
status travels with the value on the primary-capable variants (DEVICE/REFERENCE)
via their required `isPrimary` flag — there is no separate setPrimary/unsetPrimary list.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `set` | [[CustomFieldValueInput](custom-fields.md#customfieldvalueinput)!] | Field values to set or overwrite. |
| `unset` | [[Code](common.md#code)!] | Field codes to remove entirely. |

</details>

<details>

<summary>CustomFieldValueInput</summary>

A single custom-field value to set, addressed by code.

Omitting `value` is the same as passing null, so an entry clears the field unless it carries one.
An `isDefault` override cannot be changed on its own: send the current value alongside it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The custom field code. |
| `value` | [CustomFieldValueDataInput](custom-fields.md#customfieldvaluedatainput) | The typed value. Null clears the field but keeps the key (distinct from `unset`). |
| `isDefault` | `Boolean` | Overrides the definition's `isDefault` for this entity, in either direction. Omit to leave any existing override untouched; the override is dropped when the field is `unset` or its value is cleared. |

</details>

<details>

<summary>CustomFieldValueDataInput</summary>

Typed custom-field value. Exactly one variant must be set (`@oneOf`), matching the custom
field's declared FieldType:

| FieldType          | Variant      |
|--------------------|--------------|
| STRING, TEXT       | `string`     |
| DECIMAL            | `decimal`    |
| INTEGER            | `integer`    |
| BOOLEAN            | `boolean`    |
| DATE               | `date`       |
| DATETIME           | `datetime`   |
| GEOJSON            | `geojson`    |
| DEVICE             | `device`     |
| REFERENCE (single) | `reference`  |
| REFERENCE (multi)  | `references` |
| OPTIONS (single)   | `option`     |
| OPTIONS (multi)    | `options`    |

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `string` | `String` | STRING / TEXT value. |
| `decimal` | [Decimal](common.md#decimal) | DECIMAL value (arbitrary precision, string-encoded). |
| `integer` | [Long](common.md#long) | INTEGER value (signed 64-bit). |
| `boolean` | `Boolean` | BOOLEAN value. |
| `date` | [Date](common.md#date) | DATE value. |
| `datetime` | [DateTime](common.md#datetime) | DATETIME value. |
| `geojson` | [GeoJSON](common.md#geojson) | GEOJSON value. |
| `device` | [DeviceValueInput](custom-fields.md#devicevalueinput) | DEVICE value (primary-capable). |
| `reference` | [ReferenceValueInput](custom-fields.md#referencevalueinput) | REFERENCE value for a single-value field (primary-capable). |
| `references` | [ReferenceListValueInput](custom-fields.md#referencelistvalueinput) | REFERENCE value for an isMulti field (primary-capable). |
| `option` | [Code](common.md#code) | OPTIONS value for a single-value field. |
| `options` | [[Code](common.md#code)!] | OPTIONS value for an isMulti field. |

</details>

<details>

<summary>DeviceValueInput</summary>

DEVICE custom-field value.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The device to assign. |
| `isPrimary` | `Boolean!` | Whether this device is the entity's primary DEVICE. Required — prevents silent demotion. |
| `reassign` | `Boolean` | If the device is already assigned to another asset, detach it there and reassign it here, atomically, instead of failing with VALIDATION_ERROR. Asset holders only. A DEVICE field may only be defined on an AssetType since v0.7.0, but definitions written before it stay live, and on such a field `reassign: true` is rejected with VALIDATION_ERROR naming it: only an asset holds a device exclusively, so there is nothing to detach it from. |

</details>

<details>

<summary>ReferenceValueInput</summary>

REFERENCE custom-field value for a single-value field.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The referenced entity ID. |
| `isPrimary` | `Boolean!` | Whether this reference is the entity's primary REFERENCE. Required — prevents silent demotion. |

</details>

<details>

<summary>ReferenceListValueInput</summary>

REFERENCE custom-field value for an isMulti field.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `ids` | `[ID!]!` | The referenced entity IDs, in the order they are stored. |
| `isPrimary` | `Boolean!` | Whether this reference is the entity's primary REFERENCE. Required — prevents silent demotion. |

</details>

**Output types:**

<details>

<summary>GeoObjectPayload</summary>

The result of a geo object mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `geoObject` | [GeoObject](#geoobject)! | The created or updated geo object. |

</details>

<details>

<summary>GeoObject (entity)</summary>

A geographic object such as a geofence, point of interest, or route.

**Implements:** [Node](common.md#node), [Titled](common.md#titled), [Customizable](common.md#customizable), [Versioned](common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](workspaces/README.md#workspace)! | The workspace that owns this geo object. |
| `type` | [GeoObjectType](#geoobjecttype)! | The geo object type classification. |
| `geojsonData` | [GeoJSON](common.md#geojson)! | The geographic shape of this object as GeoJSON geometry. |
| `customFields` | [[CustomFieldValue](custom-fields.md#customfieldvalue)!]! | Typed custom field values, one entry per set field code. Each element is a concrete `CustomFieldValue` implementation matching the field's `FieldType` — select fields via inline fragments. The system-reserved code `geojson_data` is excluded — it is exposed through `GeoObject.geojsonData` instead. |
| `containsPoints` | [[PointContainmentResult](#pointcontainmentresult)!]! | Checks if the given points are contained within this geo object's geometry. Returns the containment status for each point. Only applicable to Polygon and MultiPolygon geometries. |

</details>

---

### geoObjectDelete

Deletes a geo object.

```graphql
geoObjectDelete(
    input: GeoObjectDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `GeoObjectDeleteInput!` | The input fields for deleting the geo object. |

**Input types:**

<details>

<summary>GeoObjectDeleteInput</summary>

Input for deleting a geo object.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The geo object ID to delete. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

</details>

**Output types:**

<details>

<summary>DeletePayload</summary>

The result of a delete mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deletedId` | `ID!` | The ID of the deleted entity. |

</details>

---

### geoObjectTypeCreate

Creates a new geo object type.

```graphql
geoObjectTypeCreate(
    input: GeoObjectTypeCreateInput!
  ): GeoObjectTypePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `GeoObjectTypeCreateInput!` | The input fields for creating the geo object type. |

**Input types:**

<details>

<summary>GeoObjectTypeCreateInput</summary>

Input for creating a geo object type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the item. |
| `code` | [Code](common.md#code) | The machine-readable code. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `meta` | [CatalogItemMetaInput](catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinitionInput](custom-fields.md#customfielddefinitioninput)!] | Operations on custom field definitions for this geo object type. Only `create` is allowed when creating a new catalog item. |

</details>

<details>

<summary>CatalogItemMetaInput</summary>

Display properties for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `description` | `String` | The description. |
| `hidden` | `Boolean` | Whether the item is hidden from regular UI lists. |

</details>

<details>

<summary>CustomFieldDefinitionInput</summary>

A single operation on a custom field definition within the parent catalog item.
Exactly one action must be provided.

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `create` | [CustomFieldDefinitionCreateData](custom-fields.md#customfielddefinitioncreatedata) | Create a new custom field definition. |
| `update` | [CustomFieldDefinitionUpdateData](custom-fields.md#customfielddefinitionupdatedata) | Update an existing custom field definition. |
| `delete` | [CustomFieldDefinitionDeleteData](custom-fields.md#customfielddefinitiondeletedata) | Delete a custom field definition. |
| `archive` | [CustomFieldDefinitionArchiveData](custom-fields.md#customfielddefinitionarchivedata) | Archive a custom field definition (non-destructive deactivation). |
| `restore` | [CustomFieldDefinitionRestoreData](custom-fields.md#customfielddefinitionrestoredata) | Restore a previously archived custom field definition. |

</details>

<details>

<summary>CustomFieldDefinitionCreateData</summary>

Data for creating a custom field definition within its parent catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code) | The machine-readable code. Must start with `cf_`, so a custom-field code can never be confused with an entity's own field name. Auto-generated from the title if omitted, prefix included. The prefix also keeps this input clear of the platform's own reserved code `geojson_data`. |
| `title` | `String!` | The display name. |
| `description` | `String` | The description. |
| `fieldType` | [FieldType](custom-fields.md#fieldtype)! | The data type. Immutable after creation. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `params` | [FieldParamsInput](custom-fields.md#fieldparamsinput)! | The type-specific parameters. Exactly one variant must be provided. |
| `isDefault` | `Boolean` | Whether the field is on by default for entities of this type. Defaults to false. |

</details>

<details>

<summary>FieldParamsInput</summary>

Field parameters input. Exactly one field must be provided.

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `string` | [StringFieldParamsInput](custom-fields.md#stringfieldparamsinput) | Parameters for STRING field type. |
| `text` | [TextFieldParamsInput](custom-fields.md#textfieldparamsinput) | Parameters for TEXT field type. |
| `decimal` | [DecimalFieldParamsInput](custom-fields.md#decimalfieldparamsinput) | Parameters for DECIMAL field type. |
| `integer` | [IntegerFieldParamsInput](custom-fields.md#integerfieldparamsinput) | Parameters for INTEGER field type. |
| `boolean` | [BooleanFieldParamsInput](custom-fields.md#booleanfieldparamsinput) | Parameters for BOOLEAN field type. |
| `date` | [DateFieldParamsInput](custom-fields.md#datefieldparamsinput) | Parameters for DATE field type. |
| `datetime` | [DateTimeFieldParamsInput](custom-fields.md#datetimefieldparamsinput) | Parameters for DATETIME field type. |
| `geojson` | [GeoJsonFieldParamsInput](custom-fields.md#geojsonfieldparamsinput) | Parameters for GEOJSON field type. |
| `options` | [OptionsFieldParamsInput](custom-fields.md#optionsfieldparamsinput) | Parameters for OPTIONS field type. |
| `device` | [DeviceFieldParamsInput](custom-fields.md#devicefieldparamsinput) | Parameters for DEVICE field type. |
| `reference` | [ReferenceFieldParamsInput](custom-fields.md#referencefieldparamsinput) | Parameters for REFERENCE field type. |

</details>

<details>

<summary>StringFieldParamsInput</summary>

Parameters for STRING field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minLength` | `Int` | The minimum character length. |
| `maxLength` | `Int` | The maximum character length. |
| `defaultString` | `String` | The default value. |
| `trim` | `Boolean` | Whether to trim whitespace. |

</details>

<details>

<summary>TextFieldParamsInput</summary>

Parameters for TEXT field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `maxLength` | `Int` | The maximum character length. |
| `defaultText` | `String` | The default value. |
| `trim` | `Boolean` | Whether to trim whitespace. |

</details>

<details>

<summary>DecimalFieldParamsInput</summary>

Parameters for DECIMAL field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minDecimal` | [Decimal](common.md#decimal) | The minimum allowed value. |
| `maxDecimal` | [Decimal](common.md#decimal) | The maximum allowed value. |
| `scale` | `Int!` | Digits after the decimal point. Must be >= 0. Values sent with more fractional digits than `scale` are rounded using HALF_UP before storage. |
| `defaultDecimal` | [Decimal](common.md#decimal) | The default value. |

</details>

<details>

<summary>IntegerFieldParamsInput</summary>

Parameters for INTEGER field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minInteger` | [Long](common.md#long) | The minimum allowed value. |
| `maxInteger` | [Long](common.md#long) | The maximum allowed value. |
| `defaultInteger` | [Long](common.md#long) | The default value. |

</details>

<details>

<summary>BooleanFieldParamsInput</summary>

Parameters for BOOLEAN field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `defaultBoolean` | `Boolean` | The default value. |

</details>

<details>

<summary>DateFieldParamsInput</summary>

Parameters for DATE field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `defaultDate` | [Date](common.md#date) | The default value. |

</details>

<details>

<summary>DateTimeFieldParamsInput</summary>

Parameters for DATETIME field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `defaultDatetime` | [DateTime](common.md#datetime) | The default value. |

</details>

<details>

<summary>GeoJsonFieldParamsInput</summary>

Parameters for GEOJSON field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `allowedTypes` | [[GeoJsonGeometryType](#geojsongeometrytype)!] | The allowed geometry types. Null means all types are allowed. |

</details>

<details>

<summary>OptionsFieldParamsInput</summary>

Parameters for OPTIONS field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `isMulti` | `Boolean` | Whether multiple options can be selected. |
| `options` | [[FieldOptionInput](custom-fields.md#fieldoptioninput)!]! | The available options. |
| `defaultOptions` | [Code](common.md#code) | The default option code. |

</details>

<details>

<summary>FieldOptionInput</summary>

Input for an option definition.
When updating options: if an entry without `code` is provided, a new option is created.
If the label already exists within this field, an error is returned.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code) | The unique code. Auto-generated from label if omitted. |
| `label` | `String!` | The display label. Must be unique within the custom field. |
| `description` | `String` | The description. |
| `isArchived` | `Boolean` | Whether this option is archived. |

</details>

<details>

<summary>DeviceFieldParamsInput</summary>

Parameters for DEVICE field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `refSubtypeIds` | `[ID!]` | The device type IDs a value may belong to. Omit or leave empty to allow any device type. |

</details>

<details>

<summary>ReferenceFieldParamsInput</summary>

Parameters for REFERENCE field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `isMulti` | `Boolean` | Whether multiple entities can be referenced. |
| `refEntityTypeCode` | [Code](common.md#code)! | The target entity type code, e.g. `asset`, `geo_object`, `schedule`, `tag`, `user_catalog_item`. The `customFieldTypes` query lists the accepted values for a given owner. |
| `refSubtypeIds` | `[ID!]` | The subtype IDs a value may belong to (AssetType / GeoObjectType / catalog). Required for `user_catalog_item`, which needs exactly one catalog; rejected for target types that nothing narrows further. Omit or leave empty to allow the whole target type. |
| `defaultRefIds` | `[ID!]` | The default referenced entity IDs. |

</details>

<details>

<summary>CustomFieldDefinitionUpdateData</summary>

Data for updating an existing custom field definition. Note: `fieldType` cannot be changed.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The code of the field definition to update. |
| `title` | `String` | The new display name. |
| `description` | `String` | The new description. |
| `order` | `Int` | The new display order. |
| `params` | [FieldParamsInput](custom-fields.md#fieldparamsinput) | The updated parameters. Only `isRequired` and type-specific fields can be changed. |
| `isDefault` | `Boolean` | The new type-level default. Omit to leave unchanged. |

</details>

<details>

<summary>CustomFieldDefinitionDeleteData</summary>

Data for permanently deleting a custom field definition.

If entities have values for this field, the default behavior is to reject the deletion.
Use `onValues: CASCADE` to explicitly allow deletion with all associated values.

Prefer archiving for non-destructive deactivation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The code of the field definition to delete. |
| `onValues` | [CustomFieldDefinitionDeleteBehavior](custom-fields.md#customfielddefinitiondeletebehavior) | What to do when existing entities have values for this field. Defaults to `REJECT` to prevent accidental data loss. |

</details>

<details>

<summary>CustomFieldDefinitionArchiveData</summary>

Data for archiving or restoring a custom field definition.

Archiving deactivates the field without data loss:
- The field definition and all its values are preserved.
- The field no longer appears in forms and accepts no new values.
- Existing values remain readable and visible in history/exports.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The code of the field definition to archive. |

</details>

<details>

<summary>CustomFieldDefinitionRestoreData</summary>

Data for restoring a previously archived custom field definition.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The code of the field definition to restore. |

</details>

**Output types:**

<details>

<summary>GeoObjectTypePayload</summary>

The result of a geo object type mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `geoObjectType` | [GeoObjectType](#geoobjecttype)! | The created or updated geo object type. |

</details>

<details>

<summary>GeoObjectType (entity)</summary>

A classification type for geographic objects.

**Implements:** [CatalogItem](catalogs/catalog-items.md#catalogitem), [Node](common.md#node), [Versioned](common.md#versioned), [Titled](common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinition](custom-fields.md#customfielddefinition)!]! | Custom field definitions specific to this geo object type, ordered by display order. |

</details>

---

### geoObjectTypeUpdate

Updates a geo object type.

```graphql
geoObjectTypeUpdate(
    input: GeoObjectTypeUpdateInput!
  ): GeoObjectTypePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `GeoObjectTypeUpdateInput!` | The input fields for updating the geo object type. |

**Input types:**

<details>

<summary>GeoObjectTypeUpdateInput</summary>

Input for updating a geo object type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `meta` | [CatalogItemMetaInput](catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinitionInput](custom-fields.md#customfielddefinitioninput)!] | Operations on custom field definitions belonging to this geo object type. |

</details>

<details>

<summary>CatalogItemMetaInput</summary>

Display properties for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `description` | `String` | The description. |
| `hidden` | `Boolean` | Whether the item is hidden from regular UI lists. |

</details>

<details>

<summary>CustomFieldDefinitionInput</summary>

A single operation on a custom field definition within the parent catalog item.
Exactly one action must be provided.

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `create` | [CustomFieldDefinitionCreateData](custom-fields.md#customfielddefinitioncreatedata) | Create a new custom field definition. |
| `update` | [CustomFieldDefinitionUpdateData](custom-fields.md#customfielddefinitionupdatedata) | Update an existing custom field definition. |
| `delete` | [CustomFieldDefinitionDeleteData](custom-fields.md#customfielddefinitiondeletedata) | Delete a custom field definition. |
| `archive` | [CustomFieldDefinitionArchiveData](custom-fields.md#customfielddefinitionarchivedata) | Archive a custom field definition (non-destructive deactivation). |
| `restore` | [CustomFieldDefinitionRestoreData](custom-fields.md#customfielddefinitionrestoredata) | Restore a previously archived custom field definition. |

</details>

<details>

<summary>CustomFieldDefinitionCreateData</summary>

Data for creating a custom field definition within its parent catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code) | The machine-readable code. Must start with `cf_`, so a custom-field code can never be confused with an entity's own field name. Auto-generated from the title if omitted, prefix included. The prefix also keeps this input clear of the platform's own reserved code `geojson_data`. |
| `title` | `String!` | The display name. |
| `description` | `String` | The description. |
| `fieldType` | [FieldType](custom-fields.md#fieldtype)! | The data type. Immutable after creation. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `params` | [FieldParamsInput](custom-fields.md#fieldparamsinput)! | The type-specific parameters. Exactly one variant must be provided. |
| `isDefault` | `Boolean` | Whether the field is on by default for entities of this type. Defaults to false. |

</details>

<details>

<summary>FieldParamsInput</summary>

Field parameters input. Exactly one field must be provided.

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `string` | [StringFieldParamsInput](custom-fields.md#stringfieldparamsinput) | Parameters for STRING field type. |
| `text` | [TextFieldParamsInput](custom-fields.md#textfieldparamsinput) | Parameters for TEXT field type. |
| `decimal` | [DecimalFieldParamsInput](custom-fields.md#decimalfieldparamsinput) | Parameters for DECIMAL field type. |
| `integer` | [IntegerFieldParamsInput](custom-fields.md#integerfieldparamsinput) | Parameters for INTEGER field type. |
| `boolean` | [BooleanFieldParamsInput](custom-fields.md#booleanfieldparamsinput) | Parameters for BOOLEAN field type. |
| `date` | [DateFieldParamsInput](custom-fields.md#datefieldparamsinput) | Parameters for DATE field type. |
| `datetime` | [DateTimeFieldParamsInput](custom-fields.md#datetimefieldparamsinput) | Parameters for DATETIME field type. |
| `geojson` | [GeoJsonFieldParamsInput](custom-fields.md#geojsonfieldparamsinput) | Parameters for GEOJSON field type. |
| `options` | [OptionsFieldParamsInput](custom-fields.md#optionsfieldparamsinput) | Parameters for OPTIONS field type. |
| `device` | [DeviceFieldParamsInput](custom-fields.md#devicefieldparamsinput) | Parameters for DEVICE field type. |
| `reference` | [ReferenceFieldParamsInput](custom-fields.md#referencefieldparamsinput) | Parameters for REFERENCE field type. |

</details>

<details>

<summary>StringFieldParamsInput</summary>

Parameters for STRING field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minLength` | `Int` | The minimum character length. |
| `maxLength` | `Int` | The maximum character length. |
| `defaultString` | `String` | The default value. |
| `trim` | `Boolean` | Whether to trim whitespace. |

</details>

<details>

<summary>TextFieldParamsInput</summary>

Parameters for TEXT field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `maxLength` | `Int` | The maximum character length. |
| `defaultText` | `String` | The default value. |
| `trim` | `Boolean` | Whether to trim whitespace. |

</details>

<details>

<summary>DecimalFieldParamsInput</summary>

Parameters for DECIMAL field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minDecimal` | [Decimal](common.md#decimal) | The minimum allowed value. |
| `maxDecimal` | [Decimal](common.md#decimal) | The maximum allowed value. |
| `scale` | `Int!` | Digits after the decimal point. Must be >= 0. Values sent with more fractional digits than `scale` are rounded using HALF_UP before storage. |
| `defaultDecimal` | [Decimal](common.md#decimal) | The default value. |

</details>

<details>

<summary>IntegerFieldParamsInput</summary>

Parameters for INTEGER field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minInteger` | [Long](common.md#long) | The minimum allowed value. |
| `maxInteger` | [Long](common.md#long) | The maximum allowed value. |
| `defaultInteger` | [Long](common.md#long) | The default value. |

</details>

<details>

<summary>BooleanFieldParamsInput</summary>

Parameters for BOOLEAN field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `defaultBoolean` | `Boolean` | The default value. |

</details>

<details>

<summary>DateFieldParamsInput</summary>

Parameters for DATE field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `defaultDate` | [Date](common.md#date) | The default value. |

</details>

<details>

<summary>DateTimeFieldParamsInput</summary>

Parameters for DATETIME field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `defaultDatetime` | [DateTime](common.md#datetime) | The default value. |

</details>

<details>

<summary>GeoJsonFieldParamsInput</summary>

Parameters for GEOJSON field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `allowedTypes` | [[GeoJsonGeometryType](#geojsongeometrytype)!] | The allowed geometry types. Null means all types are allowed. |

</details>

<details>

<summary>OptionsFieldParamsInput</summary>

Parameters for OPTIONS field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `isMulti` | `Boolean` | Whether multiple options can be selected. |
| `options` | [[FieldOptionInput](custom-fields.md#fieldoptioninput)!]! | The available options. |
| `defaultOptions` | [Code](common.md#code) | The default option code. |

</details>

<details>

<summary>FieldOptionInput</summary>

Input for an option definition.
When updating options: if an entry without `code` is provided, a new option is created.
If the label already exists within this field, an error is returned.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code) | The unique code. Auto-generated from label if omitted. |
| `label` | `String!` | The display label. Must be unique within the custom field. |
| `description` | `String` | The description. |
| `isArchived` | `Boolean` | Whether this option is archived. |

</details>

<details>

<summary>DeviceFieldParamsInput</summary>

Parameters for DEVICE field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `refSubtypeIds` | `[ID!]` | The device type IDs a value may belong to. Omit or leave empty to allow any device type. |

</details>

<details>

<summary>ReferenceFieldParamsInput</summary>

Parameters for REFERENCE field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `isMulti` | `Boolean` | Whether multiple entities can be referenced. |
| `refEntityTypeCode` | [Code](common.md#code)! | The target entity type code, e.g. `asset`, `geo_object`, `schedule`, `tag`, `user_catalog_item`. The `customFieldTypes` query lists the accepted values for a given owner. |
| `refSubtypeIds` | `[ID!]` | The subtype IDs a value may belong to (AssetType / GeoObjectType / catalog). Required for `user_catalog_item`, which needs exactly one catalog; rejected for target types that nothing narrows further. Omit or leave empty to allow the whole target type. |
| `defaultRefIds` | `[ID!]` | The default referenced entity IDs. |

</details>

<details>

<summary>CustomFieldDefinitionUpdateData</summary>

Data for updating an existing custom field definition. Note: `fieldType` cannot be changed.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The code of the field definition to update. |
| `title` | `String` | The new display name. |
| `description` | `String` | The new description. |
| `order` | `Int` | The new display order. |
| `params` | [FieldParamsInput](custom-fields.md#fieldparamsinput) | The updated parameters. Only `isRequired` and type-specific fields can be changed. |
| `isDefault` | `Boolean` | The new type-level default. Omit to leave unchanged. |

</details>

<details>

<summary>CustomFieldDefinitionDeleteData</summary>

Data for permanently deleting a custom field definition.

If entities have values for this field, the default behavior is to reject the deletion.
Use `onValues: CASCADE` to explicitly allow deletion with all associated values.

Prefer archiving for non-destructive deactivation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The code of the field definition to delete. |
| `onValues` | [CustomFieldDefinitionDeleteBehavior](custom-fields.md#customfielddefinitiondeletebehavior) | What to do when existing entities have values for this field. Defaults to `REJECT` to prevent accidental data loss. |

</details>

<details>

<summary>CustomFieldDefinitionArchiveData</summary>

Data for archiving or restoring a custom field definition.

Archiving deactivates the field without data loss:
- The field definition and all its values are preserved.
- The field no longer appears in forms and accepts no new values.
- Existing values remain readable and visible in history/exports.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The code of the field definition to archive. |

</details>

<details>

<summary>CustomFieldDefinitionRestoreData</summary>

Data for restoring a previously archived custom field definition.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The code of the field definition to restore. |

</details>

**Output types:**

<details>

<summary>GeoObjectTypePayload</summary>

The result of a geo object type mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `geoObjectType` | [GeoObjectType](#geoobjecttype)! | The created or updated geo object type. |

</details>

<details>

<summary>GeoObjectType (entity)</summary>

A classification type for geographic objects.

**Implements:** [CatalogItem](catalogs/catalog-items.md#catalogitem), [Node](common.md#node), [Versioned](common.md#versioned), [Titled](common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinition](custom-fields.md#customfielddefinition)!]! | Custom field definitions specific to this geo object type, ordered by display order. |

</details>

---

### geoObjectTypeDelete

Deletes a geo object type.

```graphql
geoObjectTypeDelete(
    input: CatalogItemDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `CatalogItemDeleteInput!` | The input fields for deleting the geo object type. |

**Input types:**

<details>

<summary>CatalogItemDeleteInput</summary>

Input for deleting a catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The catalog item ID to delete. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

</details>

**Output types:**

<details>

<summary>DeletePayload</summary>

The result of a delete mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deletedId` | `ID!` | The ID of the deleted entity. |

</details>

---

## Objects

<a id="geoobjecttype"></a>

### GeoObjectType

A classification type for geographic objects.

**Implements:** [CatalogItem](catalogs/catalog-items.md#catalogitem), [Node](common.md#node), [Versioned](common.md#versioned), [Titled](common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinition](custom-fields.md#customfielddefinition)!]! | Custom field definitions specific to this geo object type, ordered by display order. |

---

<a id="geopoint"></a>

### GeoPoint

A geographic coordinate point.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `lat` | [Latitude](common.md#latitude)! | The latitude coordinate in decimal degrees. |
| `lng` | [Longitude](common.md#longitude)! | The longitude coordinate in decimal degrees. |
| `altitude` | `Float` | The altitude in meters above sea level. |
| `accuracy` | `Float` | The horizontal accuracy in meters. |

---

<a id="geoobject"></a>

### GeoObject

A geographic object such as a geofence, point of interest, or route.

**Implements:** [Node](common.md#node), [Titled](common.md#titled), [Customizable](common.md#customizable), [Versioned](common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](workspaces/README.md#workspace)! | The workspace that owns this geo object. |
| `type` | [GeoObjectType](#geoobjecttype)! | The geo object type classification. |
| `geojsonData` | [GeoJSON](common.md#geojson)! | The geographic shape of this object as GeoJSON geometry. |
| `customFields` | [[CustomFieldValue](custom-fields.md#customfieldvalue)!]! | Typed custom field values, one entry per set field code. Each element is a concrete `CustomFieldValue` implementation matching the field's `FieldType` — select fields via inline fragments. The system-reserved code `geojson_data` is excluded — it is exposed through `GeoObject.geojsonData` instead. |
| `containsPoints` | [[PointContainmentResult](#pointcontainmentresult)!]! | Checks if the given points are contained within this geo object's geometry. Returns the containment status for each point. Only applicable to Polygon and MultiPolygon geometries. |

---

<a id="pointcontainmentresult"></a>

### PointContainmentResult

The result of checking whether a point is contained within a geometry.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `index` | `Int!` | The index of the point in the input array (0-based). |
| `point` | [GeoPoint](#geopoint)! | The point that was checked. |
| `isContained` | `Boolean!` | Whether the point is inside the geometry. |

---

<a id="geoobjectpayload"></a>

### GeoObjectPayload

The result of a geo object mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `geoObject` | [GeoObject](#geoobject)! | The created or updated geo object. |

---

<a id="geoobjecttypepayload"></a>

### GeoObjectTypePayload

The result of a geo object type mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `geoObjectType` | [GeoObjectType](#geoobjecttype)! | The created or updated geo object type. |

---

## Inputs

<a id="geopointinput"></a>

### GeoPointInput

Input for a geographic coordinate point.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `lat` | [Latitude](common.md#latitude)! | The latitude coordinate (-90 to 90 degrees). |
| `lng` | [Longitude](common.md#longitude)! | The longitude coordinate (-180 to 180 degrees). |
| `altitude` | `Float` | The altitude in meters above sea level. |
| `accuracy` | `Float` | The horizontal accuracy in meters. |

---

<a id="geoobjectfilter"></a>

### GeoObjectFilter

Filtering options for geo objects.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `typeIds` | `[ID!]` | Filter by geo object types (OR within field). |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `customFields` | [[CustomFieldFilter](custom-fields.md#customfieldfilter)!] | Filter by custom field values. |

---

<a id="geoobjectorder"></a>

### GeoObjectOrder

Ordering options for geo objects.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [GeoObjectOrderField](#geoobjectorderfield) | The standard field to order by. Mutually exclusive with `customFieldCode`. |
| `customFieldCode` | [Code](common.md#code) | The custom field code to order by. Mutually exclusive with `field`. Supported field types: STRING, TEXT, DECIMAL, INTEGER, DATE, DATETIME, and the reference types DEVICE and single-value REFERENCE, which sort by the title of the entity they point at rather than by the stored id. OPTIONS, BOOLEAN and GEOJSON are not supported for sorting, nor is a multi-value REFERENCE or a REFERENCE at an entity with no title. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

---

<a id="geoobjectcreateinput"></a>

### GeoObjectCreateInput

Input for creating a new geo object.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the geo object. |
| `typeId` | `ID!` | The geo object type ID. |
| `title` | `String!` | The geo object display name. |
| `geojsonData` | [GeoJSON](common.md#geojson)! | The GeoJSON geometry. |
| `customFields` | [CustomFieldsPatchInput](custom-fields.md#customfieldspatchinput) | The custom field values. |

---

<a id="geoobjectupdateinput"></a>

### GeoObjectUpdateInput

Input for updating an existing geo object.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The geo object ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `geojsonData` | [GeoJSON](common.md#geojson) | The new geometry. |
| `customFields` | [CustomFieldsPatchInput](custom-fields.md#customfieldspatchinput) | The custom field changes. |

---

<a id="geoobjectdeleteinput"></a>

### GeoObjectDeleteInput

Input for deleting a geo object.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The geo object ID to delete. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

---

<a id="geoobjecttypecreateinput"></a>

### GeoObjectTypeCreateInput

Input for creating a geo object type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the item. |
| `code` | [Code](common.md#code) | The machine-readable code. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `meta` | [CatalogItemMetaInput](catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinitionInput](custom-fields.md#customfielddefinitioninput)!] | Operations on custom field definitions for this geo object type. Only `create` is allowed when creating a new catalog item. |

---

<a id="geoobjecttypeupdateinput"></a>

### GeoObjectTypeUpdateInput

Input for updating a geo object type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `meta` | [CatalogItemMetaInput](catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinitionInput](custom-fields.md#customfielddefinitioninput)!] | Operations on custom field definitions belonging to this geo object type. |

---

## Enums

<a id="geojsongeometrytype"></a>

### GeoJsonGeometryType

The type of GeoJSON geometry.

| Value | Description |
| ----- | ----------- |
| `POINT` | A single geographic point. |
| `MULTI_POINT` | A collection of points. |
| `LINE_STRING` | A sequence of connected line segments. |
| `MULTI_LINE_STRING` | A collection of line strings. |
| `POLYGON` | A closed shape defined by a linear ring. |
| `MULTI_POLYGON` | A collection of polygons. |
| `GEOMETRY_COLLECTION` | A heterogeneous collection of geometry objects. |

---

<a id="geoobjectorderfield"></a>

### GeoObjectOrderField

Fields available for ordering geo objects.

| Value | Description |
| ----- | ----------- |
| `TITLE` | Order by title. |

---

## Pagination types

<a id="geoobjectconnection"></a>

### GeoObjectConnection

A paginated list of GeoObject items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[GeoObjectEdge](#geoobjectedge)!]! | A list of edges. |
| `nodes` | [[GeoObject](#geoobject)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

---

<a id="geoobjectedge"></a>

### GeoObjectEdge

An edge in the GeoObject connection.

**Implements:** [Edge](common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [GeoObject](#geoobject)! | The geo object at the end of the edge. |

---

<a id="geoobjecttypeconnection"></a>

### GeoObjectTypeConnection

A paginated list of GeoObjectType items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[GeoObjectTypeEdge](#geoobjecttypeedge)!]! | A list of edges. |
| `nodes` | [[GeoObjectType](#geoobjecttype)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

---

<a id="geoobjecttypeedge"></a>

### GeoObjectTypeEdge

An edge in the GeoObjectType connection.

**Implements:** [Edge](common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [GeoObjectType](#geoobjecttype)! | The geo object type at the end of the edge. |

---
