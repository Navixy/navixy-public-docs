---
description: Register and manage GPS trackers, sensors, and other hardware devices.
tags:
  - guide
  - beta
---

# Managing device records and identifiers

A device in Business Data Repository represents a physical hardware unit: a GPS tracker, sensor, beacon, or any other piece of trackable hardware. Devices hold the identifying information, such as IMEI numbers and serial numbers, that connects physical hardware to the rest of your platform data. When linked to an asset through a `DEVICE`-type custom field, a device lets tracking data flow from the hardware into the platform. The link works in both directions: on the asset, `primaryDevice` returns the primary device and `customFields` returns every linked device, while on the device, the `asset` field returns the asset it belongs to.

This guide covers the full device lifecycle: looking up the required catalog records, registering a device, managing its identifiers, creating device relations, updating device properties, and decommissioning it.

## Prerequisites

You need an access token for the workspace you work in. The API takes the workspace from the token, so no operation on this page asks for a workspace ID. See [Authentication](../../authentication.md) for how to get a token and how the API picks the workspace.

You also need IDs for two catalog records before you create a device:

* Device type: one of the built-in types, such as GPS Tracker or Dashcam, or a type you create
* Device model: the specific hardware model taken from the read-only model catalog

To check what device types already exist in your workspace, run this query:

```graphql
query GetDeviceTypes {
  bdr {
    deviceTypes {
      nodes { id title code }
    }
  }
}
```

The platform provides nine built-in types, such as `gps_tracker`, `obd_dongle`, and `ble_beacon`, in every workspace. If none fits, create your own with a code that no built-in type uses:

```graphql
mutation CreateDeviceType {
  bdr {
    deviceTypeCreate(input: {
      title: "Trailer Sensor"
      code: "trailer_sensor"
    }) {
      deviceType { id code }
    }
  }
}
```

{% hint style="info" %}
Device statuses are system-managed. Four built-in statuses (`Not Activated`, `Awaiting Connection`, `Active`, and `Inactive`) are provided by the platform and available in every workspace. A device created with `deviceCreate` starts with `Not Activated`. A device activated with `deviceActivate` starts with `Awaiting Connection` and becomes `Active` with its first message: see [Getting started](../../getting-started.md). Query `deviceStatuses` to get their IDs for use in filters. You can also create additional custom statuses alongside the built-in ones.
{% endhint %}

To look up a hardware model, query the read-only model catalog. You can filter by title or vendor:

