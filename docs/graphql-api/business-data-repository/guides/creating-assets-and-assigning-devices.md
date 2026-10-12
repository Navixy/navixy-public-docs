---
description: 'Create and manage assets: vehicles, equipment, and other tracked objects.'
tags:
  - guide
  - beta
---

# Creating assets and assigning devices

Assets in Business Data Repository represent the objects your workspace tracks and manages. The most common example is a vehicle, but assets can be anything you need to monitor: construction equipment, forklifts, generators, shipping containers, leased machinery, or fixed infrastructure. If your workspace has a reason to record it, assign attributes to it, or link a GPS device to it, it's a good candidate for an asset.

Each asset is defined by the **asset type**, which acts as a template: it classifies the asset and determines which custom fields are available for it. For example, a "Delivery Truck" type might have fields for license plate, fuel capacity, and assigned driver, while a "Generator" type might have fields for power output, last service date, and installation site.

To organize assets into named collections, such as grouping vehicles by depot or equipment by project, see [Organizing assets into groups](organizing-assets-into-groups.md).

## Prerequisites

You need an access token for the workspace you work in. The API takes the workspace from the token, so no operation on this page asks for a workspace ID. See [Authentication](../../authentication.md) for how to get a token and how the API picks the workspace.

### Check available asset types

Before creating an asset, request the available asset types for your workspace:

```graphql
query ListAssetTypes {
  bdr {
    assetTypes {
      nodes {
        id
        code
        title
      }
    }
  }
}
```

You'll get the types of your workspace. A new workspace starts with the preset types Workforce, Vehicle, and Cargo, which your workspace can edit or delete:

```json
{
  "data": {
    "bdr": {
      "assetTypes": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-434154500001",
            "code": "vehicle",
            "title": "Vehicle"
          }
        ]
      }
    }
  }
}
```

If you need a type that doesn't exist yet, you can create it as described in [the scenario below](creating-assets-and-assigning-devices.md#create-an-asset-type).

If you're working with an existing type and need to know which custom fields it has, query [customFieldDefinitions](../api-reference/custom-fields.md#customfielddefinition) on the type:

```graphql
query GetTruckTypeFields {
  bdr {
    assetTypes(
      filter: { codes: ["delivery_truck"] }
    ) {
      nodes {
        id
        title
        customFieldDefinitions {
          code
          title
          fieldType
          params {
            isRequired
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
      "assetTypes": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-434154500002",
            "title": "Delivery Truck",
            "customFieldDefinitions": [
              {
                "code": "cf_license_plate",
                "title": "License Plate",
                "fieldType": "STRING",
                "params": {
                  "isRequired": true
                }
              },
              {
                "code": "cf_fuel_capacity_l",
                "title": "Fuel Capacity (L)",
                "fieldType": "DECIMAL",
                "params": {
                  "isRequired": false
                }
              }
            ]
          }
        ]
      }
    }
  }
}
```

The `code` values here are exactly what you use as `code` in `customFields.set` entries when creating or updating assets of this type. For type-specific parameters like maximum string length or the list of valid options, see [Defining and using custom fields](defining-and-using-custom-fields.md).

## How assets work

### Asset types

An asset type classifies assets and defines which custom fields they have. It is a [catalog item](../api-reference/catalogs/catalog-items.md), so it combines the common catalog item fields with one field of its own, `customFieldDefinitions`:

