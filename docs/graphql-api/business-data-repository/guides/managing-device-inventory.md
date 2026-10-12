---
description: >-
  Create inventories, assign devices to them, transfer devices, and track
  assignment history.
tags:
  - guide
  - beta
---

# Managing device inventory

An inventory in Business Data Repository is a named container that represents a physical location where devices are stored: a warehouse, a service depot, a regional office, or any other place your workspace keeps hardware. Inventories show you where each device physically is at any point in time, and the assignment history lets you trace where it has been.

A device can be assigned to at most one inventory at a time. To move a device, unlink it from its current inventory first, then link it to the new one.

Unlinking doesn't erase the past. Every assignment a device has ever had stays on the device, readable through its [`inventoryHistory`](managing-device-inventory.md#view-assignment-history) field. This is a dedicated inventory history, separate from the [audit trail](investigating-changes-with-audit-logs.md): inventory assignments produce no audit events, so `inventoryHistory` is the only place to find them.

This guide continues the FleetOps Ltd scenario from [Managing device records and identifiers](managing-device-records-and-identifiers.md). The company has registered a batch of Teltonika FMB003 trackers. Now the hardware operations team needs to track which warehouse holds each device as units move from central stock to regional depots ahead of installation.

## Prerequisites

You need an access token for the workspace you work in. The API takes the workspace from the token, so no operation on this page asks for a workspace ID. See [Authentication](../../authentication.md) for how to get a token and how the API picks the workspace.

## Example scenario: Managing warehouse stock

FleetOps Ltd operates two hardware locations: a central warehouse in Berlin that receives new shipments, and a regional depot in Amsterdam that supplies the local installation team. This scenario walks through setting up both inventories, receiving a device shipment into Berlin, transferring a device to Amsterdam, and reviewing the assignment history.

{% stepper %}
{% step %}
### Create inventories

Create an inventory for each physical location. An inventory needs only a title.

{% hint style="info" %}
`version` is optional. If you leave it out, an update always applies, even when someone else changed the record after you last read it. Include it to catch such conflicts, as the examples in this scenario do. See [Optimistic locking](../../optimistic-locking.md) for details.
{% endhint %}

