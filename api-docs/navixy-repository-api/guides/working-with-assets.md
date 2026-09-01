---
description: "Create and manage assets: vehicles, equipment, and other tracked objects."
---

# Working with assets

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

Assets in Navixy Repository API represent the objects your workspace tracks and manages. The most common example is a vehicle, but assets can be anything you need to monitor: construction equipment, forklifts, generators, shipping containers, leased machinery, or fixed infrastructure. If your workspace has a reason to record it, assign attributes to it, or link a GPS device to it, it's a good candidate for an asset.

Each asset is defined by the **asset type**, which acts as a template: it classifies the asset and determines which custom fields are available for it. For example, a "Delivery Truck" type might have fields for license plate, fuel capacity, and assigned driver, while a "Generator" type might have fields for power output, last service date, and installation site.

To organize assets into named collections, such as grouping vehicles by depot or equipment by project, see [Organizing assets into groups](organizing-assets-into-groups.md).

## Prerequisites

You need your workspace's ID for all asset operations. It comes with your access credentials and is carried in your access token. See [Authentication](../authentication.md) for how tokens work and where the workspace ID comes from.

### Check available asset types

Before creating an asset, request the available asset types for your workspace:

```graphql
query ListAssetTypes {
  bdr {
    assetTypes(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
    ) {
      nodes {
        id
        code
        title
      }
    }
  }
}
```

You'll get an array of types, if any exist:

```json
{
  "data": {
    "bdr": {
      "assetTypes": {
        "nodes": [
          {
            "id": "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11",
            "code": "VEHICLE",
            "title": "Vehicle"
          }
        ]
      }
    }
  }
}
```

If you need a type that doesn't exist yet, you can create it as described in [the scenario below](working-with-assets.md#create-an-asset-type).

If you're working with an existing type and need to know which custom fields it has, query [customFieldDefinitions](../custom-fields.md#customfielddefinition) on the type:

