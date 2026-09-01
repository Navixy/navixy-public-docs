---
description: >-
  Complete reference for assets: queries, mutations, and types for the trackable
  business objects your workspace monitors and manages.
---

# Assets

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

Assets represent any trackable business object - vehicles, equipment, personnel, or any other entity you need to monitor and manage.

## Queries

### assetTypes

Lists asset types for a workspace.

```graphql
assetTypes(
    workspaceId: ID!
    filter: CatalogItemFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: CatalogItemOrder = { field: ORDER, direction: ASC }
  ): AssetTypeConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve asset types for. |
| `filter` | `CatalogItemFilter` | Filtering options for the returned asset types. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `CatalogItemOrder` | The ordering options for the returned asset types. |

**Input types:**

<details>

<summary>CatalogItemFilter</summary>

Filtering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `codes` | [[Code](../common.md#code)!] | Match any of these codes. |

</details>

<details>

<summary>CatalogItemOrder</summary>

Ordering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [CatalogItemOrderField](../catalogs/catalog-items.md#catalogitemorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>AssetTypeConnection</summary>

A paginated list of AssetType items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AssetTypeEdge](#assettypeedge)!]! | A list of edges. |
| `nodes` | [[AssetType](#assettype)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### asset (query)

Retrieves an asset by its ID.

```graphql
asset(id: ID!): Asset
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The ID of the asset to retrieve. |

**Output types:**

<details>

<summary>Asset</summary>

A physical or logical asset being tracked.