```graphql
query FindDeviceModel {
  bdr {
    deviceModels(
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

A device can have zero or more hardware identifiers that connect the platform record to the physical hardware. Telematics servers and other systems use these identifiers to look up a device.

The available identifier types are defined by the [DeviceIdType](../api-reference/devices/#enums) enum:

<table><thead><tr><th width="198">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>IMEI</code></td><td>15-digit International Mobile Equipment Identity</td></tr><tr><td><code>SERIAL_NUMBER</code></td><td>Manufacturer-assigned serial number</td></tr><tr><td><code>MAC_ADDRESS</code></td><td>Network interface MAC address</td></tr><tr><td><code>GUID</code></td><td>GUID/UUID identifier</td></tr><tr><td><code>MEID_HEX</code></td><td>Mobile Equipment Identifier, hexadecimal format</td></tr><tr><td><code>MEID_DEC</code></td><td>Mobile Equipment Identifier, decimal format</td></tr><tr><td><code>CUSTOM</code></td><td>Workspace-defined identifier</td></tr></tbody></table>

An identifier must be unique. Uniqueness is checked on the combination of `type`, `value`, and `namespace`, and the `value` comparison is case-insensitive. Leave `namespace` `null` for standard types (`IMEI`, `SERIAL_NUMBER`, `MAC_ADDRESS`, etc.) so the value is unique across the entire platform. The API doesn't enforce the rule. For `CUSTOM` identifiers, you can set a `namespace` string to limit where the value must be unique: two devices can share the same custom value as long as their namespaces differ. This is useful when integrating with external systems that have their own ID schemes and whose values may overlap.

### Device status and properties

Devices don't support [custom fields](defining-and-using-custom-fields.md). The only properties you can change are `title` and `modelId`. Device status is controlled by the platform: every new device starts with the system status `Not Activated`. The platform provides four built-in statuses (`Not Activated`, `Awaiting Connection`, `Active`, `Inactive`) visible to all workspaces. You can create additional custom statuses with [deviceStatusCreate](../api-reference/devices/#devicestatuscreate), but status cannot be changed through the public API.

#### Asset link

The `asset` field returns the linked asset as a full [Asset](../api-reference/assets/#asset) object, so you can query any of its fields directly, or `null` if the device isn't assigned to any asset. This is the same link seen from the device side: assets link to devices through custom fields of type `DEVICE`, and `Device.asset` follows that link back. The link is managed entirely from the asset side. See [Creating assets and assigning devices](creating-assets-and-assigning-devices.md) for details.

### Device relations

Two devices can be linked with a typed relation. Relation types are system-defined, so your workspace can't create new ones. There's one relation type, `primary_secondary`, with the title "Primary / Secondary". To get its ID, use the `deviceRelationTypes` query:

```graphql
query GetRelationTypes {
  bdr {
    deviceRelationTypes {
      nodes { id title code }
    }
  }
}
```

A relation has two endpoints: `first` and `second`. On the device the relation was created from (`first`), the relation appears in `relationsFrom`. On the device at the other end (`second`), the same relation appears in `relationsTo`. The `first`/`second` distinction only records the direction the relation was created in. The platform attaches no meaning to it, so your application decides what it means.

## Example scenario: Onboarding a tracker shipment

FleetOps Ltd receives a shipment of Teltonika FMB003 GPS trackers for installation across their delivery fleet. This scenario walks through registering a device, recording its identifiers, linking it to a companion beacon, renaming it once installed, and decommissioning it at end of life.

{% stepper %}
{% step %}
### Create a device

Register the first device using the type and model IDs from the prerequisites step

{% hint style="info" %}
`version` is optional. If you leave it out, an update always applies, even when someone else changed the record after you last read it. Include it to catch such conflicts, as the examples in this scenario do. See [Optimistic locking](../../optimistic-locking.md) for details.
{% endhint %}

Run this mutation:

```graphql
mutation RegisterDevice {
  bdr {
    deviceCreate(input: {
      typeId: "019a6b2f-793e-807b-8001-434454500001"
      modelId: "019a6b2f-793e-807b-8001-43444d4c0001"
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
          "id": "019a6b2f-793e-807b-8001-444556430011",
          "version": 1,
          "title": "FMB003 — Unit 001",
          "type": { "title": "GPS Tracker" },
          "model": { "title": "FMB003", "vendor": { "title": "Teltonika" } },
          "status": { "title": "Not Activated" },
          "identifiers": [
            {
            "id": "019a6b2f-793e-807b-8001-445649440001",
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
If any identifier already exists on another device, [deviceCreate](../api-reference/devices/#devicecreate) fails and the device is not created.
{% endhint %}
{% endstep %}

{% step %}
### Add more identifiers

The IMEI was provided at creation. To register additional identifiers, such as a serial number, use [deviceIdentifierAdd](../api-reference/devices/#deviceidentifieradd):

```graphql
mutation AddSerialNumber {
  bdr {
    deviceIdentifierAdd(input: {
      deviceId: "019a6b2f-793e-807b-8001-444556430011"
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
          "id": "019a6b2f-793e-807b-8001-445649440002",
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
      identifierId: "019a6b2f-793e-807b-8001-445649440001"
    }) {
      deletedId
    }
  }
}
```
{% endstep %}

{% step %}
### Assign device to an asset

FMB003 Unit 001 will track delivery truck DE-1049. To link the device, update the asset's `DEVICE`-type custom field. This example assumes the asset's type already has a custom field definition of the `DEVICE` type. See [Defining and using custom fields](defining-and-using-custom-fields.md) for instructions on setting it up.

```graphql
mutation AssignDeviceToAsset {
  bdr {
    assetUpdate(input: {
      id: "019a6b2f-793e-807b-8001-415353540011"
      version: 1
      customFields: {
        set: [
          {
            code: "cf_tracker"
            value: {
              device: {
                id: "019a6b2f-793e-807b-8001-444556430011"
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
          "id": "019a6b2f-793e-807b-8001-415353540011",
          "title": "Truck DE-1049",
          "primaryDevice": {
            "id": "019a6b2f-793e-807b-8001-444556430011",
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
    device(id: "019a6b2f-793e-807b-8001-444556430011") {
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
    deviceRelationTypes {
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
      firstId: "019a6b2f-793e-807b-8001-444556430011"
      secondId: "019a6b2f-793e-807b-8001-444556430012"
      typeId: "019a6b2f-793e-807b-8001-434452540001"
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
          "id": "019a6b2f-793e-807b-8001-4452454c0001",
          "first": {
            "id": "019a6b2f-793e-807b-8001-444556430011",
            "title": "FMB003 — Unit 001"
          },
          "second": {
            "id": "019a6b2f-793e-807b-8001-444556430012",
            "title": "EYE Beacon — Unit 001"
          },
          "type": { "title": "Primary / Secondary" }
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
      id: "019a6b2f-793e-807b-8001-4452454c0001"
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
      id: "019a6b2f-793e-807b-8001-444556430011"
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
          "id": "019a6b2f-793e-807b-8001-444556430011",
          "version": 2,
          "title": "FMB003 — Unit 001 (Truck 14)",
          "status": { "title": "Not Activated" },
          "asset": {
            "id": "019a6b2f-793e-807b-8001-415353540011",
            "title": "Truck DE-1049"
          }
        }
      }
    }
  }
}
```

{% hint style="info" %}
Providing `version` turns on optimistic locking: if the device changed since you last fetched it, the API returns a [409 Conflict](../../error-handling.md#version-conflict-409) error instead of overwriting the change without warning. Without `version`, the update always applies. See [Handling version conflicts](managing-device-records-and-identifiers.md#handling-version-conflicts) for details.
{% endhint %}
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You now have a device record with several identifiers, assigned to an asset, related to a companion beacon, and renamed once installed.
{% endhint %}

## Listing devices

To list all devices in a workspace:

```graphql
query ListDevices {
  bdr {
    devices(
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

Use [DeviceFilter](../api-reference/devices/#devicefilter) to narrow down results. Conditions across different fields are combined with AND, while multiple values within a single field are combined with OR. For the full filter reference, see [Filtering and sorting](../../filtering-and-sorting/).

Common filter combinations:

```graphql
query FindActiveTrackers {
  bdr {
    devices(
      filter: {
        typeIds: ["019a6b2f-793e-807b-8001-434454500001"]
        statusIds: ["019a6b2f-793e-807b-8001-434453540001"]
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

[DeviceFilter](../api-reference/devices/#devicefilter) supports the following fields:

| Field                | Description                                             |
| -------------------- | ------------------------------------------------------- |
| `typeIds`            | One or more device type IDs                             |
| `modelIds`           | One or more device model IDs                            |
| `statusIds`          | One or more device status IDs                           |
| `vendorIds`          | One or more manufacturer IDs                            |
| `inventoryIds`       | One or more inventory IDs                               |
| `titleContains`      | Partial, case-insensitive match on the device title     |
| `identifierContains` | Partial, case-insensitive match on any identifier value |

`identifierContains` is particularly useful for looking up a device by a partial IMEI or serial number:

```graphql
query FindByIMEI {
  bdr {
    devices(
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
    "message": "Version conflict: expected 1, found 2",
    "extensions": {
      "code": "CONFLICT",
      "status": 409,
      "detail": "Version conflict: expected 1, found 2",
      "field": "input.version",
      "expectedVersion": 1,
      "currentVersion": 2
    }
  }]
}
```

To resolve this, fetch the device again to get its current version and state, reconcile any differences with your intended changes, and retry the mutation with the updated version.

For a full explanation of how versioning works, see [Optimistic locking](../../optimistic-locking.md).

## Deleting a device

When a tracker reaches end of life and needs to be permanently removed, use `deviceDelete`. It hides the device from every query. Its identifiers stay reserved, so a new device can't reuse the same `type`, `value` and `namespace`.

`deviceDelete` doesn't close an active inventory assignment either. Unlink the device from its inventory first with `deviceInventoryUnlink` (see [Managing device inventory](managing-device-inventory.md)), then delete it.

`deviceDelete` is refused with [`IN_USE`](../../error-handling.md#in-use-409) while another device has a relation whose `second` is this device. `extensions.blockers` names those devices with the path `relationsFrom`. Remove the relations with `deviceRelationRemove` first. The relations that this device holds as `first` don't block the delete.

In a workspace that has IoT Logic, `deviceDelete` is also refused while a flow lists the device in its data source node. Every flow that isn't deleted counts, whether it's enabled or not. The blocker has `entityType` set to `IOT_LOGIC_FLOW` and `path` set to `nodes.devices`. Remove the device from the flow, or delete the flow, then delete the device.

Deleting a device automatically detaches it from every `DEVICE`-type custom field that points at it, on assets and geo objects alike, in the same transaction as the delete. The one exception is a required field: if any `DEVICE` field holding this device is `isRequired`, the delete is refused with [`IN_USE`](../../error-handling.md#in-use-409), and nothing is detached. `extensions.blockers` names the assets and geo objects with the path `customFields.<code>`. Put another device in the field, make the field not required, or delete the record that holds it. To put a replacement device in the field, update the asset's custom fields. The replacement must not be linked to another asset, unless you add `reassign: true`:

```graphql
mutation ReplaceBeforeDelete {
  bdr {
    assetUpdate(input: {
      id: "019a6b2f-793e-807b-8001-415353540011"
      version: 2
      customFields: {
        set: [{
          code: "cf_tracker"
          value: { device: { id: "019a6b2f-793e-807b-8001-444556430021", isPrimary: true } }
        }]
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
      id: "019a6b2f-793e-807b-8001-444556430011"
      version: 2
    }) {
      deletedId
    }
  }
}
```

{% hint style="warning" %}
A device delete can fail because of a required `DEVICE` field on an entirely different entity. The `IN_USE` error names that entity and the field, so change the field there and retry.
{% endhint %}

## See also

* [Devices](../api-reference/devices/): Complete reference for all device operations and types
* [Managing device inventory](managing-device-inventory.md): Assign devices to inventories and track assignment history
* [Creating assets and assigning devices](creating-assets-and-assigning-devices.md): Create and manage assets such as vehicles and equipment
