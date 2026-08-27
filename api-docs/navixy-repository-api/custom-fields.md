---
description: >-
  Type definitions for custom field definitions, including field types, params,
  options, filter inputs, and the patch input used in asset and geo object
  mutations.
---

# Custom fields

{% include ".gitbook/includes/navixy-repository-api-is-a-....md" %}

Custom field definitions allow extending entities with workspace-specific data fields.

## Queries

### customFieldTypes

Lists the custom-field types that may be defined on the given owner catalog item,
  together with what a `REFERENCE` field defined there may point at. Creating a definition
  with a type absent from this list fails with `VALIDATION_ERROR` on `input.fieldType`.

  Returns an empty list for any catalog item whose instances cannot carry custom fields.
  The owners that can are the ones whose instances implement `Customizable` — `AssetType`
  and `GeoObjectType`.

```graphql
customFieldTypes(ownerTypeId: ID!): [FieldTypeAvailability!]!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `ownerTypeId` | `ID!` | The owner catalog item — the type the fields would be defined on. |

**Output types:**

<details>

<summary>FieldTypeAvailability</summary>

One custom-field type that may be defined on a given owner, and what it may point at.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `fieldType` | [FieldType](#fieldtype)! | The field type. |
| `referenceableEntityTypes` | [[Code](common.md#code)!]! | Entity type codes a `REFERENCE` field on this owner may point at, as accepted by `ReferenceFieldParamsInput.refEntityTypeCode`. Empty for every other field type. |

</details>

---

## Objects

<a id="stringcustomfieldvalue"></a>

### StringCustomFieldValue

STRING / TEXT custom-field value.

**Implements:** [CustomFieldValue](#customfieldvalue)

---

<a id="decimalcustomfieldvalue"></a>

### DecimalCustomFieldValue

DECIMAL custom-field value (arbitrary precision, string-encoded).

**Implements:** [CustomFieldValue](#customfieldvalue)

---

<a id="integercustomfieldvalue"></a>

### IntegerCustomFieldValue

INTEGER custom-field value (signed 64-bit).

**Implements:** [CustomFieldValue](#customfieldvalue)

---

<a id="booleancustomfieldvalue"></a>

### BooleanCustomFieldValue

BOOLEAN custom-field value.

**Implements:** [CustomFieldValue](#customfieldvalue)

---

<a id="datecustomfieldvalue"></a>

### DateCustomFieldValue

DATE custom-field value.

**Implements:** [CustomFieldValue](#customfieldvalue)

---

<a id="datetimecustomfieldvalue"></a>

### DateTimeCustomFieldValue

DATETIME custom-field value.

**Implements:** [CustomFieldValue](#customfieldvalue)

---

<a id="geojsoncustomfieldvalue"></a>

### GeoJsonCustomFieldValue

GEOJSON custom-field value.

**Implements:** [CustomFieldValue](#customfieldvalue)

---

<a id="optionscustomfieldvalue"></a>

### OptionsCustomFieldValue

OPTIONS custom-field value (one or more option codes).

**Implements:** [CustomFieldValue](#customfieldvalue)

---

<a id="referencecustomfieldvalue"></a>

### ReferenceCustomFieldValue

REFERENCE custom-field value (one or more referenced entities; primary-capable).

**Implements:** [CustomFieldValue](#customfieldvalue)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The custom field code. |
| `isDefault` | `Boolean!` | Whether this field is on by default for this entity. |
| `refs` | [[Node](common.md#node)]! | The referenced entities, in stored order. Select concrete fields with inline fragments. A single-value field returns a one-element list. An element is null when its target no longer resolves — the referenced entity was deleted while the reference stayed behind. The slot is kept so stored order still lines up with the other elements. Nullable on purpose: with a non-null element type one deleted target would null the whole holder, and inside a connection the whole page of results with it. |
| `isPrimary` | `Boolean!` | Whether this reference is the entity's primary REFERENCE. |

---

<a id="customfielddefinition"></a>

### CustomFieldDefinition

A custom field definition that specifies the metadata for a custom field.

{% hint style="warning" %}
The `fieldType` property is immutable after creation. To change the field type, delete the definition and create a new one.
{% endhint %}

Titled but not a `Node`: a definition is embedded in its owning catalog item and addressed
through it, never by an id of its own. Within its owner it is identified by `code`.

**Implements:** [Titled](common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `title` | `String!` | The human-readable display name. |
| `code` | [Code](common.md#code)! | The machine-readable code, unique per owner and workspace. Always starts with `cf_`. |
| `description` | `String` | A description of the field for UI hints. |
| `order` | `Int!` | The display order within the owner context. |
| `fieldType` | [FieldType](#fieldtype)! | The data type determining validation rules and UI rendering. This property is immutable and cannot be changed after creation. |
| `params` | [FieldParams](#fieldparams)! | The type-specific parameters for validation, defaults, and options. |
| `isDefault` | `Boolean!` | Whether the field is on by default for every entity of the owning type. An individual entity can override it on its value. |
| `isArchived` | `Boolean!` | Whether this field definition is archived. Archived fields preserve existing values but no longer appear in forms and accept no new input. Use the `archive` / `restore` actions in the parent catalog item's `customFieldDefinitions` input to toggle this state. |

---

<a id="fieldtypeavailability"></a>

### FieldTypeAvailability

One custom-field type that may be defined on a given owner, and what it may point at.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `fieldType` | [FieldType](#fieldtype)! | The field type. |
| `referenceableEntityTypes` | [[Code](common.md#code)!]! | Entity type codes a `REFERENCE` field on this owner may point at, as accepted by `ReferenceFieldParamsInput.refEntityTypeCode`. Empty for every other field type. |

---

<a id="fieldparamsstring"></a>

### FieldParamsString

Parameters for STRING field type.

**Implements:** [FieldParams](#fieldparams)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required for this field. |
| `minLength` | `Int` | The minimum character length. |
| `maxLength` | `Int` | The maximum character length. |
| `defaultString` | `String` | The default value. |
| `trim` | `Boolean!` | Whether to trim leading and trailing whitespace. |

---

<a id="fieldparamstext"></a>

### FieldParamsText

Parameters for TEXT field type.

**Implements:** [FieldParams](#fieldparams)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required for this field. |
| `maxLength` | `Int` | The maximum character length. |
| `defaultText` | `String` | The default value. |
| `trim` | `Boolean!` | Whether to trim leading and trailing whitespace. |

---

<a id="fieldparamsdecimal"></a>

### FieldParamsDecimal

Parameters for DECIMAL field type.

**Implements:** [FieldParams](#fieldparams)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required for this field. |
| `minDecimal` | [Decimal](common.md#decimal) | The minimum allowed value. |
| `maxDecimal` | [Decimal](common.md#decimal) | The maximum allowed value. |
| `scale` | `Int!` | Digits after the decimal point. Must be >= 0. Values sent with more fractional digits than `scale` are rounded using HALF_UP before storage. |
| `defaultDecimal` | [Decimal](common.md#decimal) | The default value. |

---

<a id="fieldparamsinteger"></a>

### FieldParamsInteger

Parameters for INTEGER field type.

**Implements:** [FieldParams](#fieldparams)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required for this field. |
| `minInteger` | [Long](common.md#long) | The minimum allowed value. |
| `maxInteger` | [Long](common.md#long) | The maximum allowed value. |
| `defaultInteger` | [Long](common.md#long) | The default value. |

---

<a id="fieldparamsboolean"></a>

### FieldParamsBoolean

Parameters for BOOLEAN field type.

**Implements:** [FieldParams](#fieldparams)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required for this field. |
| `defaultBoolean` | `Boolean` | The default value. |

---

<a id="fieldparamsdate"></a>

### FieldParamsDate

Parameters for DATE field type.

**Implements:** [FieldParams](#fieldparams)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required for this field. |
| `defaultDate` | [Date](common.md#date) | The default value. |

---

<a id="fieldparamsdatetime"></a>

### FieldParamsDatetime

Parameters for DATETIME field type.

**Implements:** [FieldParams](#fieldparams)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required for this field. |
| `defaultDatetime` | [DateTime](common.md#datetime) | The default value. |

---

<a id="fieldparamsgeojson"></a>

### FieldParamsGeojson

Parameters for GEOJSON field type.

**Implements:** [FieldParams](#fieldparams)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required for this field. |
| `allowedTypes` | [[GeoJsonGeometryType](geo-objects.md#geojsongeometrytype)!] | The allowed geometry types. Null means all types are allowed. |

---

<a id="fieldparamsoptions"></a>

### FieldParamsOptions

Parameters for OPTIONS field type.

**Implements:** [FieldParams](#fieldparams), [MultiValue](common.md#multivalue)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required for this field. |
| `isMulti` | `Boolean!` | Whether multiple values can be selected for this field. |
| `options` | [[FieldOption](#fieldoption)!]! | The available options to choose from. |
| `defaultOptions` | [Code](common.md#code) | The default option code. |

---

<a id="fieldoption"></a>

### FieldOption

A single option in an OPTIONS field.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The unique code for this option within the field. |
| `label` | `String!` | The display label. |
| `description` | `String` | A description of the option. |
| `isArchived` | `Boolean!` | Whether this option is archived and should not be shown for new selections. |

---

<a id="devicecustomfieldvalue"></a>

### DeviceCustomFieldValue

DEVICE custom-field value (primary-capable).

**Implements:** [CustomFieldValue](#customfieldvalue)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The custom field code. |
| `isDefault` | `Boolean!` | Whether this field is on by default for this entity. |
| `device` | [Device](devices/README.md#device)! | The linked device. |
| `isPrimary` | `Boolean!` | Whether this device is the entity's primary DEVICE. |

---

<a id="fieldparamsdevice"></a>

### FieldParamsDevice

Parameters for DEVICE field type.

**Implements:** [FieldParams](#fieldparams)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required for this field. |
| `refSubtypes` | [[CatalogItem](catalogs/catalog-items.md#catalogitem)]! | The device types a value may belong to. Empty means any device type is allowed. An element is null when the device type behind it no longer resolves — see `FieldParamsReference.refSubtypes`, which is resolved by the same loader. |

---

<a id="fieldparamsreference"></a>

### FieldParamsReference

Parameters for REFERENCE field type.

**Implements:** [FieldParams](#fieldparams), [MultiValue](common.md#multivalue)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required for this field. |
| `isMulti` | `Boolean!` | Whether multiple values can be selected for this field. |
| `refEntityTypeCode` | [Code](common.md#code)! | The entity type code that can be referenced. |
| `refSubtypes` | [[CatalogItem](catalogs/catalog-items.md#catalogitem)]! | The subtypes a value may belong to (AssetType / GeoObjectType / catalog). Empty means the whole target type is allowed. An element is null when its subtype no longer resolves — the classifier item was deleted after the narrowing was validated on write. Nullable for the same reason as `defaultRefs` below: both are resolved while `FieldParams!` itself is built, so a non-null element type would let one dangling subtype blank an entire page of catalog types, even for a query that never selects this field. |
| `defaultRefs` | [[Node](common.md#node)]! | The default referenced entities. Empty when the field has no default. An element is null when its target no longer resolves — the entity was deleted after the default was validated on write. Nullable for the same reason as `ReferenceCustomFieldValue.refs`: `FieldParams!` is non-null on `CustomFieldDefinition!` inside a non-null `customFieldDefinitions` list, so a non-null element type would let one dangling default blank an entire page of catalog types. |

---

## Inputs

<a id="customfieldfiltervalue"></a>

### CustomFieldFilterValue

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

---

<a id="customfieldfilter"></a>

### CustomFieldFilter

A filter condition for a custom field value.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The custom field code to filter by. |
| `operator` | [FieldOperator](#fieldoperator)! | The comparison operator. |
| `value` | [CustomFieldFilterValue](#customfieldfiltervalue) | The value to compare against. Null for `IS_NULL` and `IS_NOT_NULL` operators. |

---

<a id="customfieldspatchinput"></a>

### CustomFieldsPatchInput

Input for updating custom field values using a patch model.

`set` provides typed values (create/overwrite); `unset` removes fields by code. Primary
status travels with the value on the primary-capable variants (DEVICE/REFERENCE)
via their required `isPrimary` flag — there is no separate setPrimary/unsetPrimary list.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `set` | [[CustomFieldValueInput](#customfieldvalueinput)!] | Field values to set or overwrite. |
| `unset` | [[Code](common.md#code)!] | Field codes to remove entirely. |

---

<a id="customfieldvalueinput"></a>

### CustomFieldValueInput

A single custom-field value to set, addressed by code.

Omitting `value` is the same as passing null, so an entry clears the field unless it carries one.
An `isDefault` override cannot be changed on its own: send the current value alongside it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The custom field code. |
| `value` | [CustomFieldValueDataInput](#customfieldvaluedatainput) | The typed value. Null clears the field but keeps the key (distinct from `unset`). |
| `isDefault` | `Boolean` | Overrides the definition's `isDefault` for this entity, in either direction. Omit to leave any existing override untouched; the override is dropped when the field is `unset` or its value is cleared. |

---

<a id="customfieldvaluedatainput"></a>

### CustomFieldValueDataInput

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
| `device` | [DeviceValueInput](#devicevalueinput) | DEVICE value (primary-capable). |
| `reference` | [ReferenceValueInput](#referencevalueinput) | REFERENCE value for a single-value field (primary-capable). |
| `references` | [ReferenceListValueInput](#referencelistvalueinput) | REFERENCE value for an isMulti field (primary-capable). |
| `option` | [Code](common.md#code) | OPTIONS value for a single-value field. |
| `options` | [[Code](common.md#code)!] | OPTIONS value for an isMulti field. |

---

<a id="devicevalueinput"></a>

### DeviceValueInput

DEVICE custom-field value.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The device to assign. |
| `isPrimary` | `Boolean!` | Whether this device is the entity's primary DEVICE. Required — prevents silent demotion. |
| `reassign` | `Boolean` | If the device is already assigned to another asset, detach it there and reassign it here, atomically, instead of failing with VALIDATION_ERROR. Asset holders only. A DEVICE field may only be defined on an AssetType since v0.7.0, but definitions written before it stay live, and on such a field `reassign: true` is rejected with VALIDATION_ERROR naming it: only an asset holds a device exclusively, so there is nothing to detach it from. |

---

<a id="referencevalueinput"></a>

### ReferenceValueInput

REFERENCE custom-field value for a single-value field.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The referenced entity ID. |
| `isPrimary` | `Boolean!` | Whether this reference is the entity's primary REFERENCE. Required — prevents silent demotion. |

---

<a id="referencelistvalueinput"></a>

### ReferenceListValueInput

REFERENCE custom-field value for an isMulti field.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `ids` | `[ID!]!` | The referenced entity IDs, in the order they are stored. |
| `isPrimary` | `Boolean!` | Whether this reference is the entity's primary REFERENCE. Required — prevents silent demotion. |

---

<a id="customfielddefinitioninput"></a>

### CustomFieldDefinitionInput

A single operation on a custom field definition within the parent catalog item.
Exactly one action must be provided.

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `create` | [CustomFieldDefinitionCreateData](#customfielddefinitioncreatedata) | Create a new custom field definition. |
| `update` | [CustomFieldDefinitionUpdateData](#customfielddefinitionupdatedata) | Update an existing custom field definition. |
| `delete` | [CustomFieldDefinitionDeleteData](#customfielddefinitiondeletedata) | Delete a custom field definition. |
| `archive` | [CustomFieldDefinitionArchiveData](#customfielddefinitionarchivedata) | Archive a custom field definition (non-destructive deactivation). |
| `restore` | [CustomFieldDefinitionRestoreData](#customfielddefinitionrestoredata) | Restore a previously archived custom field definition. |

---

<a id="customfielddefinitioncreatedata"></a>

### CustomFieldDefinitionCreateData

Data for creating a custom field definition within its parent catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code) | The machine-readable code. Must start with `cf_`, so a custom-field code can never be confused with an entity's own field name. Auto-generated from the title if omitted, prefix included. The prefix also keeps this input clear of the platform's own reserved code `geojson_data`. |
| `title` | `String!` | The display name. |
| `description` | `String` | The description. |
| `fieldType` | [FieldType](#fieldtype)! | The data type. Immutable after creation. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `params` | [FieldParamsInput](#fieldparamsinput)! | The type-specific parameters. Exactly one variant must be provided. |
| `isDefault` | `Boolean` | Whether the field is on by default for entities of this type. Defaults to false. |

---

<a id="customfielddefinitionupdatedata"></a>

### CustomFieldDefinitionUpdateData

Data for updating an existing custom field definition. Note: `fieldType` cannot be changed.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The code of the field definition to update. |
| `title` | `String` | The new display name. |
| `description` | `String` | The new description. |
| `order` | `Int` | The new display order. |
| `params` | [FieldParamsInput](#fieldparamsinput) | The updated parameters. Only `isRequired` and type-specific fields can be changed. |
| `isDefault` | `Boolean` | The new type-level default. Omit to leave unchanged. |

---

<a id="customfielddefinitiondeletedata"></a>

### CustomFieldDefinitionDeleteData

Data for permanently deleting a custom field definition.

If entities have values for this field, the default behavior is to reject the deletion.
Use `onValues: CASCADE` to explicitly allow deletion with all associated values.

Prefer archiving for non-destructive deactivation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The code of the field definition to delete. |
| `onValues` | [CustomFieldDefinitionDeleteBehavior](#customfielddefinitiondeletebehavior) | What to do when existing entities have values for this field. Defaults to `REJECT` to prevent accidental data loss. |

---

<a id="customfielddefinitionarchivedata"></a>

### CustomFieldDefinitionArchiveData

Data for archiving or restoring a custom field definition.

Archiving deactivates the field without data loss:
- The field definition and all its values are preserved.
- The field no longer appears in forms and accepts no new values.
- Existing values remain readable and visible in history/exports.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The code of the field definition to archive. |

---

<a id="customfielddefinitionrestoredata"></a>

### CustomFieldDefinitionRestoreData

Data for restoring a previously archived custom field definition.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The code of the field definition to restore. |

---

<a id="fieldparamsinput"></a>

### FieldParamsInput

Field parameters input. Exactly one field must be provided.

{% hint style="warning" %}
This input type uses `@oneOf` - exactly one field must be provided.
{% endhint %}

| Field | Type | Description |
| ----- | ---- | ----------- |
| `string` | [StringFieldParamsInput](#stringfieldparamsinput) | Parameters for STRING field type. |
| `text` | [TextFieldParamsInput](#textfieldparamsinput) | Parameters for TEXT field type. |
| `decimal` | [DecimalFieldParamsInput](#decimalfieldparamsinput) | Parameters for DECIMAL field type. |
| `integer` | [IntegerFieldParamsInput](#integerfieldparamsinput) | Parameters for INTEGER field type. |
| `boolean` | [BooleanFieldParamsInput](#booleanfieldparamsinput) | Parameters for BOOLEAN field type. |
| `date` | [DateFieldParamsInput](#datefieldparamsinput) | Parameters for DATE field type. |
| `datetime` | [DateTimeFieldParamsInput](#datetimefieldparamsinput) | Parameters for DATETIME field type. |
| `geojson` | [GeoJsonFieldParamsInput](#geojsonfieldparamsinput) | Parameters for GEOJSON field type. |
| `options` | [OptionsFieldParamsInput](#optionsfieldparamsinput) | Parameters for OPTIONS field type. |
| `device` | [DeviceFieldParamsInput](#devicefieldparamsinput) | Parameters for DEVICE field type. |
| `reference` | [ReferenceFieldParamsInput](#referencefieldparamsinput) | Parameters for REFERENCE field type. |

---

<a id="stringfieldparamsinput"></a>

### StringFieldParamsInput

Parameters for STRING field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minLength` | `Int` | The minimum character length. |
| `maxLength` | `Int` | The maximum character length. |
| `defaultString` | `String` | The default value. |
| `trim` | `Boolean` | Whether to trim whitespace. |

