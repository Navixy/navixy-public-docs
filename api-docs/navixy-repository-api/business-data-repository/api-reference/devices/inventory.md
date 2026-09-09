---
description: >-
  Complete reference for device inventory. Manage warehouses, assign devices to
  inventory locations, and query assignment history.
---

# Inventory

{% include "../../../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

Inventory management for device stock, including warehouses, assignments, and device-inventory relationships.

## Queries

### inventory (query)

Retrieves an inventory by its ID.

```graphql
inventory(id: ID!): Inventory
```

**Arguments**

| Name | Type  | Description                          |
| ---- | ----- | ------------------------------------ |
| `id` | `ID!` | The ID of the inventory to retrieve. |

**Output types:**

<details>

<summary>Inventory</summary>

An inventory or warehouse record for device stock management.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field       | Type                                     | Description                                                                                                                                                                                               |
| ----------- | ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`        | `ID!`                                    | A globally unique identifier. This ID is opaque and should not be parsed by clients.                                                                                                                      |
| `version`   | `Int!`                                   | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title`     | `String!`                                | The human-readable display name.                                                                                                                                                                          |
| `workspace` | [Workspace](../workspaces/#workspace)!   | The workspace that owns this inventory.                                                                                                                                                                   |
| `devices`   | [DeviceConnection](./#deviceconnection)! | The devices assigned to this inventory.                                                                                                                                                                   |

</details>

***

### inventories

Lists inventories for a workspace.

```graphql
inventories(
    workspaceId: ID!
    filter: InventoryFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: InventoryOrder = { field: TITLE, direction: ASC }
  ): InventoryConnection!
```

**Arguments**

| Name          | Type              | Description                                                                                                     |
| ------------- | ----------------- | --------------------------------------------------------------------------------------------------------------- |
| `workspaceId` | `ID!`             | The workspace to retrieve inventories for.                                                                      |
| `filter`      | `InventoryFilter` | Filtering options for the returned inventories.                                                                 |
| `first`       | `Int`             | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination).     |
| `after`       | `String`          | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination).  |
| `last`        | `Int`             | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination).      |
| `before`      | `String`          | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy`     | `InventoryOrder`  | The ordering options for the returned inventories.                                                              |

**Input types:**

<details>

<summary>InventoryFilter</summary>

Filtering options for inventories.

| Field           | Type     | Description                                         |
| --------------- | -------- | --------------------------------------------------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |

</details>

<details>

<summary>InventoryOrder</summary>

Ordering options for inventories.

| Field       | Type                                                                    | Description             |
| ----------- | ----------------------------------------------------------------------- | ----------------------- |
| `field`     | [InventoryOrderField](inventory.md#inventoryorderfield)!                | The field to order by.  |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>InventoryConnection</summary>

A paginated list of Inventory items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field      | Type                                                         | Description                                                |
| ---------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| `edges`    | \[[InventoryEdge](inventory.md#inventoryedge)!]!             | A list of edges.                                           |
| `nodes`    | \[[Inventory](inventory.md#inventory)!]!                     | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)!  | Information about the current page.                        |
| `total`    | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter.              |

</details>

***

## Mutations

### inventoryCreate

Creates a new inventory.

```graphql
inventoryCreate(
    input: InventoryCreateInput!
  ): InventoryPayload
```

**Arguments**

| Name    | Type                    | Description                                  |
| ------- | ----------------------- | -------------------------------------------- |
| `input` | `InventoryCreateInput!` | The input fields for creating the inventory. |

**Input types:**

<details>

<summary>InventoryCreateInput</summary>

Input for creating a new inventory.

| Field         | Type      | Description                                |
| ------------- | --------- | ------------------------------------------ |
| `workspaceId` | `ID!`     | The workspace that will own the inventory. |
| `title`       | `String!` | The display name.                          |

</details>

**Output types:**

<details>

<summary>InventoryPayload</summary>

The result of an inventory mutation.

| Field       | Type                                 | Description                       |
| ----------- | ------------------------------------ | --------------------------------- |
| `inventory` | [Inventory](inventory.md#inventory)! | The created or updated inventory. |

</details>

<details>

<summary>Inventory (entity)</summary>

An inventory or warehouse record for device stock management.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field       | Type                                     | Description                                                                                                                                                                                               |
| ----------- | ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`        | `ID!`                                    | A globally unique identifier. This ID is opaque and should not be parsed by clients.                                                                                                                      |
| `version`   | `Int!`                                   | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title`     | `String!`                                | The human-readable display name.                                                                                                                                                                          |
| `workspace` | [Workspace](../workspaces/#workspace)!   | The workspace that owns this inventory.                                                                                                                                                                   |
| `devices`   | [DeviceConnection](./#deviceconnection)! | The devices assigned to this inventory.                                                                                                                                                                   |

</details>

***

### inventoryUpdate

Updates an existing inventory.

```graphql
inventoryUpdate(
    input: InventoryUpdateInput!
  ): InventoryPayload