```graphql
query GetTruckTypeFields {
  bdr {
    assetTypes(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
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
            "id": "b1ffcd00-0d1c-5f29-cc7e-7cc0ce491b22",
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

The `code` values here are exactly what you use as `code` in `customFields.set` entries when creating or updating assets of this type. For type-specific parameters like maximum string length or the list of valid options, see [Implementing custom fields](implementing-custom-fields.md).

## How assets work

### Asset types

An asset type classifies assets and defines which custom fields they have. It is a [catalog item](../catalogs/catalog-items.md), so it combines the common catalog item fields with one field of its own, `customFieldDefinitions`:

| Field | What it holds |
| --- | --- |
| `code` | A stable machine-readable identifier ([Code](../common.md#code)). Integrations and filters use it to reference the type. |
| `title` | The display name shown in UIs. |
| `meta` | UI and lifecycle properties: `description`, `origin`, `canBeDeleted`, and `hidden`. |
| `customFieldDefinitions` | All custom fields available on assets of this type. |
| `workspace` | The workspace that owns the type. `null` for system types. |

{% hint style="warning" %}
Before creating a type, remember that `code` is immutable after creation. Choose it carefully, since it's what integrations and filters will use to reference the type.
{% endhint %}

{% hint style="info" %}
Before deleting an asset type, check `meta.canBeDeleted`. The API rejects deletion if the type still has dependent assets or is system-managed. Query `meta { canBeDeleted }` on the type to verify before calling `assetTypeDelete`.
{% endhint %}

Types come from one of two places, and the `meta.origin` field on the type says which: predefined by the platform (`SYSTEM`) or created by your workspace (`WORKSPACE`). You can only create, update, and delete types with `WORKSPACE` origin, because system types are read-only. The `workspace` field on an asset type is `null` for `SYSTEM`-origin types, since no single workspace owns them.

For the full field reference, see [AssetType](../assets/README.md#assettype).

### Asset fields

An asset has a `title`, belongs to a workspace, and is classified by an asset type. Everything else it stores lives in its custom fields:

| Field | What it holds |
| --- | --- |
| `title` | The display name. |
| `workspace` | The workspace that owns the asset. |
| `type` | The asset type that classifies the asset and defines its custom fields. |
| `customFields` | The stored custom field values, returned as a list of typed values. |
| `primaryDevice` | The linked device marked as primary. `null` if no primary device is set. |
| `groups` | A paginated list of the asset groups the asset belongs to, with optional filtering and ordering arguments. |

For the full field reference, see [Asset object](../assets/README.md#asset).

### Custom fields

Assets store your own data, such as a license plate or a fuel capacity, in the `customFields` field. When creating or updating an asset, you describe custom field changes with [CustomFieldsPatchInput](../custom-fields.md#customfieldspatchinput), which has two sub-fields:

<table><thead><tr><th width="136.111083984375">Field</th><th width="180">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>set</code></td><td>[<a href="../custom-fields.md#customfieldvalueinput">CustomFieldValueInput</a>!]</td><td>Typed field values to create or overwrite.</td></tr><tr><td><code>unset</code></td><td>[<a href="../common.md#code">Code</a>!]</td><td>List of field codes to remove entirely.</td></tr></tbody></table>

`customFields` is always a patch operation: fields you don't mention are left unchanged. To update one field without touching others, include only that field in `set`. To remove a value entirely, list its code in `unset`. Each entry in `set` has a `code` and a `value`. Inside `value`, provide exactly one of its options, the one matching the field's declared type.

See [Implementing custom fields](implementing-custom-fields.md) for details on defining field definitions and the supported field types.

### Linking devices

Assets connect to devices through custom fields of `DEVICE` type. Unlike the built-in `geojson_data` field, device fields are user-defined: you create them as custom field definitions for the asset type, which means you control the field code, can have multiple device fields per type, and can mark one as primary.

The `Asset` type has one shortcut field for the primary device. To read every linked device, request `customFields` and use an inline fragment on `DeviceCustomFieldValue`, the `... on` syntax shown in the verify step below.

<table><thead><tr><th width="169.88885498046875">Field</th><th width="126.333251953125">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>primaryDevice</code></td><td><a href="../devices/README.md#device">Device</a></td><td>The device whose <code>DEVICE</code>-type field is marked as primary. <code>null</code> if no primary device is set.</td></tr></tbody></table>

To link a device, set the value of your `DEVICE`-type custom field. You can also mark it as primary if you wish:

```graphql
customFields: {
  set: [{
    code: "cf_tracker"
    value: { device: { id: "<device-id>", isPrimary: true } }
  }]
}
```

To unlink a device, remove the field. That clears its primary status too:

```graphql
customFields: {
  unset: ["cf_tracker"]
}
```

Here, `cf_tracker` is the code you chose when creating the `DEVICE`-type custom field definition for the asset type, not a fixed keyword. `isPrimary` is required on every `DEVICE` value, which prevents an update from removing the current primary mark by accident.

Each device can be linked to only one asset. Assigning a device that is already linked elsewhere fails with a validation error, unless you add `reassign: true`, which detaches it from the other asset and attaches it here in one step: either both happen or neither does.

The link works in both directions: `Device.asset` returns the asset a device is linked to. See [Working with devices](working-with-devices.md) for details.

## Example scenario: Registering a logistics fleet

TransLog GmbH is setting up their asset registry. They need to track both their delivery trucks (with GPS devices) and warehouse forklifts (without GPS devices). This scenario walks through creating an asset type, registering assets, and maintaining the registry over time.

{% stepper %}
{% step %}
### Create an asset type

Start by creating a "Delivery Truck" asset type for your workspace. Be careful when choosing the `code`, because it's immutable after creation and is used to reference this type in integrations and filters.

{% hint style="info" %}
`version` is optional. If you leave it out, an update always applies, even when someone else changed the record after you last read it. Include it to catch such conflicts, as the examples in this scenario do. See [Optimistic locking](../optimistic-locking.md) for details.
{% endhint %}

Run this mutation:

```graphql
mutation CreateTruckType {
  bdr {
    assetTypeCreate(input: {
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
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
          "id": "b1ffcd00-0d1c-5f29-cc7e-7cc0ce491b22",
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

With the type created, add custom fields to be used by the assets of this type. Each delivery truck needs a license plate and a linked GPS device. Add both definitions in a single [assetTypeUpdate](../assets/README.md#assettypeupdate) call:

```graphql
mutation AddLicensePlateField {
  bdr {
    assetTypeUpdate(input: {
      id: "b1ffcd00-0d1c-5f29-cc7e-7cc0ce491b22"
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
          "id": "b1ffcd00-0d1c-5f29-cc7e-7cc0ce491b22",
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

For the full list of supported field types and their parameters, see [Implementing custom fields](implementing-custom-fields.md).
{% endstep %}

{% step %}
### Create an asset

Create the first truck in the registry. Add custom field values to the `set` list inside `customFields`, one entry per field. In this example, the "Delivery Truck" type has a `cf_license_plate` field.

Run this mutation:

```graphql
mutation RegisterTruck {
  bdr {
    assetCreate(input: {
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      typeId: "b1ffcd00-0d1c-5f29-cc7e-7cc0ce491b22"
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
          "id": "019a6b2f-793e-807b-8001-555345529b44",
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
    asset(id: "019a6b2f-793e-807b-8001-555345529b44") {
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
        "id": "019a6b2f-793e-807b-8001-555345529b44",
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

`primaryDevice` is `null` because no GPS unit has been assigned yet. `customFields` returns a list of typed values, one per field, each carrying the `code` you use in `set` and `unset`. The element's type matches the field's declared `fieldType`, so select the fields you need through inline fragments (the `... on` blocks above) on [CustomFieldValue](../custom-fields.md#customfieldvalue).

To keep the response clean, you can request only specific custom field codes:

```graphql
query GetTruckLicensePlate {
  bdr {
    asset(id: "019a6b2f-793e-807b-8001-555345529b44") {
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

A GPS unit has been installed in the truck. To link it, you need a `DEVICE`-type custom field on the asset type. If you haven't created one yet, add it via `assetTypeUpdate` (see [Implementing custom fields](implementing-custom-fields.md)). In this example, the "Delivery Truck" type has a field with the code `cf_tracker`.

To learn how to create a device or find its id, see [Working with devices](working-with-devices.md).

Assign the device using `assetUpdate` with the device ID in `customFields.set`. `isPrimary` is required on every `DEVICE` value, so state explicitly whether this device is the asset's primary one:

```graphql
mutation AssignTruckDevice {
  bdr {
    assetUpdate(input: {
      id: "019a6b2f-793e-807b-8001-555345529b44"
      version: 1
      customFields: {
        set: [
          {
            code: "cf_tracker"
            value: {
              device: {
                id: "c3aaef22-2f3e-6ab1-ee9c-9ee2ec713d44"
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
          "id": "019a6b2f-793e-807b-8001-555345529b44",
          "version": 2,
          "primaryDevice": {
            "id": "c3aaef22-2f3e-6ab1-ee9c-9ee2ec713d44",
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
mutation UnlinkForkliftDevice {
  bdr {
    assetUpdate(input: {
      id: "029b7c40-804f-918c-9112-666456630d55"
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

{% step %}
### Delete the asset

{% hint style="danger" %}
Asset deletion is permanent. Unlike some other entity types in the API, assets don't support soft delete and cannot be restored after deletion. Make sure you no longer need the record before proceeding.
{% endhint %}

When the truck is decommissioned and you no longer need its record, run the [assetDelete](../assets/README.md#assetdelete) mutation:

```graphql
mutation DecommissionTruck {
  bdr {
    assetDelete(input: {
      id: "019a6b2f-793e-807b-8001-555345529b44"
      version: 2
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
        "deletedId": "019a6b2f-793e-807b-8001-555345529b44"
      }
    }
  }
}
```

Including `version` ensures you don't accidentally delete an asset that someone else has modified. It's optional, but recommended. For more information on versioning, see [Optimistic locking](../optimistic-locking.md).
{% endstep %}
{% endstepper %}

## Listing assets

To list all assets for a workspace, run the following query:

```graphql
query ListAssets {
  bdr {
    assets(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
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

Use [AssetFilter](../assets/README.md#assetfilter) to narrow down results by type, linked GPS device, title, or custom field values. Conditions across different fields are combined with AND, while multiple values within a single field are combined with OR. For the full filter field reference and custom field filter operators, see [Custom field filtering and sorting](../custom-field-filtering.md#operators).

```graphql
query ListTrucks {
  bdr {
    assets(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: {
        typeIds: ["b1ffcd00-0d1c-5f29-cc7e-7cc0ce491b22"]
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

To find all assets linked to a specific GPS device, run this query:

```graphql
query FindAssetByDevice {
  bdr {
    assets(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: {
        deviceIds: ["c3aaef22-2f3e-6ab1-ee9c-9ee2ec713d44"]
      }
      first: 5
    ) {
      nodes {
        id
        title
        primaryDevice {
          id
          title
        }
      }
    }
  }
}
```

To filter assets by a custom field value, add conditions to the `customFields` list in the filter. Each condition specifies a field `code`, a comparison `operator`, and a `value`. The following query finds all delivery trucks with a specific license plate:

```graphql
query FindTruckByPlate {
  bdr {
    assets(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: {
        typeIds: ["b1ffcd00-0d1c-5f29-cc7e-7cc0ce491b22"]
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

Assets can be ordered by title (the default) or by a custom field using `customFieldCode`. Not every field type is sortable — see [Sorting by custom fields](../custom-field-filtering.md#sorting-by-custom-fields) for the supported list:

```graphql
query AssetsByLicensePlate {
  bdr {
    assets(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
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
`field` (an `AssetOrderField` enum) and `customFieldCode` can't be used together: pick one. Valid values for `field` are defined in the [AssetOrderField enum](../assets/README.md#assetorderfield).
{% endhint %}

For details on pagination, see [Pagination](../pagination.md).

## Handling version conflicts

If you include `version` in your mutation and the entity has been modified since you last fetched it, the API returns a [conflict error](../error-handling.md#version-conflict-409):

```json
{
  "errors": [
    {
      "message": "Entity has been modified by another request",
      "path": ["bdr", "assetUpdate"],
      "extensions": {
        "type": "https://api.navixy.com/errors/conflict",
        "title": "Optimistic Lock Conflict",
        "status": 409,
        "code": "CONFLICT",
        "entityType": "Asset",
        "entityId": "019a6b2f-793e-807b-8001-555345529b44",
        "expectedVersion": 1,
        "currentVersion": 2,
        "traceId": "0af7651916cd43dd8448eb211c80319c"
      }
    }
  ]
}
```

To resolve this, query the asset to get its current version and state, merge your intended changes, and retry the mutation with the updated version.

For a full explanation of how versioning works, see [Optimistic locking](../optimistic-locking.md).

## See also

* [Assets](../assets/): Complete reference for all asset operations and types
* [Organizing assets into groups](organizing-assets-into-groups.md): Collect assets into named groups by depot, project, or any other dimension
* [Implementing custom fields](implementing-custom-fields.md): Define custom fields and store your own data on entities
