---
description: >-
  Create inventories, assign devices to them, transfer devices, and track
  assignment history.
---

# Managing device inventory

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

An inventory in Navixy Repository API is a named container that represents a physical location where devices are stored: a warehouse, a service depot, a regional office, or any other place your workspace keeps hardware. Inventories show you where each device physically is at any point in time, and the assignment history lets you trace where it has been.

A device can be assigned to at most one inventory at a time. To move a device, unlink it from its current inventory first, then link it to the new one.

Unlinking doesn't erase the past. Every assignment a device has ever had stays on the device, readable through its [`inventoryHistory`](#view-assignment-history) field. This is a dedicated inventory history, separate from the [audit trail](tracking-changes-with-audit.md): inventory assignments produce no audit events, so `inventoryHistory` is the only place to find them.

This guide continues the FleetOps Ltd scenario from [Working with devices](working-with-devices.md). The company has registered a batch of Teltonika FMB003 trackers. Now the hardware operations team needs to track which warehouse holds each device as units move from central stock to regional depots ahead of installation.

## Prerequisites

You need your workspace's ID for all inventory operations. It comes with your access credentials and is carried in your access token. See [Authentication](../authentication.md) for how tokens work and where the workspace ID comes from.

## Example scenario: Managing warehouse stock

FleetOps Ltd operates two hardware locations: a central warehouse in Berlin that receives new shipments, and a regional depot in Amsterdam that supplies the local installation team. This scenario walks through setting up both inventories, receiving a device shipment into Berlin, transferring a device to Amsterdam, and reviewing the assignment history.

{% stepper %}
{% step %}
### Create inventories

Create an inventory for each physical location. Inventories only require a workspace ID and a title.

{% hint style="info" %}
`version` is optional. If you leave it out, an update always applies, even when someone else changed the record after you last read it. Include it to catch such conflicts, as the examples in this scenario do. See [Optimistic locking](../optimistic-locking.md) for details.
{% endhint %}

Run this mutation:

```graphql
mutation CreateInventories {
  bdr {
    berlin: inventoryCreate(input: {
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      title: "Berlin Warehouse"
    }) {
      inventory { id version title }
    }
    amsterdam: inventoryCreate(input: {
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      title: "Amsterdam Depot"
    }) {
      inventory { id version title }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "berlin": {
        "inventory": {
          "id": "a1b2c3d4-1234-5678-abcd-111222333444",
          "version": 1,
          "title": "Berlin Warehouse"
        }
      },
      "amsterdam": {
        "inventory": {
          "id": "b2c3d4e5-2345-6789-bcde-222333444555",
          "version": 1,
          "title": "Amsterdam Depot"
        }
      }
    }
  }
}
```

Save both IDs. You'll need them when assigning and transferring devices.
{% endstep %}

{% step %}
### Assign devices to an inventory

