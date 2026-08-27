---
description: >-
  Complete reference for device inventory. Manage warehouses, assign devices to
  inventory locations, and query assignment history.
---

# Inventory

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

Inventory management for device stock, including warehouses, assignments, and device-inventory relationships.

## Queries

### inventory (query)

Retrieves an inventory by its ID.

```graphql
inventory(id: ID!): Inventory
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The ID of the inventory to retrieve. |

**Output types:**

<details>

<summary>Inventory</summary>

An inventory or warehouse record for device stock management.

**Implements:** [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this inventory. |
| `devices` | [DeviceConnection](README.md#deviceconnection)! | The devices assigned to this inventory. |

</details>

---

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

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve inventories for. |
| `filter` | `InventoryFilter` | Filtering options for the returned inventories. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `InventoryOrder` | The ordering options for the returned inventories. |

**Input types:**

<details>

<summary>InventoryFilter</summary>

Filtering options for inventories.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |

</details>

<details>

<summary>InventoryOrder</summary>

Ordering options for inventories.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [InventoryOrderField](#inventoryorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>InventoryConnection</summary>

A paginated list of Inventory items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[InventoryEdge](#inventoryedge)!]! | A list of edges. |
| `nodes` | [[Inventory](#inventory)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Mutations

### inventoryCreate

Creates a new inventory.

```graphql
inventoryCreate(
    input: InventoryCreateInput!
  ): InventoryPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `InventoryCreateInput!` | The input fields for creating the inventory. |

**Input types:**

<details>

<summary>InventoryCreateInput</summary>

Input for creating a new inventory.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the inventory. |
| `title` | `String!` | The display name. |

</details>

**Output types:**

<details>

<summary>InventoryPayload</summary>