| Field                    | What it holds                                                                                                                                  |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `code`                   | A stable machine-readable identifier ([Code](../../core-api-reference/common.md#code)). Integrations and filters use it to reference the type. |
| `title`                  | The display name shown in UIs.                                                                                                                 |
| `meta`                   | UI and lifecycle properties: `description`, `origin`, `canBeDeleted`, and `hidden`.                                                            |
| `customFieldDefinitions` | All custom fields available on assets of this type.                                                                                            |
| `workspace`              | The workspace that owns the type. `null` for system types.                                                                                     |

{% hint style="warning" %}
Before creating a type, remember that `code` is immutable after creation. Choose it carefully, since it's what integrations and filters will use to reference the type.
{% endhint %}

{% hint style="info" %}
`meta.canBeDeleted` is `false` for system-managed types and `true` for every workspace type, whether or not assets still use it. Deleting a type that an asset still has, that an asset group type lists in `allowedAssetTypes`, that a `REFERENCE` custom field value names, or that a custom field definition names in `refSubtypeIds` or `defaultRefIds` is refused with [`IN_USE`](../../error-handling.md#in-use-409). Nothing is deleted, and `extensions.blockers` names the records to change first. An asset keeps its type for life, so delete those assets, or keep the type.
{% endhint %}

Types come from one of three places, and the `meta.origin` field on the type says which: predefined by the platform (`SYSTEM`), created by Business Data Repository when your workspace was activated (`PRESET`: the base types Workforce, Vehicle, and Cargo), or created by your workspace (`WORKSPACE`). You can update and delete types with `PRESET` or `WORKSPACE` origin, because your workspace owns them; system types are read-only. The `workspace` field on an asset type is `null` for `SYSTEM`-origin types, since no single workspace owns them.

For the full field reference, see [AssetType](../api-reference/assets/#assettype).

### Asset fields

An asset has a `title`, belongs to a workspace, and is classified by an asset type. Everything else it stores lives in its custom fields:

| Field           | What it holds                                                                                              |
| --------------- | ---------------------------------------------------------------------------------------------------------- |
| `title`         | The display name.                                                                                          |
| `workspace`     | The workspace that owns the asset.                                                                         |
| `type`          | The asset type that classifies the asset and defines its custom fields.                                    |
| `customFields`  | The stored custom field values, returned as a list of typed values.                                        |
| `primaryDevice` | The linked device marked as primary. `null` if no primary device is set.                                   |
| `groups`        | A paginated list of the asset groups the asset belongs to, with optional filtering and ordering arguments. |

For the full field reference, see [Asset object](../api-reference/assets/#asset).

### Custom fields

Assets store your own data, such as a license plate or a fuel capacity, in the `customFields` field. When creating or updating an asset, you describe custom field changes with [CustomFieldsPatchInput](../api-reference/custom-fields.md#customfieldspatchinput), which has two sub-fields:

<table><thead><tr><th width="136.111083984375">Field</th><th width="180">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>set</code></td><td>[<a href="../api-reference/custom-fields.md#customfieldvalueinput">CustomFieldValueInput</a>!]</td><td>Typed field values to create or overwrite.</td></tr><tr><td><code>unset</code></td><td>[<a href="../../core-api-reference/common.md#code">Code</a>!]</td><td>List of field codes to remove entirely.</td></tr></tbody></table>

`customFields` is always a patch operation: fields you don't mention are left unchanged. To update one field without touching others, include only that field in `set`. To remove a value entirely, list its code in `unset`. Each entry in `set` has a `code` and a `value`. Inside `value`, provide exactly one of its options, the one matching the field's declared type.

See [Defining and using custom fields](defining-and-using-custom-fields.md) for details on defining field definitions and the supported field types.

### Assigning assets to devices

Assets connect to devices through custom fields of the `DEVICE` type. There's no built-in device field: a device field is a custom field definition on the asset type. The preset types Workforce, Vehicle, and Cargo each have one optional device field, `cf_device`. On other types, you create device fields yourself, which means you control the field code, can have multiple device fields per type, and can mark one as primary. An asset with several device fields can hold several devices.

The `Asset` type has one shortcut field for the primary device. To read every linked device, request `customFields` and use an inline fragment on `DeviceCustomFieldValue`, the `... on` syntax shown in the verify step below.

<table><thead><tr><th width="169.88885498046875">Field</th><th width="126.333251953125">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>primaryDevice</code></td><td><a href="../api-reference/devices/#device">Device</a></td><td>The device whose <code>DEVICE</code>-type field is marked as primary. <code>null</code> if no primary device is set.</td></tr></tbody></table>

To assign a device, set the value of your `DEVICE`-type custom field. You can also mark it as primary if you wish:

```graphql
customFields: {
  set: [{
    code: "cf_tracker"
    value: { device: { id: "<device-id>", isPrimary: true } }
  }]
}
```

To unassign a device, remove the field. That clears its primary status too:

```graphql
customFields: {
  unset: ["cf_tracker"]
}
```

Here, `cf_tracker` is the code you chose when creating the `DEVICE`-type custom field definition for the asset type, not a fixed keyword. On the preset types, the code is `cf_device`. `isPrimary` is required on every `DEVICE` value, which prevents an update from removing the current primary mark by accident. Only one `DEVICE` field of an asset can be marked as primary.

The device must belong to your workspace. An ID from another workspace fails with a validation error reading `Device not found`, the same error as an ID that doesn't exist. If the field definition lists device types in `refSubtypeIds`, the device must have one of those types.

Each device can be linked to only one asset. Assigning a device that is already linked elsewhere fails with a validation error, unless you add `reassign: true`, which detaches it from the other asset and attaches it here in one step: either both happen or neither does.

The link works in both directions: `Device.asset` returns the asset a device is linked to. See [Managing device records and identifiers](managing-device-records-and-identifiers.md) for details.

## Example scenario: Registering a logistics fleet

TransLog GmbH is setting up their asset registry. They need to track both their delivery trucks (with GPS devices) and warehouse forklifts (without GPS devices). This scenario walks through creating an asset type, registering assets, and maintaining the registry over time.

{% stepper %}
{% step %}

### Create an asset type

Start by creating a "Delivery Truck" asset type for your workspace. Be careful when choosing the `code`, because it's immutable after creation and is used to reference this type in integrations and filters.

{% hint style="info" %}
`version` is optional. If you leave it out, an update always applies, even when someone else changed the record after you last read it. Include it to catch such conflicts, as the examples in this scenario do. See [Optimistic locking](../../optimistic-locking.md) for details.
{% endhint %}

Run this mutation:

```graphql
mutation CreateTruckType {
  bdr {
    assetTypeCreate(input: {
      code: "delivery_truck"
      title: "Delivery Truck"
      order: 10
      meta: {
        description: "Long-haul and last-mile delivery vehicles"
      }
    }) {
      assetType {
        id
        version
        code
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
      "assetTypeCreate": {
        "assetType": {
          "id": "019a6b2f-793e-807b-8001-434154500002",
          "version": 1,
          "code": "delivery_truck",
          "title": "Delivery Truck"
        }
      }
    }
  }
}
```

Save the `id`. You'll need it in the next step. You can also save `version` if you later need to update or delete the type.

{% hint style="info" %}
The `order` field controls how types appear in UI lists. Lower numbers appear first. If display order doesn't matter for your use case, omit it, and the position will be assigned automatically.
{% endhint %}
{% endstep %}

{% step %}

### Define custom fields

With the type created, add custom fields to be used by the assets of this type. Each delivery truck needs a license plate and a linked GPS device. Add both definitions in a single [assetTypeUpdate](../api-reference/assets/#assettypeupdate) call:

```graphql
mutation AddLicensePlateField {
  bdr {
    assetTypeUpdate(input: {
      id: "019a6b2f-793e-807b-8001-434154500002"
      version: 1
      customFieldDefinitions: [
        {
          create: {
            code: "cf_license_plate"
            title: "License Plate"
            fieldType: STRING
            params: { string: { isRequired: true } }
          }
        }
        {
          create: {
            code: "cf_tracker"
            title: "GPS Tracker"
            fieldType: DEVICE
            params: { device: { isRequired: false } }
          }
        }
      ]
    }) {
      assetType {
        id
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

Response:

```json
{
  "data": {
    "bdr": {
      "assetTypeUpdate": {
        "assetType": {
          "id": "019a6b2f-793e-807b-8001-434154500002",
          "version": 2,
          "customFieldDefinitions": [
            {
              "code": "cf_license_plate",
              "title": "License Plate",
              "fieldType": "STRING"
            },
            {
              "code": "cf_tracker",
              "title": "GPS Tracker",
              "fieldType": "DEVICE"
            }
          ]
        }
      }
    }
  }
}
```

Save the `version`. You'll need it if you later update or delete the type. The `code` values in `customFieldDefinitions` are exactly what you'll use as `code` in `customFields.set` when creating or updating assets of this type. The `cf_tracker` field is the one you'll use later to link a GPS device to the truck.

For the full list of supported field types and their parameters, see [Defining and using custom fields](defining-and-using-custom-fields.md).
{% endstep %}

{% step %}

### Create an asset

Create the first truck in the registry. Add custom field values to the `set` list inside `customFields`, one entry per field. In this example, the "Delivery Truck" type has a `cf_license_plate` field.

Run this mutation:

```graphql
mutation RegisterTruck {
  bdr {
    assetCreate(input: {
      typeId: "019a6b2f-793e-807b-8001-434154500002"
      title: "Truck B-44 (Hamburg–Berlin)"
      customFields: {
        set: [
          { code: "cf_license_plate", value: { string: "HH-TL 4421" } }
        ]
      }
    }) {
      asset {
        id
        version
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
      "assetCreate": {
        "asset": {
          "id": "019a6b2f-793e-807b-8001-415353540001",
          "version": 1,
          "title": "Truck B-44 (Hamburg–Berlin)"
        }
      }
    }
  }
}
```

Save the `id` and `version`. You'll need them for updates.
{% endstep %}

{% step %}

### Verify the asset

Query the asset to confirm it was created correctly:

```graphql
query GetTruck {
  bdr {
    asset(id: "019a6b2f-793e-807b-8001-415353540001") {
      id
      version
      title
      type {
        code
        title
      }
      customFields {
        code
        ... on StringCustomFieldValue { value }
        ... on DeviceCustomFieldValue {
          isPrimary
          device { id title }
        }
      }
      primaryDevice {
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
      "asset": {
        "id": "019a6b2f-793e-807b-8001-415353540001",
        "version": 1,
        "title": "Truck B-44 (Hamburg–Berlin)",
        "type": {
          "code": "delivery_truck",
          "title": "Delivery Truck"
        },
        "customFields": [
          {
            "code": "cf_license_plate",
            "value": "HH-TL 4421"
          }
        ],
        "primaryDevice": null
      }
    }
  }
}
```

`primaryDevice` is `null` because no GPS unit has been assigned yet. `customFields` returns a list of typed values, one per field, each carrying the `code` you use in `set` and `unset`. The element's type matches the field's declared `fieldType`, so select the fields you need through inline fragments (the `... on` blocks above) on [CustomFieldValue](../api-reference/custom-fields.md#customfieldvalue).

To keep the response clean, you can request only specific custom field codes:

```graphql
query GetTruckLicensePlate {
  bdr {
    asset(id: "019a6b2f-793e-807b-8001-415353540001") {
      title
      customFields(codes: ["cf_license_plate"]) {
        code
        ... on StringCustomFieldValue { value }
      }
    }
  }
}
```

{% endstep %}

{% step %}

### Assign a device

A GPS unit has been installed in the truck. To link it, you need a `DEVICE`-type custom field on the asset type. If you haven't created one yet, add it via `assetTypeUpdate` (see [Defining and using custom fields](defining-and-using-custom-fields.md)). In this example, the "Delivery Truck" type has a field with the code `cf_tracker`.

To learn how to create a device or find its id, see [Managing device records and identifiers](managing-device-records-and-identifiers.md).

Assign the device using `assetUpdate` with the device ID in `customFields.set`. `isPrimary` is required on every `DEVICE` value, so state explicitly whether this device is the asset's primary one:

```graphql
mutation AssignTruckDevice {
  bdr {
    assetUpdate(input: {
      id: "019a6b2f-793e-807b-8001-415353540001"
      version: 1
      customFields: {
        set: [
          {
            code: "cf_tracker"
            value: {
              device: {
                id: "019a6b2f-793e-807b-8001-444556430001"
                isPrimary: true
              }
            }
          }
        ]
      }
    }) {
      asset {
        id
        version
        primaryDevice {
          id
          title
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
      "assetUpdate": {
        "asset": {
          "id": "019a6b2f-793e-807b-8001-415353540001",
          "version": 2,
          "primaryDevice": {
            "id": "019a6b2f-793e-807b-8001-444556430001",
            "title": "GPS Unit #117"
          }
        }
      }
    }
  }
}
```

Note that version increments to `2` after a successful update. Use this new version for any further mutations.

If the device is already linked to another asset, this mutation fails with a validation error. Add `reassign: true` alongside `isPrimary` to detach it from the other asset and attach it here in one step.

To unassign the device (e.g., if the unit is removed for maintenance), use `unset`:

```graphql
mutation UnlinkTruckDevice {
  bdr {
    assetUpdate(input: {
      id: "019a6b2f-793e-807b-8001-415353540001"
      version: 2
      customFields: {
        unset: ["cf_tracker"]
      }
    }) {
      asset {
        id
        version
        primaryDevice { id }
      }
    }
  }
}
```

After unlinking, `primaryDevice` returns `null`. Removing the field with `unset` clears its primary status at the same time, so there's no separate call for that.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You now have an asset type with its custom field definitions, an asset created from that type and read back to verify it, and a GPS device assigned to the asset as its primary device.
{% endhint %}

## Listing assets

To list all assets for a workspace, run the following query:

```graphql
query ListAssets {
  bdr {
    assets(
      first: 20
    ) {
      nodes {
        id
        title
        type {
          code
          title
        }
      }
      pageInfo {
        hasNextPage
        endCursor
      }
    }
  }
}
```

### Filtering

Use [AssetFilter](../api-reference/assets/#assetfilter) to narrow down results by type, title, or custom field values. Conditions across different fields are combined with AND, while multiple values within a single field are combined with OR. For the full filter field reference and custom field filter operators, see [Custom field filtering and sorting](../../filtering-and-sorting/custom-field-filtering.md#operators).

```graphql
query ListTrucks {
  bdr {
    assets(
      filter: {
        typeIds: ["019a6b2f-793e-807b-8001-434154500002"]
        titleContains: "hamburg"
      }
      first: 20
    ) {
      nodes {
        id
        title
      }
    }
  }
}
```

To find the asset linked to a specific GPS device, query the device's `asset` field instead of filtering assets by device. See [Assigning assets to devices](creating-assets-and-assigning-devices.md#assigning-assets-to-devices). The reference page still lists a `deviceIds` field on `AssetFilter`. The API accepts it but ignores it today, so filtering by device returns every asset.

To filter assets by a custom field value, add conditions to the `customFields` list in the filter. Each condition specifies a field `code`, a comparison `operator`, and a `value`. The following query finds all delivery trucks with a specific license plate:

```graphql
query FindTruckByPlate {
  bdr {
    assets(
      filter: {
        typeIds: ["019a6b2f-793e-807b-8001-434154500002"]
        customFields: [
          { code: "cf_license_plate", operator: EQ, value: { string: "HH-TL 4421" } }
        ]
      }
      first: 5
    ) {
      nodes {
        id
        title
        customFields(codes: ["cf_license_plate"]) {
          code
          ... on StringCustomFieldValue { value }
        }
      }
    }
  }
}
```

Multiple conditions in the `customFields` array are combined with AND.

### Ordering

Assets can be ordered by title (the default) or by a custom field using `customFieldCode`. Not every field type is sortable — see [Sorting by custom fields](../../filtering-and-sorting/custom-field-filtering.md#sorting-by-custom-fields) for the supported list:

```graphql
query AssetsByLicensePlate {
  bdr {
    assets(
      orderBy: { customFieldCode: "cf_license_plate", direction: ASC }
      first: 20
    ) {
      nodes {
        id
        title
        customFields(codes: ["cf_license_plate"]) {
          code
          ... on StringCustomFieldValue { value }
        }
      }
    }
  }
}
```

{% hint style="warning" %}
Send either `field` (an `AssetOrderField` enum) or `customFieldCode`, not both. The API doesn't reject a request with both, and `customFieldCode` takes precedence then. Valid values for `field` are defined in the [AssetOrderField enum](../api-reference/assets/#assetorderfield).
{% endhint %}

For details on pagination, see [Pagination](../../pagination.md).

## Handling version conflicts

If you include `version` in your mutation and the entity has been modified since you last fetched it, the API returns a [conflict error](../../error-handling.md#version-conflict-409):

```json
{
  "errors": [
    {
      "message": "Version conflict: expected 1, found 2",
      "path": ["bdr", "assetUpdate"],
      "extensions": {
        "type": "https://api.navixy.com/errors/conflict",
        "title": "Concurrent Modification Conflict",
        "status": 409,
        "detail": "Version conflict: expected 1, found 2",
        "instance": "/graphql",
        "code": "CONFLICT",
        "field": "input.version",
        "expectedVersion": 1,
        "currentVersion": 2,
        "traceId": "0af7651916cd43dd8448eb211c80319c",
        "timestamp": "2025-01-15T14:30:00Z"
      }
    }
  ]
}
```

To resolve this, query the asset to get its current version and state, merge your intended changes, and retry the mutation with the updated version.

For a full explanation of how versioning works, see [Optimistic locking](../../optimistic-locking.md).

## Deleting an asset

{% hint style="danger" %}
Asset deletion is permanent for you: no operation restores a deleted asset. Make sure you no longer need the record before proceeding.
{% endhint %}

While an asset group lists the asset among its current assets, `assetDelete` is refused with [`IN_USE`](../../error-handling.md#in-use-409), and `extensions.blockers` names the groups. Remove the asset from them with `assetGroupItemsRemove` first. Past memberships don't block the delete. A `REFERENCE` field of another asset or geo object that names this asset refuses the delete too, with the path `customFields.<code>`; an asset that names itself doesn't.

When the truck is decommissioned and you no longer need its record, run the [assetDelete](../api-reference/assets/#assetdelete) mutation:

```graphql
mutation DecommissionTruck {
  bdr {
    assetDelete(input: {
      id: "019a6b2f-793e-807b-8001-415353540001"
      version: 3
    }) {
      deletedId
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assetDelete": {
        "deletedId": "019a6b2f-793e-807b-8001-415353540001"
      }
    }
  }
}
```

Including `version` ensures you don't accidentally delete an asset that someone else has modified. It's optional, but recommended. For more information on versioning, see [Optimistic locking](../../optimistic-locking.md).

## See also

- [Assets](../api-reference/assets/): Complete reference for all asset operations and types
- [Organizing assets into groups](organizing-assets-into-groups.md): Collect assets into named groups by depot, project, or any other dimension
- [Defining and using custom fields](defining-and-using-custom-fields.md): Define custom fields and store your own data on entities