**Implements:** [Node](../common.md#node), [Titled](../common.md#titled), [Customizable](../common.md#customizable), [Versioned](../common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this asset. |
| `type` | [AssetType](#assettype)! | The asset type classification. |
| `customFields` | [[CustomFieldValue](../custom-fields.md#customfieldvalue)!]! | Typed custom field values, one entry per set field code. Each element is a concrete `CustomFieldValue` implementation matching the field's `FieldType` — select fields via inline fragments. The system-reserved code `geojson_data` is excluded — it is exposed through `GeoObject.geojsonData` instead. |
| `primaryDevice` | [Device](../devices/README.md#device) | The primary device (isPrimary=true among DEVICE-type custom fields). |
| `groups` | [AssetGroupConnection](groups.md#assetgroupconnection)! | The groups this asset belongs to. |

</details>

---

### assets

Lists assets for a workspace.

```graphql
assets(
    workspaceId: ID!
    filter: AssetFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: AssetOrder = { field: TITLE, direction: ASC }
  ): AssetConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve assets for. |
| `filter` | `AssetFilter` | Filtering options for the returned assets. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `AssetOrder` | The ordering options for the returned assets. |

**Input types:**

<details>

<summary>AssetFilter</summary>

Filtering options for assets.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `typeIds` | `[ID!]` | Filter by asset types (OR within field). |
| `deviceIds` | `[ID!]` | Filter by linked devices (OR within field). |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `customFields` | [[CustomFieldFilter](../custom-fields.md#customfieldfilter)!] | Filter by custom field values. Unlike the ID list filters above, the conditions in this list combine with AND: an asset must satisfy every one of them. See `CustomFieldFilter` for how a single condition matches. |

</details>

<details>

<summary>CustomFieldFilter</summary>

A filter condition for a custom field value.

One condition is one code compared one way. Where a filter input takes a LIST of these
(`AssetFilter.customFields`, `GeoObjectFilter.customFields`), the entries combine with AND —
an entity must satisfy every one of them, and repeating a code narrows rather than widens.

Inside a single condition:
- `IN` matches when the stored value equals ANY member of the list — the list itself is an OR.
- On a multi-value field (`isMulti` OPTIONS / REFERENCE), a positive operator matches when ANY
  stored value satisfies it: `["red","blue"]` matches `{operator: EQ, value: {string: "red"}}`.
  `NE` and `IS_NULL` are the inversions of that, so they match only when NO stored value
  qualifies — `NE "red"` does not match `["red","blue"]`.
- A code with no field definition behind it is treated as SQL NULL: `IS_NULL` and `NE` match
  every entity, every other operator matches none.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../common.md#code)! | The custom field code to filter by. |
| `operator` | [FieldOperator](../custom-fields.md#fieldoperator)! | The comparison operator. |
| `value` | [CustomFieldFilterValue](../custom-fields.md#customfieldfiltervalue) | The value to compare against. Null for `IS_NULL` and `IS_NOT_NULL` operators. |

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
| `decimal` | [Decimal](../common.md#decimal) | Arbitrary-precision decimal value — for DECIMAL fields. |
| `integer` | [Long](../common.md#long) | Signed 64-bit integer value — for INTEGER fields. |
| `boolean` | `Boolean` | Boolean value — for BOOLEAN fields. |
| `date` | [Date](../common.md#date) | Date value — for DATE fields. |
| `datetime` | [DateTime](../common.md#datetime) | Date-time value — for DATETIME fields. |
| `id` | `ID` | ID value — for DEVICE, REFERENCE fields. |
| `stringList` | `[String!]` | List of strings — for IN operator on string-based fields. |
| `idList` | `[ID!]` | List of IDs — for IN operator on reference fields. |

</details>

<details>

<summary>AssetOrder</summary>

Ordering options for assets.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AssetOrderField](#assetorderfield) | The standard field to order by. Mutually exclusive with `customFieldCode`. |
| `customFieldCode` | [Code](../common.md#code) | The custom field code to order by. Mutually exclusive with `field`. Supported field types: STRING, DECIMAL, INTEGER, DATE, DATETIME, and the reference types DEVICE and single-value REFERENCE, which sort by the title of the entity they point at rather than by the stored id. TEXT, OPTIONS, BOOLEAN and GEOJSON are not supported for sorting, nor is a multi-value REFERENCE or a REFERENCE at an entity with no title. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>AssetConnection</summary>

A paginated list of Asset items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AssetEdge](#assetedge)!]! | A list of edges. |
| `nodes` | [[Asset](#asset)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Mutations

### assetCreate

Creates a new asset.

```graphql
assetCreate(
    input: AssetCreateInput!
  ): AssetPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `AssetCreateInput!` | The input fields for creating the asset. |

**Input types:**

<details>

<summary>AssetCreateInput</summary>

Input for creating a new asset.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the asset. |
| `typeId` | `ID!` | The asset type ID. |
| `title` | `String!` | The asset display name. |
| `customFields` | [CustomFieldsPatchInput](../custom-fields.md#customfieldspatchinput) | The custom field values. |

</details>

<details>

<summary>CustomFieldsPatchInput</summary>

Input for updating custom field values using a patch model.

`set` provides typed values (create/overwrite); `unset` removes fields by code. Primary
status travels with the value on the primary-capable variants (DEVICE/REFERENCE)
via their required `isPrimary` flag — there is no separate setPrimary/unsetPrimary list.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `set` | [[CustomFieldValueInput](../custom-fields.md#customfieldvalueinput)!] | Field values to set or overwrite. |
| `unset` | [[Code](../common.md#code)!] | Field codes to remove entirely. |

</details>

<details>

<summary>CustomFieldValueInput</summary>

A single custom-field value to set, addressed by code.

Omitting `value` is the same as passing null, so an entry clears the field unless it carries one.
An `isDefault` override cannot be changed on its own: send the current value alongside it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../common.md#code)! | The custom field code. |
| `value` | [CustomFieldValueDataInput](../custom-fields.md#customfieldvaluedatainput) | The typed value. Null clears the field but keeps the key (distinct from `unset`). |
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
| `decimal` | [Decimal](../common.md#decimal) | DECIMAL value (arbitrary precision, string-encoded). |
| `integer` | [Long](../common.md#long) | INTEGER value (signed 64-bit). |
| `boolean` | `Boolean` | BOOLEAN value. |
| `date` | [Date](../common.md#date) | DATE value. |
| `datetime` | [DateTime](../common.md#datetime) | DATETIME value. |
| `geojson` | [GeoJSON](../common.md#geojson) | GEOJSON value. |
| `device` | [DeviceValueInput](../custom-fields.md#devicevalueinput) | DEVICE value (primary-capable). |
| `reference` | [ReferenceValueInput](../custom-fields.md#referencevalueinput) | REFERENCE value for a single-value field (primary-capable). |
| `references` | [ReferenceListValueInput](../custom-fields.md#referencelistvalueinput) | REFERENCE value for an isMulti field (primary-capable). |
| `option` | [Code](../common.md#code) | OPTIONS value for a single-value field. |
| `options` | [[Code](../common.md#code)!] | OPTIONS value for an isMulti field. |

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

<summary>AssetPayload</summary>

The result of an asset mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `asset` | [Asset](#asset)! | The created or updated asset. |

</details>

<details>

<summary>Asset (entity)</summary>

A physical or logical asset being tracked.

**Implements:** [Node](../common.md#node), [Titled](../common.md#titled), [Customizable](../common.md#customizable), [Versioned](../common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this asset. |
| `type` | [AssetType](#assettype)! | The asset type classification. |
| `customFields` | [[CustomFieldValue](../custom-fields.md#customfieldvalue)!]! | Typed custom field values, one entry per set field code. Each element is a concrete `CustomFieldValue` implementation matching the field's `FieldType` — select fields via inline fragments. The system-reserved code `geojson_data` is excluded — it is exposed through `GeoObject.geojsonData` instead. |
| `primaryDevice` | [Device](../devices/README.md#device) | The primary device (isPrimary=true among DEVICE-type custom fields). |
| `groups` | [AssetGroupConnection](groups.md#assetgroupconnection)! | The groups this asset belongs to. |

</details>

---

### assetUpdate

Updates an existing asset.

```graphql
assetUpdate(
    input: AssetUpdateInput!
  ): AssetPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `AssetUpdateInput!` | The input fields for updating the asset. |

**Input types:**

<details>

<summary>AssetUpdateInput</summary>

Input for updating an existing asset.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The asset ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `customFields` | [CustomFieldsPatchInput](../custom-fields.md#customfieldspatchinput) | The custom field changes. |

</details>

<details>

<summary>CustomFieldsPatchInput</summary>

Input for updating custom field values using a patch model.

`set` provides typed values (create/overwrite); `unset` removes fields by code. Primary
status travels with the value on the primary-capable variants (DEVICE/REFERENCE)
via their required `isPrimary` flag — there is no separate setPrimary/unsetPrimary list.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `set` | [[CustomFieldValueInput](../custom-fields.md#customfieldvalueinput)!] | Field values to set or overwrite. |
| `unset` | [[Code](../common.md#code)!] | Field codes to remove entirely. |

</details>

<details>

<summary>CustomFieldValueInput</summary>

A single custom-field value to set, addressed by code.

Omitting `value` is the same as passing null, so an entry clears the field unless it carries one.
An `isDefault` override cannot be changed on its own: send the current value alongside it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../common.md#code)! | The custom field code. |
| `value` | [CustomFieldValueDataInput](../custom-fields.md#customfieldvaluedatainput) | The typed value. Null clears the field but keeps the key (distinct from `unset`). |
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
| `decimal` | [Decimal](../common.md#decimal) | DECIMAL value (arbitrary precision, string-encoded). |
| `integer` | [Long](../common.md#long) | INTEGER value (signed 64-bit). |
| `boolean` | `Boolean` | BOOLEAN value. |
| `date` | [Date](../common.md#date) | DATE value. |
| `datetime` | [DateTime](../common.md#datetime) | DATETIME value. |
| `geojson` | [GeoJSON](../common.md#geojson) | GEOJSON value. |
| `device` | [DeviceValueInput](../custom-fields.md#devicevalueinput) | DEVICE value (primary-capable). |
| `reference` | [ReferenceValueInput](../custom-fields.md#referencevalueinput) | REFERENCE value for a single-value field (primary-capable). |
| `references` | [ReferenceListValueInput](../custom-fields.md#referencelistvalueinput) | REFERENCE value for an isMulti field (primary-capable). |
| `option` | [Code](../common.md#code) | OPTIONS value for a single-value field. |
| `options` | [[Code](../common.md#code)!] | OPTIONS value for an isMulti field. |

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

<summary>AssetPayload</summary>

The result of an asset mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `asset` | [Asset](#asset)! | The created or updated asset. |

</details>

<details>

<summary>Asset (entity)</summary>

A physical or logical asset being tracked.

**Implements:** [Node](../common.md#node), [Titled](../common.md#titled), [Customizable](../common.md#customizable), [Versioned](../common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this asset. |
| `type` | [AssetType](#assettype)! | The asset type classification. |
| `customFields` | [[CustomFieldValue](../custom-fields.md#customfieldvalue)!]! | Typed custom field values, one entry per set field code. Each element is a concrete `CustomFieldValue` implementation matching the field's `FieldType` — select fields via inline fragments. The system-reserved code `geojson_data` is excluded — it is exposed through `GeoObject.geojsonData` instead. |
| `primaryDevice` | [Device](../devices/README.md#device) | The primary device (isPrimary=true among DEVICE-type custom fields). |
| `groups` | [AssetGroupConnection](groups.md#assetgroupconnection)! | The groups this asset belongs to. |

</details>

---

### assetDelete

Deletes an asset.

```graphql
assetDelete(
    input: AssetDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `AssetDeleteInput!` | The input fields for deleting the asset. |

**Input types:**

<details>

<summary>AssetDeleteInput</summary>

Input for deleting an asset.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The asset ID to delete. |
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

### assetTypeCreate

Creates a new asset type.

```graphql
assetTypeCreate(
    input: AssetTypeCreateInput!
  ): AssetTypePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `AssetTypeCreateInput!` | The input fields for creating the asset type. |

**Input types:**

<details>

<summary>AssetTypeCreateInput</summary>

Input for creating an asset type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the item. |
| `code` | [Code](../common.md#code) | The machine-readable code. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinitionInput](../custom-fields.md#customfielddefinitioninput)!] | Operations on custom field definitions for this asset type. Only `create` is allowed when creating a new catalog item. |

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
| `create` | [CustomFieldDefinitionCreateData](../custom-fields.md#customfielddefinitioncreatedata) | Create a new custom field definition. |
| `update` | [CustomFieldDefinitionUpdateData](../custom-fields.md#customfielddefinitionupdatedata) | Update an existing custom field definition. |
| `delete` | [CustomFieldDefinitionDeleteData](../custom-fields.md#customfielddefinitiondeletedata) | Delete a custom field definition. |
| `archive` | [CustomFieldDefinitionArchiveData](../custom-fields.md#customfielddefinitionarchivedata) | Archive a custom field definition (non-destructive deactivation). |
| `restore` | [CustomFieldDefinitionRestoreData](../custom-fields.md#customfielddefinitionrestoredata) | Restore a previously archived custom field definition. |

</details>

<details>

<summary>CustomFieldDefinitionCreateData</summary>

Data for creating a custom field definition within its parent catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../common.md#code) | The machine-readable code. Must start with `cf_`, so a custom-field code can never be confused with an entity's own field name. Auto-generated from the title if omitted, prefix included. The prefix also keeps this input clear of the platform's own reserved code `geojson_data`. |
| `title` | `String!` | The display name. |
| `description` | `String` | The description. |
| `fieldType` | [FieldType](../custom-fields.md#fieldtype)! | The data type. Immutable after creation. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `params` | [FieldParamsInput](../custom-fields.md#fieldparamsinput)! | The type-specific parameters. Exactly one variant must be provided. |
| `isDefault` | `Boolean` | Whether the field is on by default for entities of this type. Defaults to false. |

</details>

<details>

<summary>FieldParamsInput</summary>

Field parameters input. Exactly one field must be provided.

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `string` | [StringFieldParamsInput](../custom-fields.md#stringfieldparamsinput) | Parameters for STRING field type. |
| `text` | [TextFieldParamsInput](../custom-fields.md#textfieldparamsinput) | Parameters for TEXT field type. |
| `decimal` | [DecimalFieldParamsInput](../custom-fields.md#decimalfieldparamsinput) | Parameters for DECIMAL field type. |
| `integer` | [IntegerFieldParamsInput](../custom-fields.md#integerfieldparamsinput) | Parameters for INTEGER field type. |
| `boolean` | [BooleanFieldParamsInput](../custom-fields.md#booleanfieldparamsinput) | Parameters for BOOLEAN field type. |
| `date` | [DateFieldParamsInput](../custom-fields.md#datefieldparamsinput) | Parameters for DATE field type. |
| `datetime` | [DateTimeFieldParamsInput](../custom-fields.md#datetimefieldparamsinput) | Parameters for DATETIME field type. |
| `geojson` | [GeoJsonFieldParamsInput](../custom-fields.md#geojsonfieldparamsinput) | Parameters for GEOJSON field type. |
| `options` | [OptionsFieldParamsInput](../custom-fields.md#optionsfieldparamsinput) | Parameters for OPTIONS field type. |
| `device` | [DeviceFieldParamsInput](../custom-fields.md#devicefieldparamsinput) | Parameters for DEVICE field type. |
| `reference` | [ReferenceFieldParamsInput](../custom-fields.md#referencefieldparamsinput) | Parameters for REFERENCE field type. |

</details>

<details>

<summary>StringFieldParamsInput</summary>

Parameters for STRING field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minLength` | `Int` | The minimum character length. |
| `maxLength` | `Int` | The maximum character length. Narrows the `FieldType.STRING` limit; it cannot raise it. |
| `defaultString` | `String` | The default value. |
| `trim` | `Boolean` | Whether to trim whitespace. |

</details>

<details>

<summary>TextFieldParamsInput</summary>

Parameters for TEXT field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `maxLength` | `Int` | The maximum character length. Narrows the `FieldType.TEXT` limit of 65,535; it cannot raise it. |
| `defaultText` | `String` | The default value. |
| `trim` | `Boolean` | Whether to trim whitespace. |

</details>

<details>

<summary>DecimalFieldParamsInput</summary>

Parameters for DECIMAL field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minDecimal` | [Decimal](../common.md#decimal) | The minimum allowed value. |
| `maxDecimal` | [Decimal](../common.md#decimal) | The maximum allowed value. |
| `scale` | `Int!` | Digits after the decimal point. Must be >= 0. Values sent with more fractional digits than `scale` are rounded using HALF_UP before storage. |
| `defaultDecimal` | [Decimal](../common.md#decimal) | The default value. |

</details>

<details>

<summary>IntegerFieldParamsInput</summary>

Parameters for INTEGER field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minInteger` | [Long](../common.md#long) | The minimum allowed value. |
| `maxInteger` | [Long](../common.md#long) | The maximum allowed value. |
| `defaultInteger` | [Long](../common.md#long) | The default value. |

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
| `defaultDate` | [Date](../common.md#date) | The default value. |

</details>

<details>

<summary>DateTimeFieldParamsInput</summary>

Parameters for DATETIME field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `defaultDatetime` | [DateTime](../common.md#datetime) | The default value. |

</details>

<details>

<summary>GeoJsonFieldParamsInput</summary>

Parameters for GEOJSON field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `allowedTypes` | [[GeoJsonGeometryType](../geo-objects.md#geojsongeometrytype)!] | The allowed geometry types. Null means all types are allowed. |

</details>

<details>

<summary>OptionsFieldParamsInput</summary>

Parameters for OPTIONS field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `isMulti` | `Boolean` | Whether multiple options can be selected. |
| `options` | [[FieldOptionInput](../custom-fields.md#fieldoptioninput)!]! | The available options. |
| `defaultOptions` | [Code](../common.md#code) | The default option code. |

</details>

<details>

<summary>FieldOptionInput</summary>

Input for an option definition.
When updating options: if an entry without `code` is provided, a new option is created.
If the label already exists within this field, an error is returned.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../common.md#code) | The unique code. Auto-generated from label if omitted. |
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
| `refEntityTypeCode` | [Code](../common.md#code)! | The target entity type code, e.g. `asset`, `geo_object`, `schedule`, `tag`, `user_catalog_item`. The `customFieldTypes` query lists the accepted values for a given owner. |
| `refSubtypeIds` | `[ID!]` | The subtype IDs a value may belong to (AssetType / GeoObjectType / catalog). Required for `user_catalog_item`, which needs exactly one catalog; rejected for target types that nothing narrows further. Omit or leave empty to allow the whole target type. |
| `defaultRefIds` | `[ID!]` | The default referenced entity IDs. |

</details>

<details>

<summary>CustomFieldDefinitionUpdateData</summary>

Data for updating an existing custom field definition. Note: `fieldType` cannot be changed.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../common.md#code)! | The code of the field definition to update. |
| `title` | `String` | The new display name. |
| `description` | `String` | The new description. |
| `order` | `Int` | The new display order. |
| `params` | [FieldParamsInput](../custom-fields.md#fieldparamsinput) | The updated parameters. Only `isRequired` and type-specific fields can be changed. |
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
| `code` | [Code](../common.md#code)! | The code of the field definition to delete. |
| `onValues` | [CustomFieldDefinitionDeleteBehavior](../custom-fields.md#customfielddefinitiondeletebehavior) | What to do when existing entities have values for this field. Defaults to `REJECT` to prevent accidental data loss. |

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
| `code` | [Code](../common.md#code)! | The code of the field definition to archive. |

</details>

<details>

<summary>CustomFieldDefinitionRestoreData</summary>

Data for restoring a previously archived custom field definition.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../common.md#code)! | The code of the field definition to restore. |

</details>

**Output types:**

<details>

<summary>AssetTypePayload</summary>

The result of an asset type mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetType` | [AssetType](#assettype)! | The created or updated asset type. |

</details>

<details>

<summary>AssetType (entity)</summary>

A classification type for assets.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinition](../custom-fields.md#customfielddefinition)!]! | Custom field definitions specific to this asset type, ordered by display order. |

</details>

---

### assetTypeUpdate

Updates an asset type.

```graphql
assetTypeUpdate(
    input: AssetTypeUpdateInput!
  ): AssetTypePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `AssetTypeUpdateInput!` | The input fields for updating the asset type. |

**Input types:**

<details>

<summary>AssetTypeUpdateInput</summary>

Input for updating an asset type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinitionInput](../custom-fields.md#customfielddefinitioninput)!] | Operations on custom field definitions belonging to this asset type. |

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
| `create` | [CustomFieldDefinitionCreateData](../custom-fields.md#customfielddefinitioncreatedata) | Create a new custom field definition. |
| `update` | [CustomFieldDefinitionUpdateData](../custom-fields.md#customfielddefinitionupdatedata) | Update an existing custom field definition. |
| `delete` | [CustomFieldDefinitionDeleteData](../custom-fields.md#customfielddefinitiondeletedata) | Delete a custom field definition. |
| `archive` | [CustomFieldDefinitionArchiveData](../custom-fields.md#customfielddefinitionarchivedata) | Archive a custom field definition (non-destructive deactivation). |
| `restore` | [CustomFieldDefinitionRestoreData](../custom-fields.md#customfielddefinitionrestoredata) | Restore a previously archived custom field definition. |

</details>

<details>

<summary>CustomFieldDefinitionCreateData</summary>

Data for creating a custom field definition within its parent catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../common.md#code) | The machine-readable code. Must start with `cf_`, so a custom-field code can never be confused with an entity's own field name. Auto-generated from the title if omitted, prefix included. The prefix also keeps this input clear of the platform's own reserved code `geojson_data`. |
| `title` | `String!` | The display name. |
| `description` | `String` | The description. |
| `fieldType` | [FieldType](../custom-fields.md#fieldtype)! | The data type. Immutable after creation. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `params` | [FieldParamsInput](../custom-fields.md#fieldparamsinput)! | The type-specific parameters. Exactly one variant must be provided. |
| `isDefault` | `Boolean` | Whether the field is on by default for entities of this type. Defaults to false. |

</details>

<details>

<summary>FieldParamsInput</summary>

Field parameters input. Exactly one field must be provided.

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `string` | [StringFieldParamsInput](../custom-fields.md#stringfieldparamsinput) | Parameters for STRING field type. |
| `text` | [TextFieldParamsInput](../custom-fields.md#textfieldparamsinput) | Parameters for TEXT field type. |
| `decimal` | [DecimalFieldParamsInput](../custom-fields.md#decimalfieldparamsinput) | Parameters for DECIMAL field type. |
| `integer` | [IntegerFieldParamsInput](../custom-fields.md#integerfieldparamsinput) | Parameters for INTEGER field type. |
| `boolean` | [BooleanFieldParamsInput](../custom-fields.md#booleanfieldparamsinput) | Parameters for BOOLEAN field type. |
| `date` | [DateFieldParamsInput](../custom-fields.md#datefieldparamsinput) | Parameters for DATE field type. |
| `datetime` | [DateTimeFieldParamsInput](../custom-fields.md#datetimefieldparamsinput) | Parameters for DATETIME field type. |
| `geojson` | [GeoJsonFieldParamsInput](../custom-fields.md#geojsonfieldparamsinput) | Parameters for GEOJSON field type. |
| `options` | [OptionsFieldParamsInput](../custom-fields.md#optionsfieldparamsinput) | Parameters for OPTIONS field type. |
| `device` | [DeviceFieldParamsInput](../custom-fields.md#devicefieldparamsinput) | Parameters for DEVICE field type. |
| `reference` | [ReferenceFieldParamsInput](../custom-fields.md#referencefieldparamsinput) | Parameters for REFERENCE field type. |

</details>

<details>

<summary>StringFieldParamsInput</summary>

Parameters for STRING field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minLength` | `Int` | The minimum character length. |
| `maxLength` | `Int` | The maximum character length. Narrows the `FieldType.STRING` limit; it cannot raise it. |
| `defaultString` | `String` | The default value. |
| `trim` | `Boolean` | Whether to trim whitespace. |

</details>

<details>

<summary>TextFieldParamsInput</summary>

Parameters for TEXT field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `maxLength` | `Int` | The maximum character length. Narrows the `FieldType.TEXT` limit of 65,535; it cannot raise it. |
| `defaultText` | `String` | The default value. |
| `trim` | `Boolean` | Whether to trim whitespace. |

</details>

<details>

<summary>DecimalFieldParamsInput</summary>

Parameters for DECIMAL field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minDecimal` | [Decimal](../common.md#decimal) | The minimum allowed value. |
| `maxDecimal` | [Decimal](../common.md#decimal) | The maximum allowed value. |
| `scale` | `Int!` | Digits after the decimal point. Must be >= 0. Values sent with more fractional digits than `scale` are rounded using HALF_UP before storage. |
| `defaultDecimal` | [Decimal](../common.md#decimal) | The default value. |

</details>

<details>

<summary>IntegerFieldParamsInput</summary>

Parameters for INTEGER field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minInteger` | [Long](../common.md#long) | The minimum allowed value. |
| `maxInteger` | [Long](../common.md#long) | The maximum allowed value. |
| `defaultInteger` | [Long](../common.md#long) | The default value. |

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
| `defaultDate` | [Date](../common.md#date) | The default value. |

</details>

<details>

<summary>DateTimeFieldParamsInput</summary>

Parameters for DATETIME field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `defaultDatetime` | [DateTime](../common.md#datetime) | The default value. |

</details>

<details>

<summary>GeoJsonFieldParamsInput</summary>

Parameters for GEOJSON field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `allowedTypes` | [[GeoJsonGeometryType](../geo-objects.md#geojsongeometrytype)!] | The allowed geometry types. Null means all types are allowed. |

</details>

<details>

<summary>OptionsFieldParamsInput</summary>

Parameters for OPTIONS field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `isMulti` | `Boolean` | Whether multiple options can be selected. |
| `options` | [[FieldOptionInput](../custom-fields.md#fieldoptioninput)!]! | The available options. |
| `defaultOptions` | [Code](../common.md#code) | The default option code. |

</details>

<details>

<summary>FieldOptionInput</summary>

Input for an option definition.
When updating options: if an entry without `code` is provided, a new option is created.
If the label already exists within this field, an error is returned.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../common.md#code) | The unique code. Auto-generated from label if omitted. |
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
| `refEntityTypeCode` | [Code](../common.md#code)! | The target entity type code, e.g. `asset`, `geo_object`, `schedule`, `tag`, `user_catalog_item`. The `customFieldTypes` query lists the accepted values for a given owner. |
| `refSubtypeIds` | `[ID!]` | The subtype IDs a value may belong to (AssetType / GeoObjectType / catalog). Required for `user_catalog_item`, which needs exactly one catalog; rejected for target types that nothing narrows further. Omit or leave empty to allow the whole target type. |
| `defaultRefIds` | `[ID!]` | The default referenced entity IDs. |

</details>

<details>

<summary>CustomFieldDefinitionUpdateData</summary>

Data for updating an existing custom field definition. Note: `fieldType` cannot be changed.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../common.md#code)! | The code of the field definition to update. |
| `title` | `String` | The new display name. |
| `description` | `String` | The new description. |
| `order` | `Int` | The new display order. |
| `params` | [FieldParamsInput](../custom-fields.md#fieldparamsinput) | The updated parameters. Only `isRequired` and type-specific fields can be changed. |
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
| `code` | [Code](../common.md#code)! | The code of the field definition to delete. |
| `onValues` | [CustomFieldDefinitionDeleteBehavior](../custom-fields.md#customfielddefinitiondeletebehavior) | What to do when existing entities have values for this field. Defaults to `REJECT` to prevent accidental data loss. |

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
| `code` | [Code](../common.md#code)! | The code of the field definition to archive. |

</details>

<details>

<summary>CustomFieldDefinitionRestoreData</summary>

Data for restoring a previously archived custom field definition.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../common.md#code)! | The code of the field definition to restore. |

</details>

**Output types:**

<details>

<summary>AssetTypePayload</summary>

The result of an asset type mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetType` | [AssetType](#assettype)! | The created or updated asset type. |

</details>

<details>

<summary>AssetType (entity)</summary>

A classification type for assets.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinition](../custom-fields.md#customfielddefinition)!]! | Custom field definitions specific to this asset type, ordered by display order. |

</details>

---

### assetTypeDelete

Deletes an asset type.

```graphql
assetTypeDelete(
    input: CatalogItemDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `CatalogItemDeleteInput!` | The input fields for deleting the asset type. |

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

<a id="assettype"></a>

### AssetType

A classification type for assets.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinition](../custom-fields.md#customfielddefinition)!]! | Custom field definitions specific to this asset type, ordered by display order. |

---

<a id="asset"></a>

### Asset

A physical or logical asset being tracked.

**Implements:** [Node](../common.md#node), [Titled](../common.md#titled), [Customizable](../common.md#customizable), [Versioned](../common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this asset. |
| `type` | [AssetType](#assettype)! | The asset type classification. |
| `customFields` | [[CustomFieldValue](../custom-fields.md#customfieldvalue)!]! | Typed custom field values, one entry per set field code. Each element is a concrete `CustomFieldValue` implementation matching the field's `FieldType` — select fields via inline fragments. The system-reserved code `geojson_data` is excluded — it is exposed through `GeoObject.geojsonData` instead. |
| `primaryDevice` | [Device](../devices/README.md#device) | The primary device (isPrimary=true among DEVICE-type custom fields). |
| `groups` | [AssetGroupConnection](groups.md#assetgroupconnection)! | The groups this asset belongs to. |

---

<a id="assetpayload"></a>

### AssetPayload

The result of an asset mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `asset` | [Asset](#asset)! | The created or updated asset. |

---

<a id="assettypepayload"></a>

### AssetTypePayload

The result of an asset type mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetType` | [AssetType](#assettype)! | The created or updated asset type. |

---

## Inputs

<a id="assetfilter"></a>

### AssetFilter

Filtering options for assets.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `typeIds` | `[ID!]` | Filter by asset types (OR within field). |
| `deviceIds` | `[ID!]` | Filter by linked devices (OR within field). |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `customFields` | [[CustomFieldFilter](../custom-fields.md#customfieldfilter)!] | Filter by custom field values. Unlike the ID list filters above, the conditions in this list combine with AND: an asset must satisfy every one of them. See `CustomFieldFilter` for how a single condition matches. |

---

<a id="assetorder"></a>

### AssetOrder

Ordering options for assets.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AssetOrderField](#assetorderfield) | The standard field to order by. Mutually exclusive with `customFieldCode`. |
| `customFieldCode` | [Code](../common.md#code) | The custom field code to order by. Mutually exclusive with `field`. Supported field types: STRING, DECIMAL, INTEGER, DATE, DATETIME, and the reference types DEVICE and single-value REFERENCE, which sort by the title of the entity they point at rather than by the stored id. TEXT, OPTIONS, BOOLEAN and GEOJSON are not supported for sorting, nor is a multi-value REFERENCE or a REFERENCE at an entity with no title. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

---

<a id="assetcreateinput"></a>

### AssetCreateInput

Input for creating a new asset.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the asset. |
| `typeId` | `ID!` | The asset type ID. |
| `title` | `String!` | The asset display name. |
| `customFields` | [CustomFieldsPatchInput](../custom-fields.md#customfieldspatchinput) | The custom field values. |

---

<a id="assetupdateinput"></a>

### AssetUpdateInput

Input for updating an existing asset.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The asset ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `customFields` | [CustomFieldsPatchInput](../custom-fields.md#customfieldspatchinput) | The custom field changes. |

---

<a id="assetdeleteinput"></a>

### AssetDeleteInput

Input for deleting an asset.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The asset ID to delete. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

---

<a id="assettypecreateinput"></a>

### AssetTypeCreateInput

Input for creating an asset type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the item. |
| `code` | [Code](../common.md#code) | The machine-readable code. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinitionInput](../custom-fields.md#customfielddefinitioninput)!] | Operations on custom field definitions for this asset type. Only `create` is allowed when creating a new catalog item. |

---

<a id="assettypeupdateinput"></a>

### AssetTypeUpdateInput

Input for updating an asset type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |
| `customFieldDefinitions` | [[CustomFieldDefinitionInput](../custom-fields.md#customfielddefinitioninput)!] | Operations on custom field definitions belonging to this asset type. |

---

## Enums

<a id="assetorderfield"></a>

### AssetOrderField

Fields available for ordering assets.

| Value | Description |
| ----- | ----------- |
| `TITLE` | Order by title. |

---

## Pagination types

<a id="assetconnection"></a>

### AssetConnection

A paginated list of Asset items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AssetEdge](#assetedge)!]! | A list of edges. |
| `nodes` | [[Asset](#asset)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="assetedge"></a>

### AssetEdge

An edge in the Asset connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [Asset](#asset)! | The asset at the end of the edge. |

---

<a id="assettypeconnection"></a>

### AssetTypeConnection

A paginated list of AssetType items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AssetTypeEdge](#assettypeedge)!]! | A list of edges. |
| `nodes` | [[AssetType](#assettype)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="assettypeedge"></a>

### AssetTypeEdge

An edge in the AssetType connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [AssetType](#assettype)! | The asset type at the end of the edge. |

---
