---
description: Register and manage GPS trackers, sensors, and other hardware devices.
---

# Working with devices

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

A device in Navixy Repository API represents a physical hardware unit: a GPS tracker, sensor, beacon, or any other piece of trackable hardware. Devices hold the identifying information, such as IMEI numbers and serial numbers, that connects physical hardware to the rest of your platform data. When linked to an asset through a `DEVICE`-type custom field, a device lets tracking data flow from the hardware into the platform. The link works in both directions: on the asset, `primaryDevice` returns the primary device and `customFields` returns every linked device, while on the device, the `asset` field returns the asset it belongs to.

This guide covers the full device lifecycle: looking up the required catalog records, registering a device, managing its identifiers, creating device relations, updating device properties, and decommissioning it.

## Prerequisites

You need your workspace's ID for all device operations. Use the `me` query to retrieve it:

```graphql
query GetMyWorkspace {
  bdr {
    me {
      ... on User {
        memberships {
          nodes {
            workspace {
              id
              title
            }
          }
        }
      }
    }
  }
}
```

You'll receive a response:

```json
{
  "data": {
    "bdr": {
      "me": {
        "memberships": {
          "nodes": [
            {
              "workspace": {
                "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
                "title": "TransLog GmbH"
              }
            }
          ]
        }
      }   
    }
  }
}
```

Use the `id` of the workspace you want to work with for all subsequent device operations.

You also need IDs for two catalog records before you create a device:

- Device type: a classification you define (for example, "GPS Tracker" or "Sensor")
- Device model: the specific hardware model taken from the read-only model catalog

To check what device types already exist in your workspace, run this query:

```graphql
query GetDeviceTypes {
  bdr {
    deviceTypes(workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7") {
      nodes { id title code }
    }
  }
}
```

If no device types exist yet, create the ones you need:

```graphql
mutation CreateDeviceType {
  bdr {
    deviceTypeCreate(input: {
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      title: "GPS Tracker"
      code: "gps_tracker"
    }) {
      deviceType { id code }
    }
  }
}
```

{% hint style="info" %}
Device statuses are system-managed. Three built-in statuses (`Not Activated`, `Active`, and `Inactive`) are provided by the platform and available in every workspace. Every new device starts with `Not Activated`. Query `deviceStatuses` to get their IDs for use in filters. You can also create additional custom statuses alongside the built-in ones.
{% endhint %}

To look up a hardware model, query the read-only model catalog. You can filter by title or vendor:

```graphql
query FindDeviceModel {
  bdr {
    deviceModels(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: { titleContains: "FMB003" }
    ) {
      nodes { id title code vendor { id title } }
    }
  }
}
```

If you want to browse models by manufacturer first, use the `deviceVendors` query to get vendor IDs, then filter `deviceModels` by them using `filter.vendorIds`.

Save the returned IDs. You'll need both to create a device.

## How devices work

### Device identifiers

Each device can have one or more hardware identifiers that connect the platform record to the physical hardware. Telematics servers and other systems use these identifiers to look up a device.

