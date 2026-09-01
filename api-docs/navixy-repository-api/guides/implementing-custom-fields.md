---
description: Define and use custom fields to attach domain-specific data to entities.
---

# Implementing custom fields

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

In Navixy Repository API, assets and geo objects each come with a set of built-in fields such as `title`, `workspace`, and `type`. Custom fields let you store your own data in these entities, such as a VIN number, a fuel type, an inspection date, or an access level, without any changes to the platform schema.

## How custom fields work

Custom fields can be user-defined or predefined. User-defined fields are ones you create, and each belongs to a single entity type: a `cf_vin` field defined on the "Vehicle" asset type appears on vehicles and nowhere else. Predefined fields are built into the platform for certain entity types. For example, geo objects have `geojson_data`, which you read through its own field, `GeoObject.geojsonData`.

{% hint style="warning" %}
This guide covers user-defined fields. Every user-defined code must start with `cf_`, so a custom field can never share a name with a built-in field like `title`. If you omit `code` entirely, the API generates it from the title, prefix included; a code you supply yourself must already start with `cf_`, or the API rejects it with a validation error. The code `geojson_data` is reserved by the platform, so a definition can't claim it.
{% endhint %}

Every custom field has a [FieldType](../custom-fields.md#fieldtype) that determines what kind of data it stores and what validation options are available. You can add any number of fields with different types to a given entity type.

### Field type reference

<table><thead><tr><th width="135">Field type</th><th width="185">Use for</th><th width="247">Key params</th><th>Value in <code>set</code></th></tr></thead><tbody><tr><td><code>STRING</code></td><td>Short text, codes, identifiers</td><td><code>isRequired</code>, <code>minLength</code>, <code>maxLength</code>, <code>defaultString</code>, <code>trim</code></td><td><code>{ string: "1HGBH41JXMN109186" }</code></td></tr><tr><td><code>TEXT</code></td><td>Long descriptions, notes</td><td><code>isRequired</code>, <code>maxLength</code>, <code>defaultText</code>, <code>trim</code></td><td><code>{ string: "Installed under dashboard" }</code></td></tr><tr><td><code>DECIMAL</code></td><td>Precise measurements, currency, weights</td><td><code>isRequired</code>, <code>minDecimal</code>, <code>maxDecimal</code>, <code>scale</code>, <code>defaultDecimal</code></td><td><code>{ decimal: "42.50" }</code></td></tr><tr><td><code>INTEGER</code></td><td>Whole-number quantities, counts</td><td><code>isRequired</code>, <code>minInteger</code>, <code>maxInteger</code>, <code>defaultInteger</code></td><td><code>{ integer: 42 }</code></td></tr><tr><td><code>BOOLEAN</code></td><td>Flags, yes/no attributes</td><td><code>isRequired</code>, <code>defaultBoolean</code></td><td><code>{ boolean: true }</code></td></tr><tr><td><code>DATE</code></td><td>Calendar dates</td><td><code>isRequired</code>, <code>defaultDate</code></td><td><code>{ date: "2025-06-01" }</code></td></tr><tr><td><code>DATETIME</code></td><td>Timestamps</td><td><code>isRequired</code>, <code>defaultDatetime</code></td><td><code>{ datetime: "2025-06-01T09:00:00Z" }</code></td></tr><tr><td><code>GEOJSON</code></td><td>Geometry data</td><td><code>isRequired</code>, <code>allowedTypes</code> (<a href="../geo-objects.md#geojsongeometrytype">GeoJsonGeometryType</a>)</td><td><code>{ geojson: {"type":"Point","coordinates":[...]} }</code></td></tr><tr><td><code>OPTIONS</code></td><td>Predefined choices (single or multi)</td><td><code>isRequired</code>, <code>isMulti</code>, <code>options[]</code>, <code>defaultOptions</code></td><td><code>{ option: "diesel" }</code> or <code>{ options: ["diesel"] }</code></td></tr><tr><td><code>DEVICE</code></td><td>Links to device records. Definable on an <code>AssetType</code> only</td><td><code>isRequired</code>, <code>refSubtypeIds</code></td><td><code>{ device: { id: "...", isPrimary: true } }</code></td></tr><tr><td><code>REFERENCE</code></td><td>Links to any other entity, including catalog items and tags</td><td><code>isRequired</code>, <code>isMulti</code>, <code>refEntityTypeCode</code>, <code>refSubtypeIds</code>, <code>defaultRefIds</code></td><td><code>{ reference: { id: "...", isPrimary: false } }</code> or <code>{ references: { ids: [...], isPrimary: false } }</code></td></tr></tbody></table>

{% hint style="info" %}
There is no separate field type for schedules, catalog items, or tags. Use `REFERENCE` and fix the target with `refEntityTypeCode`, for example `schedule`, `tag`, or `user_catalog_item`. Devices are the exception: they keep their own `DEVICE` type. To find out which types a given owner accepts, and which entity types its `REFERENCE` fields may point at, query [customFieldTypes](#discovering-available-field-types).
{% endhint %}

Each field is defined by [CustomFieldDefinition](../custom-fields.md#customfielddefinition), a metadata record that specifies the field's code, display title, type, and validation rules. When you create or update an entity, you supply field values through the `customFields` field in the mutation input, and the API validates each value against the corresponding definition.

### Writing custom field values

In any create or update mutation, `customFields` accepts a [CustomFieldsPatchInput](../custom-fields.md#customfieldspatchinput) with two sub-fields:

<table><thead><tr><th width="169">Field</th><th width="230">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>set</code></td><td>[<a href="../custom-fields.md#customfieldvalueinput">CustomFieldValueInput</a>!]</td><td>Typed field values to create or overwrite.</td></tr><tr><td><code>unset</code></td><td>[<a href="../common.md#code">Code</a>!]</td><td>List of field codes to remove entirely.</td></tr></tbody></table>

Each entry in `set` has three parts:

<table><thead><tr><th width="140">Field</th><th width="230">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>code</code></td><td><a href="../common.md#code">Code</a>!</td><td>The field code, including the <code>cf_</code> prefix.</td></tr><tr><td><code>value</code></td><td><a href="../custom-fields.md#customfieldvaluedatainput">CustomFieldValueDataInput</a></td><td>The typed value. Uses <a href="../directives.md#oneof">@oneOf</a>, so provide exactly one option, the one matching the field's declared type. Set it to <code>null</code> to clear the value but keep the key, which is different from <code>unset</code>.</td></tr><tr><td><code>isDefault</code></td><td><code>Boolean</code></td><td>Overrides the definition's <code>isDefault</code> for this entity, in either direction. Omit to leave any existing override untouched.</td></tr></tbody></table>

This is the **patch model**: fields you don't mention are left unchanged. You can `set` and `unset` in the same mutation. For example, to update a license plate and remove an assigned driver in one call, add this code:

```graphql
customFields: {
  set: [
    { code: "cf_license_plate", value: { string: "HH-TL 4421" } }
  ]
  unset: ["cf_assigned_driver"]
}
```

Omitting `customFields` altogether leaves all existing values untouched.

## Scenario: Enriching fleet records with metadata

A logistics company needs to store operational metadata on their vehicle assets: a VIN number for compliance, a fuel type for route planning, and a next service date for maintenance management. All examples in this guide use these three fields.

Adding this metadata requires the following steps:

{% stepper %}
{% step %}

#### Choose a field type

Before creating a definition, pick the `fieldType` that best matches your data. See the [field type reference](implementing-custom-fields.md#field-type-reference).

{% hint style="warning" %}
`fieldType` is immutable after creation. If you need to change a field's type, delete the definition and create a new one. Deleting a definition that still has stored values is rejected by default (`onValues: REJECT`); set `onValues: CASCADE` to remove the definition together with all its stored values.
{% endhint %}
{% endstep %}

{% step %}

#### Create field definitions

Custom field definitions belong to the type whose entities will have the fields. In this scenario, that's an `AssetType` such as "Vehicle". There's no separate mutation for definitions: include them in the `customFieldDefinitions` list when you update the type itself with [assetTypeUpdate](../assets/README.md#assettypeupdate). The whole mutation succeeds or fails as one change: either every definition in the list is created, updated, or deleted under one type `version`, or none of them are.

{% hint style="info" %}
The `version` field is optional (see [Optimistic locking](../optimistic-locking.md)) but recommended when modifying a shared catalog item that other users may be editing concurrently.
{% endhint %}

**2.1 Check the existing definitions**

Before adding new fields, check which fields the type already has. Fetch the type and include `customFieldDefinitions` in the query:

```graphql
query GetVehicleTypeFields {
  bdr {
    assetTypes(
      workspaceId: "019d48ea-0752-8000-801f-444556437ab1"
      filter: { codes: ["vehicle"] }
      first: 1
    ) {
      nodes {
        title
        customFieldDefinitions {
          code
          title
          fieldType
          isArchived
        }
      }
    }
  }
}
```

Response (if the fields already exist):

```json
{
  "data": {
    "bdr": {
      "assetTypes": {
        "nodes": [
          {
            "title": "Vehicle",
            "customFieldDefinitions": [
              {
                "code": "cf_vin",
                "title": "VIN Number",
                "fieldType": "STRING",
                "isArchived": false
              },
              {
                "code": "cf_fuel_type",
                "title": "Fuel Type",
                "fieldType": "OPTIONS",
                "isArchived": false
              }
            ]
          }
        ]
      }
    }
  }
}
```

If no custom fields have been created yet, `customFieldDefinitions` is an empty array. The same pattern works for [geoObjectTypes](../geo-objects.md#geoobjecttypes), the other owner that has custom fields.

**2.2 Choose codes for your fields**

Choose a [code](../common.md#code) for each field before creating its definition. The code is what you use to read and write the field's values in every query and mutation. Once entities store values under a code, avoid changing it: a code can only be changed by deleting the definition and creating a new one, and the stored values are lost with it.

If you omit `code`, it's auto-generated from `title` (transliterated to lowercase Latin, spaces replaced with `_`, truncated at 30 characters, with a numeric suffix on collision). Explicitly setting a code gives you control over how that key appears in your data.

Codes can contain ASCII letters, digits, underscores, dots, and hyphens, and must start with a letter or digit (max 64 characters).

**2.3 Create the field definitions**

Add all three fields in a single mutation. Each entry in `customFieldDefinitions` names one operation, `create` in this case, because the input is [@oneOf](../directives.md#oneof): exactly one of `create`, `update`, `delete`, `archive`, or `restore` per entry.

```graphql
mutation AddVehicleFields {
  bdr {
    assetTypeUpdate(
      input: {
        id: "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11"
        version: 1
        customFieldDefinitions: [
          # STRING field (VIN number)
          {
            create: {
              code: "cf_vin"
              title: "VIN Number"
              description: "17-character Vehicle Identification Number"
              fieldType: STRING
              order: 1
              params: {
                string: {
                  isRequired: true
                  minLength: 17
                  maxLength: 17
                  trim: true
                }
              }
            }
          }
          # OPTIONS field (fuel type with predefined choices)
          {
            create: {
              code: "cf_fuel_type"
              title: "Fuel Type"
              fieldType: OPTIONS
              order: 2
              params: {
                options: {
                  isRequired: true
                  isMulti: false
                  defaultOptions: "diesel"
                  options: [
                    { code: "diesel", label: "Diesel" }
                    { code: "electric", label: "Electric" }
                    { code: "hybrid", label: "Hybrid" }
                  ]
                }
              }
            }
          }
          # DATE field (next scheduled service)
          {
            create: {
              code: "cf_next_service_date"
              title: "Next Service Date"
              fieldType: DATE
              order: 3
              params: { date: { isRequired: false } }
            }
          }
        ]
      }
    ) {
      assetType {
        version
        customFieldDefinitions {
          code
          title
          fieldType
        }
      }
    }
  }
}
```

The response returns the updated type with incremented `version` and the full list of definitions:

```json
{
  "data": {
    "bdr": {
      "assetTypeUpdate": {
        "assetType": {
          "version": 2,
          "customFieldDefinitions": [
            { "code": "cf_vin", "title": "VIN Number", "fieldType": "STRING" },
            {
              "code": "cf_fuel_type",
              "title": "Fuel Type",
              "fieldType": "OPTIONS"
            },
            {
              "code": "cf_next_service_date",
              "title": "Next Service Date",
              "fieldType": "DATE"
            }
          ]
        }
      }
    }
  }
}
```

{% hint style="warning" %}
The `params` input is [@oneOf](../directives.md#oneof): provide exactly one params block, and it must be the one matching your `fieldType`.

Each field type has its own named params block. The example above demonstrates this: the VIN field uses `params: { string: { ... } }`, the fuel type field uses `params: { options: { ... } }`, and the service date field uses `params: { date: { ... } }`. Providing the wrong variant returns a [validation error](../error-handling.md#validation-error-400).&#x20;
{% endhint %}

The same pattern applies to [geoObjectTypeUpdate](../geo-objects.md#geoobjecttypeupdate), with just the parent type's input changing. Only `create` is allowed for a catalog item creation mutation such as [assetTypeCreate](../assets/README.md#assettypecreate). The full set of operations (`update`, `delete`, `archive`, `restore`) is available for [assetTypeUpdate](../assets/README.md#assettypeupdate).
{% endstep %}

{% step %}

#### Set and update values

Pass `customFields` in the create mutation with the initial values under `set`. The following example creates a vehicle asset with all three fields populated:

```graphql
mutation CreateVehicleAsset {
  bdr {
    assetCreate(
      input: {
        workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
        typeId: "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11"
        title: "Truck 12"
        customFields: {
          set: [
            { code: "cf_vin", value: { string: "1HGBH41JXMN109186" } }
            { code: "cf_fuel_type", value: { option: "diesel" } }
            { code: "cf_next_service_date", value: { date: "2025-09-01" } }
          ]
        }
      }
    ) {
      asset {
        id
        version
        customFields {
          code
          ... on StringCustomFieldValue {
            stringValue: value
          }
          ... on OptionsCustomFieldValue {
            values
          }
          ... on DateCustomFieldValue {
            dateValue: value
          }
        }
      }
    }
  }
}
```

The response returns the created asset's `id`, `version`, and `customFields`:

```json
{
  "data": {
    "bdr": {
      "assetCreate": {
        "asset": {
          "id": "019a6b2f-793e-807b-8001-555345529b44",
          "version": 1,
          "customFields": [
            { "code": "cf_vin", "stringValue": "1HGBH41JXMN109186" },
            { "code": "cf_fuel_type", "values": ["diesel"] },
            { "code": "cf_next_service_date", "dateValue": "2025-09-01" }
          ]
        }
      }
    }
  }
}
```

{% hint style="warning" %}
If any custom field value fails validation, the entire mutation is rejected with a [validation error](../error-handling.md#validation-error-400). Common causes include a value that violates the field's `params` constraints (for example, a VIN shorter than 17 characters), an unrecognized field code, or a value of the wrong type. The error response includes a `field` path and a `detail` message identifying the problem. See [Error handling](../error-handling.md) for the full error format.
{% endhint %}

The same pattern applies to [geoObjectCreate](../geo-objects.md#geoobjectcreate).
{% endstep %}

{% step %}

#### Update custom field values

Use `set` to overwrite specific fields and `unset` to remove them. Fields omitted from both are left unchanged.

The following mutation updates the next service date after a completed maintenance and removes a temporary inspection hold:

```graphql
mutation UpdateVehicleFields {
  bdr {
    assetUpdate(
      input: {
        id: "019a6b2f-793e-807b-8001-555345529b44"
        version: 1
        customFields: {
          set: [{ code: "cf_next_service_date", value: { date: "2026-03-01" } }]
          unset: ["cf_inspection_hold"]
        }
      }
    ) {
      asset {
        id
        version
        customFields {
          code
          ... on StringCustomFieldValue {
            stringValue: value
          }
          ... on OptionsCustomFieldValue {
            values
          }
          ... on DateCustomFieldValue {
            dateValue: value
          }
        }
      }
    }
  }
}
```

The response returns the updated asset's `id`, `version`, and `customFields`. Fields that were unset no longer appear in the list:

```json
{
  "data": {
    "bdr": {
      "assetUpdate": {
        "asset": {
          "id": "019a6b2f-793e-807b-8001-555345529b44",
          "version": 2,
          "customFields": [
            { "code": "cf_vin", "stringValue": "1HGBH41JXMN109186" },
            { "code": "cf_fuel_type", "values": ["diesel"] },
            { "code": "cf_next_service_date", "dateValue": "2026-03-01" }
          ]
        }
      }
    }
  }
}
```

{% hint style="info" %}
You can include `version` in update mutations to enable [optimistic locking](../optimistic-locking.md). Fetch the entity first if you don't have the latest version.
{% endhint %}
{% endstep %}

{% step %}

#### Read custom field values

`customFields` on any entity returns a list of typed values, one per field that has a value. Each element's type matches the field's declared `fieldType`: a `STRING` field comes back as a `StringCustomFieldValue`, a `DATE` field as a `DateCustomFieldValue`, and so on. All of them share the [CustomFieldValue](../custom-fields.md#customfieldvalue) interface, which only guarantees `code` and `isDefault`. To read the actual values, use inline fragments, the `... on TypeName { }` blocks in the query below. Each block says "if the element is this type, also return these fields" (see [Inline fragments for interfaces](../graphql-basics/graphql-tips-and-patterns.md#inline-fragments-for-interfaces)). By default, all fields are returned. Run the following query:

```graphql
query GetAssetFields {
  bdr {
    asset(id: "019a6b2f-793e-807b-8001-555345529b44") {
      title
      customFields {
        code
        isDefault
        ... on StringCustomFieldValue {
          stringValue: value
        }
        ... on OptionsCustomFieldValue {
          values
        }
        ... on DateCustomFieldValue {
          dateValue: value
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "asset": {
        "title": "Truck 12",
        "customFields": [
          {
            "code": "cf_vin",
            "isDefault": true,
            "stringValue": "1HGBH41JXMN109186"
          },
          { "code": "cf_fuel_type", "isDefault": true, "values": ["diesel"] },
          {
            "code": "cf_next_service_date",
            "isDefault": false,
            "dateValue": "2025-09-01"
          }
        ]
      }
    }
  }
}
```

`StringCustomFieldValue` and `DateCustomFieldValue` both have a field named `value`, but with different data types (`String!` and `Date!`). GraphQL rejects a query that selects both under the same name, so one of them needs a new name, which is what an alias does. That's why the query above uses `stringValue: value` and `dateValue: value`.

`isDefault` says whether the field is on by default for this entity. Its value is copied from the definition's `isDefault`, unless the entity has overridden it through `CustomFieldValueInput.isDefault`.

To retrieve only specific fields, use the `codes` argument. This keeps responses smaller when an entity type has many fields:

```graphql
query GetVehicleKeyFields {
  bdr {
    asset(id: "019a6b2f-793e-807b-8001-555345529b44") {
      title
      customFields(codes: ["cf_vin", "cf_fuel_type"]) {
        code
        ... on StringCustomFieldValue {
          stringValue: value
        }
        ... on OptionsCustomFieldValue {
          values
        }
      }
    }
  }
}
```

The response contains only the requested fields:

```json
{
  "data": {
    "bdr": {
      "asset": {
        "title": "Truck 12",
        "customFields": [
          { "code": "cf_vin", "stringValue": "1HGBH41JXMN109186" },
          { "code": "cf_fuel_type", "values": ["diesel"] }
        ]
      }
    }
  }
}
```

{% endstep %}

{% step %}

#### Filter entities by custom field value

The asset and geo object list queries support filtering by custom field values through [CustomFieldFilter](../custom-fields.md#customfieldfilter). Add one or more conditions to the `customFields` filter array. Multiple conditions are applied as AND.

For the full operator list and value formats by field type, see [Filtering and sorting](../custom-field-filtering.md#operators).

**How to filter by an OPTIONS value**

Find all electric vehicle assets:

```graphql
query FindElectricVehicles {
  bdr {
    assets(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: {
        typeIds: ["a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11"]
        customFields: [
          { code: "cf_fuel_type", operator: EQ, value: { string: "electric" } }
        ]
      }
    ) {
      nodes {
        id
        title
        customFields(codes: ["cf_fuel_type", "cf_next_service_date"]) {
          code
          ... on OptionsCustomFieldValue {
            values
          }
          ... on DateCustomFieldValue {
            dateValue: value
          }
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assets": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-555345529b44",
            "title": "Truck 12",
            "customFields": [
              { "code": "cf_fuel_type", "values": ["electric"] },
              { "code": "cf_next_service_date", "dateValue": "2025-09-01" }
            ]
          }
        ]
      }
    }
  }
}
```

**How to filter by a DATE value**

Find vehicles with a service date before a deadline:

```graphql
query FindOverdueVehicles {
  bdr {
    assets(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: {
        typeIds: ["a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11"]
        customFields: [
          {
            code: "cf_next_service_date"
            operator: LT
            value: { date: "2025-07-01" }
          }
        ]
      }
    ) {
      nodes {
        id
        title
        customFields(codes: ["cf_next_service_date"]) {
          code
          ... on DateCustomFieldValue {
            dateValue: value
          }
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assets": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-555345529b44",
            "title": "Truck 12",
            "customFields": [
              { "code": "cf_next_service_date", "dateValue": "2025-06-15" }
            ]
          }
        ]
      }
    }
  }
}
```

**How to combine multiple conditions**

Find electric vehicle assets whose next service date has passed:

```graphql
query FindOverdueElectricVehicles {
  bdr {
    assets(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: {
        typeIds: ["a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11"]
        customFields: [
          { code: "cf_fuel_type", operator: EQ, value: { string: "electric" } }
          {
            code: "cf_next_service_date"
            operator: LT
            value: { date: "2025-07-01" }
          }
        ]
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

Response:

```json
{
  "data": {
    "bdr": {
      "assets": {
        "nodes": [
          {
            "id": "019a6c3f-894e-817b-8002-666456630c55",
            "title": "Tracker 04"
          }
        ]
      }
    }
  }
}
```

{% hint style="info" %}
Omit `value` (or set it to `null`) when using the `IS_NULL` and `IS_NOT_NULL` operators.
{% endhint %}
{% endstep %}
{% endstepper %}

## Discovering available field types

Not every field type can be defined on every owner. `DEVICE` fields, for example, may only be
defined on an `AssetType`, and a `REFERENCE` field can only point at the entity types the platform
accepts for that owner. Rather than hardcoding those rules, ask the API with
[customFieldTypes](../custom-fields.md#customfieldtypes):

```graphql
query VehicleFieldTypes {
  bdr {
    customFieldTypes(ownerTypeId: "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11") {
      fieldType
      referenceableEntityTypes
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "customFieldTypes": [
        { "fieldType": "STRING", "referenceableEntityTypes": [] },
        { "fieldType": "OPTIONS", "referenceableEntityTypes": [] },
        { "fieldType": "DEVICE", "referenceableEntityTypes": [] },
        {
          "fieldType": "REFERENCE",
          "referenceableEntityTypes": [
            "asset",
            "geo_object",
            "schedule",
            "tag",
            "user_catalog_item"
          ]
        }
      ]
    }
  }
}
```

`referenceableEntityTypes` lists the values accepted by `ReferenceFieldParamsInput.refEntityTypeCode`,
and it's empty for every field type other than `REFERENCE`. Creating a definition whose `fieldType`
is absent from this list fails with a [validation error](../error-handling.md#validation-error-400)
on `input.fieldType`.

## Managing definitions

### How to update a custom field definition

You can update the `title`, `description`, `order`, `params`, and `isDefault`. The `code` and `fieldType` cannot be changed after creation.

Add an `update` operation to the parent type's `customFieldDefinitions` array. The field is identified by its `code`:

```graphql
mutation UpdateVinField {
  bdr {
    assetTypeUpdate(
      input: {
        id: "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11"
        version: 2
        customFieldDefinitions: [
          {
            update: {
              code: "cf_vin"
              title: "VIN"
              description: "Vehicle Identification Number (17 characters, no I/O/Q)"
              params: {
                string: {
                  isRequired: true
                  minLength: 17
                  maxLength: 17
                  trim: true
                }
              }
            }
          }
        ]
      }
    ) {
      assetType {
        version
        customFieldDefinitions {
          code
          title
          description
        }
      }
    }
  }
}
```

The response returns the updated type with incremented `version` and the updated definition.

For OPTIONS fields, you can add new options or archive existing ones. Archiving an option (`isArchived: true`) hides it from new selections without affecting records that already carry it:

```graphql
params: {
  options: {
    isRequired: true
    isMulti: false
    options: [
      { code: "diesel",   label: "Diesel",   isArchived: false }
      { code: "electric", label: "Electric", isArchived: false }
      { code: "hybrid",   label: "Hybrid",   isArchived: false }
      { code: "hydrogen", label: "Hydrogen", isArchived: false }
      { code: "lpg",      label: "LPG",      isArchived: true  }
    ]
  }
}
```

### How to archive and restore a custom field definition

Archiving is the preferred method of deactivating a field you no longer need. An archived field preserves all existing values (which remain readable and visible in history and exports), but the field stops accepting new input and no longer appears in forms.

To archive a field, run this mutation:

```graphql
mutation ArchiveVinField {
  bdr {
    assetTypeUpdate(
      input: {
        id: "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11"
        version: 3
        customFieldDefinitions: [{ archive: { code: "cf_vin" } }]
      }
    ) {
      assetType {
        version
        customFieldDefinitions {
          code
          title
          isArchived
        }
      }
    }
  }
}
```

To reactivate an archived field, use `restore`:

```graphql
mutation RestoreVinField {
  bdr {
    assetTypeUpdate(
      input: {
        id: "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11"
        version: 4
        customFieldDefinitions: [{ restore: { code: "cf_vin" } }]
      }
    ) {
      assetType {
        version
        customFieldDefinitions {
          code
          title
          isArchived
        }
      }
    }
  }
}
```

### How to delete a custom field definition

Deletion is permanent and cannot be undone. Use [archiving](implementing-custom-fields.md#how-to-archive-and-restore-a-custom-field-definition) instead unless you explicitly need to remove the field and its data.

By default, deletion is rejected if any entity currently has a value for the field. Pass `onValues: CASCADE` to force deletion along with all associated values across every entity record:

```graphql
mutation DeleteDefinition {
  bdr {
    assetTypeUpdate(
      input: {
        id: "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11"
        version: 5
        customFieldDefinitions: [
          { delete: { code: "cf_vin", onValues: CASCADE } }
        ]
      }
    ) {
      assetType {
        version
        customFieldDefinitions {
          code
          title
        }
      }
    }
  }
}
```

The response shows that `cf_vin` no longer appears in the list, confirming that the definition and all its stored values have been removed.

```json
{
  "data": {
    "bdr": {
      "assetTypeUpdate": {
        "assetType": {
          "version": 6,
          "customFieldDefinitions": [
            { "code": "cf_fuel_type", "title": "Fuel Type" },
            { "code": "cf_next_service_date", "title": "Next Service Date" }
          ]
        }
      }
    }
  }
}
```

{% hint style="info" %}
`onValues: CASCADE` permanently removes the field's values from every entity record. They cannot be recovered. Use `archive` for non-destructive deactivation.

If you create a new definition with the same `code` later, existing records don't keep any value for it. The data removed by `CASCADE` is not recoverable.
{% endhint %}

## Constraints and considerations

Keep in mind the following:

- **Validation errors reject the entire mutation:** Mutations that include invalid custom field values are rejected in full. See [Error handling](../error-handling.md) for the error format.
- `fieldType`** is immutable:** To change a field's type, delete its definition and create a new one. Deleting the definition removes its values from all entity records.
- `code`** is stable once in use:** `code` must start with `cf_` and be unique within the owner type and workspace. As this is the key used to read and write values across all entity mutations and queries, avoid recreating it under a different name if records contain values paired with it. If you rely on auto-generation, verify the generated code before any records are written under it.
- `params`** takes exactly one params block:** `FieldParamsInput` is [@oneOf](../directives.md#oneof), so provide exactly the block that matches your `fieldType`. Providing `string: { ... }` when `fieldType` is `DECIMAL` returns a validation error.
- **Multi-value fields and filtering:** For `OPTIONS` and `REFERENCE` fields configured with `isMulti: true`, a filter matches if _any_ value in the list satisfies the condition. For example, if an asset has `cf_fuel_type` set to `["diesel", "hybrid"]`, filtering with `EQ: "diesel"` matches it.
- **Predefined fields:** The platform manages `geojson_data`, which is excluded from `customFields` responses and returned through `GeoObject.geojsonData` instead. Its code is reserved and can't be used for a user-defined field.
- **Owners are **`Customizable`** types only:** custom fields are defined on an `AssetType` or a `GeoObjectType`, so assets and geo objects are the entities that have them. `DEVICE` fields are narrower still: they may only be defined on an `AssetType`.
- `isPrimary`** is required on **`DEVICE`** and **`REFERENCE`** values:** stating it on every write prevents an update from silently demoting the entity's current primary value.

## See also

* [Custom fields](../custom-fields.md): Complete reference for all custom field operations and types
* [Filtering and sorting](../filtering-and-sorting.md): Narrow list queries and control result order
* [Custom field filtering and sorting](../custom-field-filtering.md): Filter and sort assets and geo objects by custom field values
* [Optimistic locking](../optimistic-locking.md): Prevent concurrent updates from overwriting each other with `version`