Run this mutation. A `bdr` block runs one mutation at a time, so each call gets its own aliased `bdr` block (see [Aliases](../../graphql-basics/graphql-tips-and-patterns.md#aliases)):

```graphql
mutation CreateInventories {
  berlin: bdr {
    inventoryCreate(input: {
      title: "Berlin Warehouse"
    }) {
      inventory { id version title }
    }
  }
  amsterdam: bdr {
    inventoryCreate(input: {
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
    "berlin": {
      "inventoryCreate": {
        "inventory": {
          "id": "019a6b2f-793e-807b-8001-494e56540001",
          "version": 1,
          "title": "Berlin Warehouse"
        }
      }
    },
    "amsterdam": {
      "inventoryCreate": {
        "inventory": {
          "id": "019a6b2f-793e-807b-8001-494e56540002",
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

When the shipment of FMB003 trackers arrives at Berlin Warehouse, assign each device to that inventory using `deviceInventoryLink`. You can send several assignments in one request by giving each call its own aliased `bdr` block, such as `unit001` below, because a `bdr` block runs one mutation at a time (see [Aliases](../../graphql-basics/graphql-tips-and-patterns.md#aliases)):

```graphql
mutation ReceiveShipment {
  unit001: bdr {
    deviceInventoryLink(input: {
      deviceId: "019a6b2f-793e-807b-8001-444556430011"
      inventoryId: "019a6b2f-793e-807b-8001-494e56540001"
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
  unit002: bdr {
    deviceInventoryLink(input: {
      deviceId: "019a6b2f-793e-807b-8001-444556430013"
      inventoryId: "019a6b2f-793e-807b-8001-494e56540001"
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
    "unit001": {
      "deviceInventoryLink": {
        "deviceInventoryRelation": {
          "id": "019a6b2f-793e-807b-8001-4449524c0001",
          "device": { "id": "019a6b2f-793e-807b-8001-444556430011", "title": "FMB003 — Unit 001" },
          "inventory": { "title": "Berlin Warehouse" },
          "assignedAt": "2025-03-10T09:15:00Z",
          "assignedBy": { "title": "Anna Müller" }
        }
      }
    },
    "unit002": {
      "deviceInventoryLink": {
        "deviceInventoryRelation": {
          "id": "019a6b2f-793e-807b-8001-4449524c0002",
          "device": { "id": "019a6b2f-793e-807b-8001-444556430013", "title": "FMB003 — Unit 002" },
          "inventory": { "title": "Berlin Warehouse" },
          "assignedAt": "2025-03-10T09:15:01Z",
          "assignedBy": { "title": "Anna Müller" }
        }
      }
    }
  }
}
```

A device can hold only one active assignment, so `deviceInventoryLink` fails if the device is already assigned anywhere, including to the same inventory you're linking it to. The error is a [validation error](../../error-handling.md#validation-error-400) reading `Device <id> already linked to inventory <id>`. Unlink first, then link.

{% hint style="info" %}
`assignedBy` records the actor who made the API call. It is `null` when the request carried no actor.
{% endhint %}
{% endstep %}

{% step %}
### Query inventory contents

To see which devices are currently assigned to Berlin Warehouse, query the `devices` field on the inventory. It accepts the same [DeviceFilter](../api-reference/devices/#devicefilter) input as the top-level `devices` query, but only `typeIds`, `modelIds`, `statusIds`, and `titleContains` have any effect here. `identifierContains`, `vendorIds`, and `inventoryIds` are accepted but ignored.

```graphql
query BerlinStock {
  bdr {
    inventory(id: "019a6b2f-793e-807b-8001-494e56540001") {
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
              "id": "019a6b2f-793e-807b-8001-444556430011",
              "title": "FMB003 — Unit 001",
              "status": { "title": "Not Activated" },
              "identifiers": [
                { "type": "IMEI", "value": "356307042772396" }
              ]
            },
            {
              "id": "019a6b2f-793e-807b-8001-444556430013",
              "title": "FMB003 — Unit 002",
              "status": { "title": "Not Activated" },
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
      filter: {
        inventoryIds: [
          "019a6b2f-793e-807b-8001-494e56540001"
          "019a6b2f-793e-807b-8001-494e56540002"
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
      deviceId: "019a6b2f-793e-807b-8001-444556430011"
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
      deviceId: "019a6b2f-793e-807b-8001-444556430011"
      inventoryId: "019a6b2f-793e-807b-8001-494e56540002"
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
      deviceId: "019a6b2f-793e-807b-8001-444556430011"
    }) {
      deletedId
    }
  }
  link: bdr {
    deviceInventoryLink(input: {
      deviceId: "019a6b2f-793e-807b-8001-444556430011"
      inventoryId: "019a6b2f-793e-807b-8001-494e56540002"
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

This works cleanly only when the device is currently assigned somewhere: `deviceInventoryUnlink` returns a [404 error](../../error-handling.md#entity-not-found-404) when the device has no active assignment. The aliased blocks run independently, so the link still executes and succeeds, but the response carries the unlink error. For a device you're assigning for the first time, call `deviceInventoryLink` on its own.
{% endhint %}
{% endstep %}

{% step %}
### View assignment history

To see where Unit 001 has been, query its `inventoryHistory`. Records are returned newest first by default.

```graphql
query DeviceHistory {
  bdr {
    device(id: "019a6b2f-793e-807b-8001-444556430011") {
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

There's no separate record for an unlink, and no field for when an assignment ended. Each record tells you when an assignment started (`assignedAt`) and who made it (`assignedBy`). A device leaving one inventory shows up as its next assignment to a different one. After an unlink with no new link, the newest record stays in the history, but the device has no current assignment and `Device.inventory` is `null`. To find the current assignment, read `Device.inventory`.

To retrieve the oldest assignments first, override the default ordering:

```graphql
inventoryHistory(
  first: 10
  orderBy: { field: ASSIGNED_AT, direction: ASC }
)
```
{% endstep %}

{% step %}
### Update an inventory

To rename an inventory, use `inventoryUpdate` with its current version:

```graphql
mutation RenameInventory {
  bdr {
    inventoryUpdate(input: {
      id: "019a6b2f-793e-807b-8001-494e56540001"
      version: 1
      title: "Berlin Central Warehouse"
    }) {
      inventory { id version title }
    }
  }
}
```
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You now have two inventories, one in Berlin and one in Amsterdam, devices assigned to Berlin, one device transferred to Amsterdam, the assignment history that records that transfer, and an inventory renamed under its version.
{% endhint %}

## Listing inventories

To list all inventories for a workspace:

```graphql
query ListInventories {
  bdr {
    inventories(
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

To resolve this, fetch the inventory again to get its current version and retry. Note that `deviceInventoryLink` and `deviceInventoryUnlink` don't use version checks: linking fails when the device is already assigned, and unlinking fails when it isn't, so a stale retry surfaces as one of those errors instead of a version conflict.

For a full explanation of how versioning works, see [Optimistic locking](../../optimistic-locking.md).

## Deleting an inventory

To delete an inventory that is no longer needed, use `inventoryDelete`:

```graphql
mutation DeleteInventory {
  bdr {
    inventoryDelete(input: {
      id: "019a6b2f-793e-807b-8001-494e56540001"
      version: 2
    }) {
      deletedId
    }
  }
}
```

{% hint style="warning" %}
`inventoryDelete` is refused with [`IN_USE`](../../error-handling.md#in-use-409) while a device is linked to the inventory, and `extensions.blockers` lists the linked devices that you can read. Unlink them with `deviceInventoryUnlink` first, then delete the inventory. Past links don't block the delete. An asset or a geo object that names the inventory in a custom field refuses the delete too, with the path `customFields.<code>`. There's no way to reverse the delete through the API.
{% endhint %}

## See also

* [Managing device records and identifiers](managing-device-records-and-identifiers.md): Register GPS devices and manage their identifiers and relations
* [Inventory](../api-reference/devices/inventory.md): Complete reference for all inventory operations and types