The available identifier types are defined by the [DeviceIdType](../devices/README.md#enums) enum:

<table><thead><tr><th width="198">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>IMEI</code></td><td>15-digit International Mobile Equipment Identity</td></tr><tr><td><code>SERIAL_NUMBER</code></td><td>Manufacturer-assigned serial number</td></tr><tr><td><code>MAC_ADDRESS</code></td><td>Network interface MAC address</td></tr><tr><td><code>GUID</code></td><td>GUID/UUID identifier</td></tr><tr><td><code>MEID_HEX</code></td><td>Mobile Equipment Identifier, hexadecimal format</td></tr><tr><td><code>MEID_DEC</code></td><td>Mobile Equipment Identifier, decimal format</td></tr><tr><td><code>CUSTOM</code></td><td>Workspace-defined identifier</td></tr></tbody></table>

An identifier must be unique. Uniqueness is checked on the combination of `type`, `value`, and `namespace`, and the `value` comparison is case-insensitive. For standard hardware identifier types (`IMEI`, `SERIAL_NUMBER`, `MAC_ADDRESS`, etc.), `namespace` is always `null`, so the value must be unique across the entire platform. For `CUSTOM` identifiers, you can set a `namespace` string to limit where the value must be unique: two devices can share the same custom value as long as their namespaces differ. This is useful when integrating with external systems that have their own ID schemes and whose values may overlap.

### Device status and properties

Devices don't support [custom fields](implementing-custom-fields.md). The only properties you can change are `title` and `modelId`. Device status is controlled by the platform: every new device starts with the system status `Not Activated`. The platform provides three built-in statuses (`Not Activated`, `Active`, `Inactive`) visible to all workspaces. You can create additional custom statuses with [deviceStatusCreate](../devices/README.md#devicestatuscreate), but status cannot be changed through the public API.

#### Asset link

The `asset` field returns the linked asset as a full [Asset](../assets/README.md#asset) object, so you can query any of its fields directly, or `null` if the device isn't assigned to any asset. This is the same link seen from the device side: assets link to devices through custom fields of type `DEVICE`, and `Device.asset` follows that link back. The link is managed entirely from the asset side. See [Working with assets](working-with-assets.md) for details.

### Device relations

Two devices can be linked with a typed relation. Relation types are system-defined, so your workspace can't create new ones. To see what types are available, use the `deviceRelationTypes` query:

```graphql
query GetRelationTypes {
  bdr {
    deviceRelationTypes(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
    ) {
      nodes { id title code }
    }
  }
}
```

A relation has two endpoints: `first` and `second`. On the device the relation was created from (`first`), the relation appears in `relationsFrom`. On the device at the other end (`second`), the same relation appears in `relationsTo`. The `first`/`second` distinction only records the direction the relation was created in. The platform attaches no meaning to it, so your application decides what it means.

## Example scenario: Onboarding a tracker shipment

FleetOps Ltd receives a shipment of Teltonika FMB003 GPS trackers for installation across their delivery fleet. This scenario walks through registering a device, recording its identifiers, linking it to a companion beacon, updating its status once installed, and decommissioning it at end of life.

{% stepper %}
{% step %}
### Create a device

Register the first device using the type and model IDs from the prerequisites step

{% hint style="info" %}
`version` is optional. If you leave it out, an update always applies, even when someone else changed the record after you last read it. Include it to catch such conflicts, as the examples in this scenario do. See [Optimistic locking](../optimistic-locking.md) for details.
{% endhint %}

Run this mutation:

```graphql
mutation RegisterDevice {
  bdr {
    deviceCreate(input: {
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      typeId: "b8e3c1f0-1d2a-4e5b-9c8d-111222333444"
      modelId: "c9f4d2e1-2e3b-5f6c-ad9e-222333444555"
      title: "FMB003 — Unit 001"
      identifiers: [
        { type: IMEI, value: "356307042772396" }
      ]
    }) {
      device {
        id
        version
        title
        type { title }
        model { title vendor { title } }
        status { title }
        identifiers { id type value namespace }
        asset { id title }
      }
    }
  }
}
```

Identifiers are optional: a device may be registered before its hardware is known, and identifiers can be added later with `deviceIdentifierAdd`.

{% hint style="info" %}
`title` is optional. When omitted or blank, the server generates `"<vendor> <model> <identifier value>"`, for example `"Teltonika FMB003 356307042772396"`. The identifier is chosen by type priority (`IMEI`, then `SERIAL_NUMBER`, then `MAC_ADDRESS`), falling back to the first one in the list. With no identifiers at all, the title is `"<vendor> <model>"`. Provide an explicit title when the default isn't descriptive enough.
{% endhint %}

Response:

```json
{
  "data": {
    "bdr": {
      "deviceCreate": {
        "device": {
          "id": "e1b6f4a3-4a5d-7b8e-cf10-444555666777",
          "version": 1,
          "title": "FMB003 — Unit 001",
          "type": { "title": "GPS Tracker" },
          "model": { "title": "FMB003", "vendor": { "title": "Teltonika" } },
          "status": { "title": "Not Activated" },
          "identifiers": [
            {
            "id": "f2c7a5b4-5b6e-8c9f-d011-555666777888",
            "type": "IMEI",
            "value": "356307042772396",
            "namespace": null
            }
          ],
          "asset": null
        }
      }
    }
  }
}
```

Save the `id` and `version`. You'll need both for later operations.

{% hint style="info" %}
If any identifier already exists on another device, [deviceCreate](../devices/README.md#devicecreate) fails and the device is not created.
{% endhint %}
{% endstep %}

{% step %}
### Add more identifiers

The IMEI was provided at creation. To register additional identifiers, such as a serial number, use [deviceIdentifierAdd](../devices/README.md#deviceidentifieradd):

```graphql
mutation AddSerialNumber {
  bdr {
    deviceIdentifierAdd(input: {
      deviceId: "e1b6f4a3-4a5d-7b8e-cf10-444555666777"
      identifier: {
        type: SERIAL_NUMBER
        value: "TLT-2024-00391"
      }
    }) {
      deviceIdentifier { id type value namespace }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "deviceIdentifierAdd": {
        "deviceIdentifier": {
          "id": "a3d8b6c5-6c7f-9d0a-e122-666777888999",
          "type": "SERIAL_NUMBER",
          "value": "TLT-2024-00391",
          "namespace": null
        }
      }
    }
  }
}
```

Both identifiers have `namespace: null`, so each value must be unique across the whole platform. Registering the same IMEI on another device returns a `409 DUPLICATE` error.

To correct a mistakenly entered identifier, remove it using its `id`:

```graphql
mutation RemoveIdentifier {
  bdr {
    deviceIdentifierRemove(input: {
      identifierId: "f2c7a5b4-5b6e-8c9f-d011-555666777888"
    }) {
      deletedId
    }
  }
}
```
{% endstep %}

{% step %}
### Assign device to an asset

FMB003 Unit 001 will track delivery truck DE-1049. To link the device, update the asset's `DEVICE`-type custom field. This example assumes the asset's type already has a custom field definition of the `DEVICE` type. See [Implementing custom fields](implementing-custom-fields.md) for instructions on setting it up.

```graphql
mutation AssignDeviceToAsset {
  bdr {
    assetUpdate(input: {
      id: "a4c9d5e6-6d8f-4a1b-b234-777888999000"
      version: 1
      customFields: {
        set: [
          {
            code: "cf_tracker"
            value: {
              device: {
                id: "e1b6f4a3-4a5d-7b8e-cf10-444555666777"
                isPrimary: true
              }
            }
          }
        ]
      }
    }) {
      asset {
        id
        title
        primaryDevice { id title }
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
          "id": "a4c9d5e6-6d8f-4a1b-b234-777888999000",
          "title": "Truck DE-1049",
          "primaryDevice": {
            "id": "e1b6f4a3-4a5d-7b8e-cf10-444555666777",
            "title": "FMB003 — Unit 001"
          }
        }
      }
    }
  }
}
```

{% hint style="warning" %}
The `code` in `set` is the code of the `DEVICE`-type custom field defined on the asset type, not a fixed keyword. `isPrimary` is required on every `DEVICE` value, and setting it to `true` makes the device available through `Asset.primaryDevice`.
{% endhint %}

You can verify the link from the device side:

```graphql
query CheckAssetLink {
  bdr {
    device(id: "e1b6f4a3-4a5d-7b8e-cf10-444555666777") {
      id
      title
      asset { id title }
    }
  }
}
```

Each device can be linked to only one asset. Assigning a device that is already linked to a different asset fails with a validation error, unless you add `reassign: true` to the `device` value, which detaches it from the other asset and attaches it here in one step.

To unlink the device later without deleting it, use `unset` on the asset. That clears its primary status at the same time:

```graphql
customFields: {
  unset: ["cf_tracker"]
}
```
{% endstep %}

{% step %}
### Create a device relation

FMB003 Unit 001 will be installed in the same vehicle as a Teltonika EYE beacon used for cargo monitoring. Link the two devices with a relation.

Start by looking up the available relation types for your workspace:

```graphql
query GetRelationTypes {
  bdr {
    deviceRelationTypes(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
    ) {
      nodes { id title code }
    }
  }
}
```

Then create the relation using the IDs of both devices and the relation type you want:

```graphql
mutation LinkDevices {
  bdr {
    deviceRelationCreate(input: {
      firstId: "e1b6f4a3-4a5d-7b8e-cf10-444555666777"
      secondId: "b4e9c7d6-7d8a-0e1b-f233-777888999aaa"
      typeId: "c5fad8e7-8e9b-1f2c-a344-888999aaabbb"
    }) {
      deviceRelation {
        id
        first { id title }
        second { id title }
        type { title }
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
      "deviceRelationCreate": {
        "deviceRelation": {
          "id": "d6abe9f8-9f0c-2a3d-b455-999aaabbbccc",
          "first": {
            "id": "e1b6f4a3-4a5d-7b8e-cf10-444555666777",
            "title": "FMB003 — Unit 001"
          },
          "second": {
            "id": "b4e9c7d6-7d8a-0e1b-f233-777888999aaa",
            "title": "EYE Beacon — Unit 001"
          },
          "type": { "title": "Primary/Accessory" }
        }
      }
    }
  }
}
```

To remove the relation when the devices are separated, use `deviceRelationRemove` with the relation's `id`:

```graphql
mutation UnlinkDevices {
  bdr {
    deviceRelationRemove(input: {
      id: "d6abe9f8-9f0c-2a3d-b455-999aaabbbccc"
    }) {
      deletedId
    }
  }
}
```
{% endstep %}

{% step %}
### Update a device

When Unit 001 is installed in a delivery truck, update its title to reflect the assignment. You can also update the model in the same mutation.

```graphql
mutation DeployDevice {
  bdr {
    deviceUpdate(input: {
      id: "e1b6f4a3-4a5d-7b8e-cf10-444555666777"
      version: 1
      title: "FMB003 — Unit 001 (Truck 14)"
    }) {
      device {
        id
        version
        title
        status { title }
        asset { id title }
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
      "deviceUpdate": {
        "device": {
          "id": "e1b6f4a3-4a5d-7b8e-cf10-444555666777",
          "version": 2,
          "title": "FMB003 — Unit 001 (Truck 14)",
          "status": { "title": "Not Activated" },
          "asset": {
            "id": "a4c9d5e6-6d8f-4a1b-b234-777888999000",
            "title": "Truck DE-1049"
          }
        }
      }
    }
  }
}
```

{% hint style="info" %}
Providing `version` turns on optimistic locking: if the device changed since you last fetched it, the API returns a [409 Conflict](../error-handling.md#version-conflict-409) error instead of overwriting the change without warning. Without `version`, the update always applies. See [Handling version conflicts](#handling-version-conflicts) for details.
{% endhint %}
{% endstep %}

{% step %}
### Delete a device

When a tracker reaches end of life and needs to be permanently removed, use `deviceDelete`.

Deleting a device automatically detaches it from every `DEVICE`-type custom field that points at it, on assets and geo objects alike, in the same transaction as the delete. The one exception is a required field: if any `DEVICE` field holding this device is `isRequired`, the delete is rejected with a [validation error](../error-handling.md#validation-error-400). In that case, unlink the device first by updating the asset's custom fields:

```graphql
mutation UnlinkBeforeDelete {
  bdr {
    assetUpdate(input: {
      id: "a4c9d5e6-6d8f-4a1b-b234-777888999000"
      version: 2
      customFields: {
        unset: ["cf_tracker"]
      }
    }) {
      asset { id primaryDevice { id } }
    }
  }
}
```

Then delete the device:

```graphql
mutation DecommissionDevice {
  bdr {
    deviceDelete(input: {
      id: "e1b6f4a3-4a5d-7b8e-cf10-444555666777"
      version: 2
    }) {
      deletedId
    }
  }
}
```

{% hint style="warning" %}
A device delete can fail because of a required `DEVICE` field on an entirely different entity. The validation error names the field, so unlink the device there and retry.
{% endhint %}
{% endstep %}
{% endstepper %}

## Listing devices

To list all devices in a workspace:

```graphql
query ListDevices {
  bdr {
    devices(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      first: 20
    ) {
      nodes {
        id
        title
        type { title }
        model { title vendor { title } }
        status { title }
        identifiers { type value namespace }
        asset { id title }
      }
      pageInfo {
        hasNextPage
        endCursor
      }
    }
  }
}
```

## Filtering

Use [DeviceFilter](../devices/README.md#devicefilter) to narrow down results. Conditions across different fields are combined with AND, while multiple values within a single field are combined with OR. For the full filter reference, see [Filtering and sorting](../filtering-and-sorting.md).

Common filter combinations:

```graphql
query FindActiveTrackers {
  bdr {
    devices(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: {
        typeIds: ["b8e3c1f0-1d2a-4e5b-9c8d-111222333444"]
        statusIds: ["e2c8f5a4-5a6e-8b9f-c010-444555666778"]
      }
      first: 20
    ) {
      nodes {
        id
        title
        status { title }
      }
    }
  }
}
```

[DeviceFilter](../devices/README.md#devicefilter) supports the following fields:

| Field | Description |
| --- | --- |
| `typeIds` | One or more device type IDs |
| `modelIds` | One or more device model IDs |
| `statusIds` | One or more device status IDs |
| `vendorIds` | One or more manufacturer IDs |
| `inventoryIds` | One or more inventory IDs |
| `titleContains` | Partial, case-insensitive match on the device title |
| `identifierContains` | Partial, case-insensitive match on any identifier value |

`identifierContains` is particularly useful for looking up a device by a partial IMEI or serial number:

```graphql
query FindByIMEI {
  bdr {
    devices(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: { identifierContains: "356307" }
      first: 5
    ) {
      nodes {
        id
        title
        identifiers { type value }
      }
    }
  }
}
```

## Handling version conflicts

If you provide `version` in `deviceUpdate` or `deviceDelete` and the device has been modified by another request since your last fetch, the API returns a `409 CONFLICT` error:

```json
{
  "errors": [{
    "message": "Entity has been modified by another request",
    "extensions": {
      "code": "CONFLICT",
      "entityType": "Device",
      "entityId": "e1b6f4a3-4a5d-7b8e-cf10-444555666777",
      "expectedVersion": 1,
      "currentVersion": 2
    }
  }]
}
```

To resolve this, fetch the device again to get its current version and state, reconcile any differences with your intended changes, and retry the mutation with the updated version.

For a full explanation of how versioning works, see [Optimistic locking](../optimistic-locking.md).

## See also

* [Devices](../devices/README.md): Complete reference for all device operations and types
* [Managing device inventory](managing-device-inventory.md): Assign devices to inventories and track assignment history
* [Working with assets](working-with-assets.md): Create and manage assets such as vehicles and equipment