When the shipment of FMB003 trackers arrives at Berlin Warehouse, assign each device to that inventory using `deviceInventoryLink`. You can send several assignments in one request by giving each an alias, such as `unit001` below (see [Aliases](../graphql-basics/graphql-tips-and-patterns.md#aliases)):

```graphql
mutation ReceiveShipment {
  bdr {
    unit001: deviceInventoryLink(input: {
      deviceId: "e1b6f4a3-4a5d-7b8e-cf10-444555666777"
      inventoryId: "a1b2c3d4-1234-5678-abcd-111222333444"
    }) {
      deviceInventoryRelation {
        id
        device { id title }
        inventory { title }
        assignedAt
        assignedBy { title }
      }
    }
    unit002: deviceInventoryLink(input: {
      deviceId: "f2c7d5e4-5b6f-8c9a-d011-555666777888"
      inventoryId: "a1b2c3d4-1234-5678-abcd-111222333444"
    }) {
      deviceInventoryRelation {
        id
        device { id title }
        inventory { title }
        assignedAt
        assignedBy { title }
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
      "unit001": {
        "deviceInventoryRelation": {
          "id": "c3d4e5f6-3456-789a-cdef-333444555666",
          "device": { "id": "e1b6f4a3-4a5d-7b8e-cf10-444555666777", "title": "FMB003 — Unit 001" },
          "inventory": { "title": "Berlin Warehouse" },
          "assignedAt": "2025-03-10T09:15:00Z",
          "assignedBy": { "title": "Anna Müller" }
        }
      },
      "unit002": {
        "deviceInventoryRelation": {
          "id": "d4e5f6a7-4567-89ab-def0-444555666777",
          "device": { "id": "f2c7d5e4-5b6f-8c9a-d011-555666777888", "title": "FMB003 — Unit 002" },
          "inventory": { "title": "Berlin Warehouse" },
          "assignedAt": "2025-03-10T09:15:01Z",
          "assignedBy": { "title": "Anna Müller" }
        }
      }
    }
  }
}
```

A device can hold only one active assignment, so `deviceInventoryLink` fails if the device is already assigned anywhere, including to the same inventory you're linking it to. The error is a [validation error](../error-handling.md#validation-error-400) reading `Device <id> already linked to inventory <id>`. Unlink first, then link.

{% hint style="info" %}
`assignedBy` records the actor who made the API call. It can be `null` when the request came from an automated system rather than a signed-in user.
{% endhint %}
{% endstep %}

{% step %}
### Query inventory contents

To see which devices are currently assigned to Berlin Warehouse, query the `devices` field on the inventory. You can apply the same filters and ordering as the top-level `devices` query.

```graphql
query BerlinStock {
  bdr {
    inventory(id: "a1b2c3d4-1234-5678-abcd-111222333444") {
      title
      devices(first: 50) {
        nodes {
          id
          title
          status { title }
          identifiers { type value }
        }
        total { count }
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
      "inventory": {
        "title": "Berlin Warehouse",
        "devices": {
          "nodes": [
            {
              "id": "e1b6f4a3-4a5d-7b8e-cf10-444555666777",
              "title": "FMB003 — Unit 001",
              "status": { "title": "In Stock" },
              "identifiers": [
                { "type": "IMEI", "value": "356307042772396" }
              ]
            },
            {
              "id": "f2c7d5e4-5b6f-8c9a-d011-555666777888",
              "title": "FMB003 — Unit 002",
              "status": { "title": "In Stock" },
              "identifiers": [
                { "type": "IMEI", "value": "356307042883407" }
              ]
            }
          ],
          "total": { "count": 2 }
        }
      }
    }
  }
}
```

Alternatively, use the top-level `devices` query with `filter.inventoryIds` to query devices across multiple inventories at once:

```graphql
query AllWarehouseStock {
  bdr {
    devices(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: {
        inventoryIds: [
          "a1b2c3d4-1234-5678-abcd-111222333444"
          "b2c3d4e5-2345-6789-bcde-222333444555"
        ]
      }
      first: 50
    ) {
      nodes {
        id
        title
        inventory { title }
        status { title }
      }
    }
  }
}
```
{% endstep %}

{% step %}
### Transfer a device to another inventory

The Amsterdam installation team requests Unit 001 for a local job. Transferring a device takes two steps: unlink it from its current inventory, then link it to the destination. There's no single transfer mutation, and linking a device that's still assigned elsewhere fails.

First, unlink from Berlin Warehouse:

```graphql
mutation UnlinkFromBerlin {
  bdr {
    deviceInventoryUnlink(input: {
      deviceId: "e1b6f4a3-4a5d-7b8e-cf10-444555666777"
    }) {
      deletedId
    }
  }
}
```

Then assign to Amsterdam Depot:

```graphql
mutation LinkToAmsterdam {
  bdr {
    deviceInventoryLink(input: {
      deviceId: "e1b6f4a3-4a5d-7b8e-cf10-444555666777"
      inventoryId: "b2c3d4e5-2345-6789-bcde-222333444555"
    }) {
      deviceInventoryRelation {
        device { title }
        inventory { title }
        assignedAt
      }
    }
  }
}
```

Both calls can be combined into a single request. In a mutation, you can select only one field inside a single `bdr` block, so alias the `bdr` namespace itself, one block per call:

```graphql
mutation TransferDevice {
  unlink: bdr {
    deviceInventoryUnlink(input: {
      deviceId: "e1b6f4a3-4a5d-7b8e-cf10-444555666777"
    }) {
      deletedId
    }
  }
  link: bdr {
    deviceInventoryLink(input: {
      deviceId: "e1b6f4a3-4a5d-7b8e-cf10-444555666777"
      inventoryId: "b2c3d4e5-2345-6789-bcde-222333444555"
    }) {
      deviceInventoryRelation {
        device { title }
        inventory { title }
        assignedAt
      }
    }
  }
}
```

{% hint style="warning" %}
When combining `unlink` and `link` in a single request, the aliased `bdr` blocks run one at a time, in the order they appear in the request, so the unlink always runs before the link.

This works cleanly only when the device is currently assigned somewhere: `deviceInventoryUnlink` returns a [404 error](../error-handling.md#entity-not-found-404) when the device has no active assignment. The aliased blocks run independently, so the link still executes and succeeds, but the response carries the unlink error. For a device you're assigning for the first time, call `deviceInventoryLink` on its own.
{% endhint %}
{% endstep %}

{% step %}
### View assignment history

To see where Unit 001 has been, query its `inventoryHistory`. Records are returned newest first by default.

```graphql
query DeviceHistory {
  bdr {
    device(id: "e1b6f4a3-4a5d-7b8e-cf10-444555666777") {
      title
      inventoryHistory(first: 10) {
        nodes {
          inventory { title }
          assignedAt
          assignedBy { title }
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
      "device": {
        "title": "FMB003 — Unit 001",
        "inventoryHistory": {
          "nodes": [
            {
              "inventory": { "title": "Amsterdam Depot" },
              "assignedAt": "2025-03-12T14:30:00Z",
              "assignedBy": { "title": "Pieter van den Berg" }
            },
            {
              "inventory": { "title": "Berlin Warehouse" },
              "assignedAt": "2025-03-10T09:15:00Z",
              "assignedBy": { "title": "Anna Müller" }
            }
          ]
        }
      }
    }
  }
}
```

History only grows: every assignment is recorded permanently, and nothing is ever rewritten. Both past and current assignments appear, so a device linked to Berlin, unlinked, then linked to Amsterdam returns two records.

There's no separate record for an unlink, and no field for when an assignment ended. Each record tells you when an assignment started (`assignedAt`) and who made it (`assignedBy`). A device leaving one inventory shows up as its next assignment to a different one, and the current assignment is the newest record, which also matches `Device.inventory`.

To retrieve the oldest assignments first, override the default ordering:

```graphql
inventoryHistory(
  first: 10
  orderBy: { field: ASSIGNED_AT, direction: ASC }
)
```
{% endstep %}

{% step %}
### Update or delete an inventory

To rename an inventory, use `inventoryUpdate` with its current version:

```graphql
mutation RenameInventory {
  bdr {
    inventoryUpdate(input: {
      id: "a1b2c3d4-1234-5678-abcd-111222333444"
      version: 1
      title: "Berlin Central Warehouse"
    }) {
      inventory { id version title }
    }
  }
}
```

To delete an inventory that is no longer needed, use `inventoryDelete`:

```graphql
mutation DeleteInventory {
  bdr {
    inventoryDelete(input: {
      id: "a1b2c3d4-1234-5678-abcd-111222333444"
      version: 2
    }) {
      deletedId
    }
  }
}
```

{% hint style="danger" %}
Unassign every device before deleting an inventory. `inventoryDelete` doesn't check for assigned devices and doesn't return an error, but the devices are left in a broken state:

* `Device.inventory` returns `null`, so the device looks unassigned.
* The assignment is still active underneath, so `deviceInventoryLink` refuses to assign the device to any other inventory until you call `deviceInventoryUnlink` on it.
* `Device.inventoryHistory` fails for that device, because each history record must resolve the inventory it points to and the deleted one no longer resolves.

Unlink the devices first, then delete the inventory. There's no way to reverse the delete through the API.
{% endhint %}
{% endstep %}
{% endstepper %}

## Listing inventories

To list all inventories for a workspace:

```graphql
query ListInventories {
  bdr {
    inventories(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      first: 20
    ) {
      nodes {
        id
        title
      }
      pageInfo {
        hasNextPage
        endCursor
      }
    }
  }
}
```

Use `filter.titleContains` to search by name:

```graphql
query FindInventory {
  bdr {
    inventories(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: { titleContains: "amsterdam" }
      first: 5
    ) {
      nodes { id title }
    }
  }
}
```

## Handling version conflicts

`inventoryUpdate` and `inventoryDelete` support optimistic locking via the `version` field. If the inventory was modified by another request since your last fetch, the API returns a `409 CONFLICT` error:

```json
{
  "errors": [{
    "message": "Entity has been modified by another request",
    "extensions": {
      "code": "CONFLICT",
      "entityType": "Inventory",
      "entityId": "a1b2c3d4-1234-5678-abcd-111222333444",
      "expectedVersion": 1,
      "currentVersion": 2
    }
  }]
}
```

To resolve this, fetch the inventory again to get its current version and retry. Note that `deviceInventoryLink` and `deviceInventoryUnlink` don't use version checks: linking fails when the device is already assigned, and unlinking fails when it isn't, so a stale retry surfaces as one of those errors instead of a version conflict.

For a full explanation of how versioning works, see [Optimistic locking](../optimistic-locking.md).

## See also

* [Working with devices](working-with-devices.md): Register GPS devices and manage their identifiers and relations
* [Inventory](../devices/inventory.md): Complete reference for all inventory operations and types