```

**Arguments**

| Name    | Type                    | Description                                  |
| ------- | ----------------------- | -------------------------------------------- |
| `input` | `InventoryUpdateInput!` | The input fields for updating the inventory. |

**Input types:**

<details>

<summary>InventoryUpdateInput</summary>

Input for updating an existing inventory.

| Field     | Type     | Description                                                                                     |
| --------- | -------- | ----------------------------------------------------------------------------------------------- |
| `id`      | `ID!`    | The inventory ID to update.                                                                     |
| `version` | `Int`    | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title`   | `String` | The new display name.                                                                           |

</details>

**Output types:**

<details>

<summary>InventoryPayload</summary>

The result of an inventory mutation.

| Field       | Type                                 | Description                       |
| ----------- | ------------------------------------ | --------------------------------- |
| `inventory` | [Inventory](inventory.md#inventory)! | The created or updated inventory. |

</details>

<details>

<summary>Inventory (entity)</summary>

An inventory or warehouse record for device stock management.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field       | Type                                     | Description                                                                                                                                                                                               |
| ----------- | ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`        | `ID!`                                    | A globally unique identifier. This ID is opaque and should not be parsed by clients.                                                                                                                      |
| `version`   | `Int!`                                   | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title`     | `String!`                                | The human-readable display name.                                                                                                                                                                          |
| `workspace` | [Workspace](../workspaces/#workspace)!   | The workspace that owns this inventory.                                                                                                                                                                   |
| `devices`   | [DeviceConnection](./#deviceconnection)! | The devices assigned to this inventory.                                                                                                                                                                   |

</details>

***

### inventoryDelete

Deletes an inventory.

```graphql
inventoryDelete(
    input: InventoryDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name    | Type                    | Description                                  |
| ------- | ----------------------- | -------------------------------------------- |
| `input` | `InventoryDeleteInput!` | The input fields for deleting the inventory. |

**Input types:**

<details>

<summary>InventoryDeleteInput</summary>

Input for deleting an inventory.

| Field     | Type  | Description                                                                                     |
| --------- | ----- | ----------------------------------------------------------------------------------------------- |
| `id`      | `ID!` | The inventory ID to delete.                                                                     |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

</details>

**Output types:**

<details>

<summary>DeletePayload</summary>

The result of a delete mutation.

| Field       | Type  | Description                   |
| ----------- | ----- | ----------------------------- |
| `deletedId` | `ID!` | The ID of the deleted entity. |

</details>

***

### deviceInventoryLink

Links a device to an inventory.

A device holds at most one active inventory link. This is not an idempotent command: `VALIDATION_ERROR` when the device already has one, _including_ a link to the very inventory named in the input. Moving a device between inventories is `deviceInventoryUnlink` then `deviceInventoryLink`, and re-running a link that already succeeded is an error, not a no-op.

```graphql
deviceInventoryLink(
    input: DeviceInventoryLinkInput!
  ): DeviceInventoryRelationPayload
```

**Arguments**

| Name    | Type                        | Description                              |
| ------- | --------------------------- | ---------------------------------------- |
| `input` | `DeviceInventoryLinkInput!` | The input fields for linking the device. |

**Input types:**

<details>

<summary>DeviceInventoryLinkInput</summary>

Input for linking a device to an inventory. Both device and inventory must belong to the same workspace.

| Field         | Type  | Description                                                    |
| ------------- | ----- | -------------------------------------------------------------- |
| `deviceId`    | `ID!` | The device ID.                                                 |
| `inventoryId` | `ID!` | The inventory ID. Must be in the same workspace as the device. |

</details>

**Output types:**

<details>

<summary>DeviceInventoryRelationPayload</summary>

The result of a device inventory link mutation.

| Field                     | Type                                                             | Description                       |
| ------------------------- | ---------------------------------------------------------------- | --------------------------------- |
| `deviceInventoryRelation` | [DeviceInventoryRelation](inventory.md#deviceinventoryrelation)! | The created inventory assignment. |

</details>

<details>

<summary>DeviceInventoryRelation (entity)</summary>

A record of a device's assignment to an inventory.

**Implements:** [Node](../../../core-api-reference/common.md#node)

| Field        | Type                                                        | Description                                                                          |
| ------------ | ----------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| `id`         | `ID!`                                                       | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `device`     | [Device](./#device)!                                        | The device that was assigned.                                                        |
| `inventory`  | [Inventory](inventory.md#inventory)!                        | The inventory the device was assigned to.                                            |
| `assignedAt` | [DateTime](../../../core-api-reference/common.md#datetime)! | The date and time when the device was assigned.                                      |
| `assignedBy` | [Actor](../actors/#actor)                                   | The actor who assigned the device.                                                   |

</details>

***

### deviceInventoryUnlink

Unlinks a device from an inventory.

Not an idempotent command: `NOT_FOUND` when the device has no active inventory link, including a repeat of a call that already succeeded. The input names only the device — the link to close is the active one, whichever inventory it points at.

```graphql
deviceInventoryUnlink(
    input: DeviceInventoryUnlinkInput!
  ): DeletePayload
```

**Arguments**

| Name    | Type                          | Description                                |
| ------- | ----------------------------- | ------------------------------------------ |
| `input` | `DeviceInventoryUnlinkInput!` | The input fields for unlinking the device. |

**Input types:**

<details>

<summary>DeviceInventoryUnlinkInput</summary>

Input for unlinking a device from an inventory.

| Field      | Type  | Description              |
| ---------- | ----- | ------------------------ |
| `deviceId` | `ID!` | The device ID to unlink. |

</details>

**Output types:**

<details>

<summary>DeletePayload</summary>

The result of a delete mutation.

| Field       | Type  | Description                   |
| ----------- | ----- | ----------------------------- |
| `deletedId` | `ID!` | The ID of the deleted entity. |

</details>

***

## Objects

### DeviceInventoryRelation

A record of a device's assignment to an inventory.

**Implements:** [Node](../../../core-api-reference/common.md#node)

| Field        | Type                                                        | Description                                                                          |
| ------------ | ----------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| `id`         | `ID!`                                                       | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `device`     | [Device](./#device)!                                        | The device that was assigned.                                                        |
| `inventory`  | [Inventory](inventory.md#inventory)!                        | The inventory the device was assigned to.                                            |
| `assignedAt` | [DateTime](../../../core-api-reference/common.md#datetime)! | The date and time when the device was assigned.                                      |
| `assignedBy` | [Actor](../actors/#actor)                                   | The actor who assigned the device.                                                   |

***

### Inventory

An inventory or warehouse record for device stock management.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field       | Type                                     | Description                                                                                                                                                                                               |
| ----------- | ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`        | `ID!`                                    | A globally unique identifier. This ID is opaque and should not be parsed by clients.                                                                                                                      |
| `version`   | `Int!`                                   | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title`     | `String!`                                | The human-readable display name.                                                                                                                                                                          |
| `workspace` | [Workspace](../workspaces/#workspace)!   | The workspace that owns this inventory.                                                                                                                                                                   |
| `devices`   | [DeviceConnection](./#deviceconnection)! | The devices assigned to this inventory.                                                                                                                                                                   |

***

### InventoryPayload

The result of an inventory mutation.

| Field       | Type                                 | Description                       |
| ----------- | ------------------------------------ | --------------------------------- |
| `inventory` | [Inventory](inventory.md#inventory)! | The created or updated inventory. |

***

### DeviceInventoryRelationPayload

The result of a device inventory link mutation.

| Field                     | Type                                                             | Description                       |
| ------------------------- | ---------------------------------------------------------------- | --------------------------------- |
| `deviceInventoryRelation` | [DeviceInventoryRelation](inventory.md#deviceinventoryrelation)! | The created inventory assignment. |

***

## Inputs

### DeviceInventoryRelationOrder

Ordering options for device inventory relations.

| Field       | Type                                                                                 | Description             |
| ----------- | ------------------------------------------------------------------------------------ | ----------------------- |
| `field`     | [DeviceInventoryRelationOrderField](inventory.md#deviceinventoryrelationorderfield)! | The field to order by.  |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)!              | The direction to order. |

***

### InventoryFilter

Filtering options for inventories.

| Field           | Type     | Description                                         |
| --------------- | -------- | --------------------------------------------------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |

***

### InventoryOrder

Ordering options for inventories.

| Field       | Type                                                                    | Description             |
| ----------- | ----------------------------------------------------------------------- | ----------------------- |
| `field`     | [InventoryOrderField](inventory.md#inventoryorderfield)!                | The field to order by.  |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

***

### InventoryCreateInput

Input for creating a new inventory.

| Field         | Type      | Description                                |
| ------------- | --------- | ------------------------------------------ |
| `workspaceId` | `ID!`     | The workspace that will own the inventory. |
| `title`       | `String!` | The display name.                          |

***

### InventoryUpdateInput

Input for updating an existing inventory.

| Field     | Type     | Description                                                                                     |
| --------- | -------- | ----------------------------------------------------------------------------------------------- |
| `id`      | `ID!`    | The inventory ID to update.                                                                     |
| `version` | `Int`    | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title`   | `String` | The new display name.                                                                           |

***

### InventoryDeleteInput

Input for deleting an inventory.

| Field     | Type  | Description                                                                                     |
| --------- | ----- | ----------------------------------------------------------------------------------------------- |
| `id`      | `ID!` | The inventory ID to delete.                                                                     |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

***

### DeviceInventoryLinkInput

Input for linking a device to an inventory. Both device and inventory must belong to the same workspace.

| Field         | Type  | Description                                                    |
| ------------- | ----- | -------------------------------------------------------------- |
| `deviceId`    | `ID!` | The device ID.                                                 |
| `inventoryId` | `ID!` | The inventory ID. Must be in the same workspace as the device. |

***

### DeviceInventoryUnlinkInput

Input for unlinking a device from an inventory.

| Field      | Type  | Description              |
| ---------- | ----- | ------------------------ |
| `deviceId` | `ID!` | The device ID to unlink. |

***

## Enums

### DeviceInventoryRelationOrderField

Fields available for ordering device inventory relations.

| Value         | Description               |
| ------------- | ------------------------- |
| `ASSIGNED_AT` | Order by assignment date. |

***

### InventoryOrderField

Fields available for ordering inventories.

| Value   | Description     |
| ------- | --------------- |
| `TITLE` | Order by title. |

***

## Interfaces

### InventoryItem

An object that can be assigned to an inventory.

**Implements:** [Node](../../../core-api-reference/common.md#node)

| Field       | Type                                | Description                                       |
| ----------- | ----------------------------------- | ------------------------------------------------- |
| `id`        | `ID!`                               | A globally unique identifier.                     |
| `inventory` | [Inventory](inventory.md#inventory) | The inventory this item is currently assigned to. |

***

## Pagination types

### InventoryConnection

A paginated list of Inventory items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field      | Type                                                         | Description                                                |
| ---------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| `edges`    | \[[InventoryEdge](inventory.md#inventoryedge)!]!             | A list of edges.                                           |
| `nodes`    | \[[Inventory](inventory.md#inventory)!]!                     | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)!  | Information about the current page.                        |
| `total`    | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter.              |

***

### InventoryEdge

An edge in the Inventory connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field    | Type                                 | Description                           |
| -------- | ------------------------------------ | ------------------------------------- |
| `cursor` | `String!`                            | An opaque cursor for this edge.       |
| `node`   | [Inventory](inventory.md#inventory)! | The inventory at the end of the edge. |

***

### DeviceInventoryRelationConnection

A paginated list of DeviceInventoryRelation items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field      | Type                                                                         | Description                                                |
| ---------- | ---------------------------------------------------------------------------- | ---------------------------------------------------------- |
| `edges`    | \[[DeviceInventoryRelationEdge](inventory.md#deviceinventoryrelationedge)!]! | A list of edges.                                           |
| `nodes`    | \[[DeviceInventoryRelation](inventory.md#deviceinventoryrelation)!]!         | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)!                  | Information about the current page.                        |
| `total`    | [CountInfo](../../../core-api-reference/common.md#countinfo)                 | The total count of items matching the filter.              |

***

### DeviceInventoryRelationEdge

An edge in the DeviceInventoryRelation connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field    | Type                                                             | Description                                           |
| -------- | ---------------------------------------------------------------- | ----------------------------------------------------- |
| `cursor` | `String!`                                                        | An opaque cursor for this edge.                       |
| `node`   | [DeviceInventoryRelation](inventory.md#deviceinventoryrelation)! | The device inventory relation at the end of the edge. |

***