---

<a id="textfieldparamsinput"></a>

### TextFieldParamsInput

Parameters for TEXT field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `maxLength` | `Int` | The maximum character length. |
| `defaultText` | `String` | The default value. |
| `trim` | `Boolean` | Whether to trim whitespace. |

---

<a id="decimalfieldparamsinput"></a>

### DecimalFieldParamsInput

Parameters for DECIMAL field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minDecimal` | [Decimal](common.md#decimal) | The minimum allowed value. |
| `maxDecimal` | [Decimal](common.md#decimal) | The maximum allowed value. |
| `scale` | `Int!` | Digits after the decimal point. Must be >= 0. Values sent with more fractional digits than `scale` are rounded using HALF_UP before storage. |
| `defaultDecimal` | [Decimal](common.md#decimal) | The default value. |

---

<a id="integerfieldparamsinput"></a>

### IntegerFieldParamsInput

Parameters for INTEGER field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `minInteger` | [Long](common.md#long) | The minimum allowed value. |
| `maxInteger` | [Long](common.md#long) | The maximum allowed value. |
| `defaultInteger` | [Long](common.md#long) | The default value. |

---

<a id="booleanfieldparamsinput"></a>

### BooleanFieldParamsInput

Parameters for BOOLEAN field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `defaultBoolean` | `Boolean` | The default value. |

---

<a id="datefieldparamsinput"></a>

### DateFieldParamsInput

Parameters for DATE field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `defaultDate` | [Date](common.md#date) | The default value. |

---

<a id="datetimefieldparamsinput"></a>

### DateTimeFieldParamsInput

Parameters for DATETIME field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `defaultDatetime` | [DateTime](common.md#datetime) | The default value. |

---

<a id="geojsonfieldparamsinput"></a>

### GeoJsonFieldParamsInput

Parameters for GEOJSON field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `allowedTypes` | [[GeoJsonGeometryType](geo-objects.md#geojsongeometrytype)!] | The allowed geometry types. Null means all types are allowed. |

---

<a id="optionsfieldparamsinput"></a>

### OptionsFieldParamsInput

Parameters for OPTIONS field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `isMulti` | `Boolean` | Whether multiple options can be selected. |
| `options` | [[FieldOptionInput](#fieldoptioninput)!]! | The available options. |
| `defaultOptions` | [Code](common.md#code) | The default option code. |

---

<a id="fieldoptioninput"></a>

### FieldOptionInput

Input for an option definition.
When updating options: if an entry without `code` is provided, a new option is created.
If the label already exists within this field, an error is returned.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code) | The unique code. Auto-generated from label if omitted. |
| `label` | `String!` | The display label. Must be unique within the custom field. |
| `description` | `String` | The description. |
| `isArchived` | `Boolean` | Whether this option is archived. |

---

<a id="devicefieldparamsinput"></a>

### DeviceFieldParamsInput

Parameters for DEVICE field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `refSubtypeIds` | `[ID!]` | The device type IDs a value may belong to. Omit or leave empty to allow any device type. |

---

<a id="referencefieldparamsinput"></a>

### ReferenceFieldParamsInput

Parameters for REFERENCE field type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required. |
| `isMulti` | `Boolean` | Whether multiple entities can be referenced. |
| `refEntityTypeCode` | [Code](common.md#code)! | The target entity type code, e.g. `asset`, `geo_object`, `schedule`, `tag`, `user_catalog_item`. The `customFieldTypes` query lists the accepted values for a given owner. |
| `refSubtypeIds` | `[ID!]` | The subtype IDs a value may belong to (AssetType / GeoObjectType / catalog). Required for `user_catalog_item`, which needs exactly one catalog; rejected for target types that nothing narrows further. Omit or leave empty to allow the whole target type. |
| `defaultRefIds` | `[ID!]` | The default referenced entity IDs. |

---

## Enums

<a id="fieldtype"></a>

### FieldType

The data type of a custom field, determining validation rules and UI rendering.

| Value | Description |
| ----- | ----------- |
| `STRING` | Single-line text input. Maximum 255 characters. |
| `TEXT` | Multi-line text input. Maximum 65,535 characters. |
| `DECIMAL` | Arbitrary-precision decimal value (encoded as Decimal string). |
| `INTEGER` | Signed 64-bit integer value (encoded as Long). |
| `BOOLEAN` | Boolean true/false value. |
| `DATE` | Calendar date without time component (YYYY-MM-DD). |
| `DATETIME` | Date and time with timezone information. |
| `GEOJSON` | GeoJSON geometry object (Point, Polygon, LineString, etc.). |
| `OPTIONS` | Selection from a predefined list of options. |
| `DEVICE` | Reference to a Device entity. Definable on an `AssetType` only — see the `customFieldTypes` query. May be narrowed to specific device types via `DeviceFieldParamsInput.refSubtypeIds`. |
| `REFERENCE` | Reference to any entity by its type and ID, including catalog items and tags. The target type is fixed per definition by `ReferenceFieldParamsInput.refEntityTypeCode` and may be narrowed further by `refSubtypeIds`; `customFieldTypes` lists the accepted target types. Devices are not among them — they have their own `DEVICE` field type. |

---

<a id="customfielddefinitiondeletebehavior"></a>

### CustomFieldDefinitionDeleteBehavior

Controls the behavior when deleting a custom field definition that has existing values.

Prefer archiving for non-destructive deactivation.
Use `CASCADE` only when permanent data removal is explicitly intended.

| Value | Description |
| ----- | ----------- |
| `REJECT` | Reject the deletion with an error if any entity has a value for this field. Default. |
| `CASCADE` | Delete the field definition and permanently remove all associated values. |

---

<a id="fieldoperator"></a>

### FieldOperator

Comparison operators for filtering by custom field values.

| Value | Description |
| ----- | ----------- |
| `EQ` | Value equals the specified value. |
| `NE` | Value does not equal the specified value. |
| `GT` | Value is greater than the specified value. |
| `GTE` | Value is greater than or equal to the specified value. |
| `LT` | Value is less than the specified value. |
| `LTE` | Value is less than or equal to the specified value. |
| `CONTAINS` | String value contains the specified substring (case-insensitive). |
| `IN` | Value is one of the specified values in the array. |
| `IS_NULL` | Value is null. |
| `IS_NOT_NULL` | Value is not null. |

---

## Interfaces

<a id="customfieldvalue"></a>

### CustomFieldValue

A typed custom-field value on an entity. The concrete type matches the custom field's
declared `FieldType`. `isPrimary` is present only on the primary-capable types
(DEVICE / REFERENCE).

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](common.md#code)! | The custom field code. |
| `isDefault` | `Boolean!` | Whether this field is on by default for this entity: the definition's `isDefault`, unless the entity overrides it on the value. |

---

<a id="fieldparams"></a>

### FieldParams

The base interface for field parameters.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isRequired` | `Boolean!` | Whether a value is required for this field. |

---