The result of an inventory mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `inventory` | [Inventory](#inventory)! | The created or updated inventory. |

</details>

<details>

<summary>Inventory (entity)</summary>

An inventory or warehouse record for device stock management.

**Implements:** [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this inventory. |
| `devices` | [DeviceConnection](README.md#deviceconnection)! | The devices assigned to this inventory. |

</details>

---

### inventoryUpdate

Updates an existing inventory.

```graphql
inventoryUpdate(
    input: InventoryUpdateInput!
  ): InventoryPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `InventoryUpdateInput!` | The input fields for updating the inventory. |

**Input types:**

<details>

<summary>InventoryUpdateInput</summary>

Input for updating an existing inventory.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The inventory ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |

</details>

**Output types:**

<details>

<summary>InventoryPayload</summary>

The result of an inventory mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `inventory` | [Inventory](#inventory)! | The created or updated inventory. |

</details>

<details>

<summary>Inventory (entity)</summary>

An inventory or warehouse record for device stock management.

**Implements:** [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this inventory. |
| `devices` | [DeviceConnection](README.md#deviceconnection)! | The devices assigned to this inventory. |

</details>

---

### inventoryDelete

Deletes an inventory.

```graphql
inventoryDelete(
    input: InventoryDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `InventoryDeleteInput!` | The input fields for deleting the inventory. |

**Input types:**

<details>

<summary>InventoryDeleteInput</summary>

Input for deleting an inventory.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The inventory ID to delete. |
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

### deviceInventoryLink

Links a device to an inventory.

```graphql
deviceInventoryLink(
    input: DeviceInventoryLinkInput!
  ): DeviceInventoryRelationPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceInventoryLinkInput!` | The input fields for linking the device. |

**Input types:**

<details>

<summary>DeviceInventoryLinkInput</summary>

Input for linking a device to an inventory. Both device and inventory must belong to the same workspace.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceId` | `ID!` | The device ID. |
| `inventoryId` | `ID!` | The inventory ID. Must be in the same workspace as the device. |

</details>

**Output types:**

<details>

<summary>DeviceInventoryRelationPayload</summary>

The result of a device inventory link mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceInventoryRelation` | [DeviceInventoryRelation](#deviceinventoryrelation)! | The created inventory assignment. |

</details>

<details>

<summary>DeviceInventoryRelation (entity)</summary>

A record of a device's assignment to an inventory.

**Implements:** [Node](../common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `device` | [Device](README.md#device)! | The device that was assigned. |
| `inventory` | [Inventory](#inventory)! | The inventory the device was assigned to. |
| `assignedAt` | [DateTime](../common.md#datetime)! | The date and time when the device was assigned. |
| `assignedBy` | [Actor](../actors/README.md#actor) | The actor who assigned the device. |

</details>

---

### deviceInventoryUnlink

Unlinks a device from an inventory.

```graphql
deviceInventoryUnlink(
    input: DeviceInventoryUnlinkInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceInventoryUnlinkInput!` | The input fields for unlinking the device. |

**Input types:**

<details>

<summary>DeviceInventoryUnlinkInput</summary>

Input for unlinking a device from an inventory.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceId` | `ID!` | The device ID to unlink. |

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

<a id="deviceinventoryrelation"></a>

### DeviceInventoryRelation

A record of a device's assignment to an inventory.

**Implements:** [Node](../common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `device` | [Device](README.md#device)! | The device that was assigned. |
| `inventory` | [Inventory](#inventory)! | The inventory the device was assigned to. |
| `assignedAt` | [DateTime](../common.md#datetime)! | The date and time when the device was assigned. |
| `assignedBy` | [Actor](../actors/README.md#actor) | The actor who assigned the device. |

---

<a id="inventory"></a>

### Inventory

An inventory or warehouse record for device stock management.

**Implements:** [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this inventory. |
| `devices` | [DeviceConnection](README.md#deviceconnection)! | The devices assigned to this inventory. |

---

<a id="inventorypayload"></a>

### InventoryPayload

The result of an inventory mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `inventory` | [Inventory](#inventory)! | The created or updated inventory. |

---

<a id="deviceinventoryrelationpayload"></a>

### DeviceInventoryRelationPayload

The result of a device inventory link mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceInventoryRelation` | [DeviceInventoryRelation](#deviceinventoryrelation)! | The created inventory assignment. |

---

## Inputs

<a id="deviceinventoryrelationorder"></a>

### DeviceInventoryRelationOrder

Ordering options for device inventory relations.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [DeviceInventoryRelationOrderField](#deviceinventoryrelationorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

---

<a id="inventoryfilter"></a>

### InventoryFilter

Filtering options for inventories.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |

---

<a id="inventoryorder"></a>

### InventoryOrder

Ordering options for inventories.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [InventoryOrderField](#inventoryorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

---

<a id="inventorycreateinput"></a>

### InventoryCreateInput

Input for creating a new inventory.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the inventory. |
| `title` | `String!` | The display name. |

---

<a id="inventoryupdateinput"></a>

### InventoryUpdateInput

Input for updating an existing inventory.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The inventory ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |

---

<a id="inventorydeleteinput"></a>

### InventoryDeleteInput

Input for deleting an inventory.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The inventory ID to delete. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

---

<a id="deviceinventorylinkinput"></a>

### DeviceInventoryLinkInput

Input for linking a device to an inventory. Both device and inventory must belong to the same workspace.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceId` | `ID!` | The device ID. |
| `inventoryId` | `ID!` | The inventory ID. Must be in the same workspace as the device. |

---

<a id="deviceinventoryunlinkinput"></a>

### DeviceInventoryUnlinkInput

Input for unlinking a device from an inventory.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceId` | `ID!` | The device ID to unlink. |

---

## Enums

<a id="deviceinventoryrelationorderfield"></a>

### DeviceInventoryRelationOrderField

Fields available for ordering device inventory relations.

| Value | Description |
| ----- | ----------- |
| `ASSIGNED_AT` | Order by assignment date. |

---

<a id="inventoryorderfield"></a>

### InventoryOrderField

Fields available for ordering inventories.

| Value | Description |
| ----- | ----------- |
| `TITLE` | Order by title. |

---

## Interfaces

<a id="inventoryitem"></a>

### InventoryItem

An object that can be assigned to an inventory.

**Implements:** [Node](../common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `inventory` | [Inventory](#inventory) | The inventory this item is currently assigned to. |

---

## Pagination types

<a id="inventoryconnection"></a>

### InventoryConnection

A paginated list of Inventory items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[InventoryEdge](#inventoryedge)!]! | A list of edges. |
| `nodes` | [[Inventory](#inventory)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="inventoryedge"></a>

### InventoryEdge

An edge in the Inventory connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [Inventory](#inventory)! | The inventory at the end of the edge. |

---

<a id="deviceinventoryrelationconnection"></a>

### DeviceInventoryRelationConnection

A paginated list of DeviceInventoryRelation items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceInventoryRelationEdge](#deviceinventoryrelationedge)!]! | A list of edges. |
| `nodes` | [[DeviceInventoryRelation](#deviceinventoryrelation)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="deviceinventoryrelationedge"></a>

### DeviceInventoryRelationEdge

An edge in the DeviceInventoryRelation connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [DeviceInventoryRelation](#deviceinventoryrelation)! | The device inventory relation at the end of the edge. |

---
