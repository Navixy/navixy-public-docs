---
description: >-
  Complete reference for devices: register GPS trackers and sensors, manage
  hardware identifiers, link devices to assets, and track relations.
---

# Devices

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

Devices are GPS trackers, sensors, and other hardware that collect and transmit location and telemetry data.

## Queries

### deviceTypes

Lists device types for a workspace.

```graphql
deviceTypes(
    workspaceId: ID!
    filter: CatalogItemFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: CatalogItemOrder = { field: ORDER, direction: ASC }
  ): DeviceTypeConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve device types for. |
| `filter` | `CatalogItemFilter` | Filtering options for the returned device types. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `CatalogItemOrder` | The ordering options for the returned device types. |

**Input types:**

<details>

<summary>CatalogItemFilter</summary>

Filtering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `codes` | [[Code](../common.md#code)!] | Match any of these codes. |

</details>

<details>

<summary>CatalogItemOrder</summary>

Ordering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [CatalogItemOrderField](../catalogs/catalog-items.md#catalogitemorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>DeviceTypeConnection</summary>

A paginated list of DeviceType items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceTypeEdge](#devicetypeedge)!]! | A list of edges. |
| `nodes` | [[DeviceType](#devicetype)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### deviceStatuses

Lists device statuses for a workspace.

```graphql
deviceStatuses(
    workspaceId: ID!
    filter: CatalogItemFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: CatalogItemOrder = { field: ORDER, direction: ASC }
  ): DeviceStatusConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve device statuses for. |
| `filter` | `CatalogItemFilter` | Filtering options for the returned device statuses. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `CatalogItemOrder` | The ordering options for the returned device statuses. |

**Input types:**

<details>

<summary>CatalogItemFilter</summary>

Filtering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `codes` | [[Code](../common.md#code)!] | Match any of these codes. |

</details>

<details>

<summary>CatalogItemOrder</summary>

Ordering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [CatalogItemOrderField](../catalogs/catalog-items.md#catalogitemorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>DeviceStatusConnection</summary>

A paginated list of DeviceStatus items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceStatusEdge](#devicestatusedge)!]! | A list of edges. |
| `nodes` | [[DeviceStatus](#devicestatus)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### deviceModels

Lists device models with optional vendor filter.

```graphql
deviceModels(
    workspaceId: ID!
    filter: DeviceModelFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: CatalogItemOrder = { field: TITLE, direction: ASC }
  ): DeviceModelConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve device models for. |
| `filter` | `DeviceModelFilter` | Filtering options for the returned device models. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `CatalogItemOrder` | The ordering options for the returned device models. |

**Input types:**

<details>

<summary>DeviceModelFilter</summary>

Filtering options for device models.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `vendorIds` | `[ID!]` | Filter by vendors (OR within field). |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `code` | [Code](../common.md#code) | Exact code match. |

</details>

<details>

<summary>CatalogItemOrder</summary>

Ordering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [CatalogItemOrderField](../catalogs/catalog-items.md#catalogitemorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>DeviceModelConnection</summary>

A paginated list of DeviceModel items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceModelEdge](#devicemodeledge)!]! | A list of edges. |
| `nodes` | [[DeviceModel](#devicemodel)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### deviceVendors

Lists device vendors for a workspace.

```graphql
deviceVendors(
    workspaceId: ID!
    filter: CatalogItemFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: CatalogItemOrder = { field: ORDER, direction: ASC }
  ): DeviceVendorConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve device vendors for. |
| `filter` | `CatalogItemFilter` | Filtering options for the returned device vendors. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `CatalogItemOrder` | The ordering options for the returned device vendors. |

**Input types:**

<details>

<summary>CatalogItemFilter</summary>

Filtering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `codes` | [[Code](../common.md#code)!] | Match any of these codes. |

</details>

<details>

<summary>CatalogItemOrder</summary>

Ordering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [CatalogItemOrderField](../catalogs/catalog-items.md#catalogitemorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>DeviceVendorConnection</summary>

A paginated list of DeviceVendor items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceVendorEdge](#devicevendoredge)!]! | A list of edges. |
| `nodes` | [[DeviceVendor](#devicevendor)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### deviceRelationTypes

Lists device relation types for a workspace.

```graphql
deviceRelationTypes(
    workspaceId: ID!
    filter: CatalogItemFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: CatalogItemOrder = { field: ORDER, direction: ASC }
  ): DeviceRelationTypeConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve device relation types for. |
| `filter` | `CatalogItemFilter` | Filtering options for the returned device relation types. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `CatalogItemOrder` | The ordering options for the returned device relation types. |

**Input types:**

<details>

<summary>CatalogItemFilter</summary>

Filtering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `codes` | [[Code](../common.md#code)!] | Match any of these codes. |

</details>

<details>

<summary>CatalogItemOrder</summary>

Ordering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [CatalogItemOrderField](../catalogs/catalog-items.md#catalogitemorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>DeviceRelationTypeConnection</summary>

A paginated list of DeviceRelationType items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceRelationTypeEdge](#devicerelationtypeedge)!]! | A list of edges. |
| `nodes` | [[DeviceRelationType](#devicerelationtype)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### device (query)

Retrieves a device by its ID.

```graphql
device(id: ID!): Device
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The ID of the device to retrieve. |

**Output types:**

<details>

<summary>Device</summary>

A tracking device such as a GPS tracker, sensor, or beacon.

**Implements:** [Node](../common.md#node), [Titled](../common.md#titled), [Versioned](../common.md#versioned), [InventoryItem](inventory.md#inventoryitem)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this device. |
| `type` | [DeviceType](#devicetype)! | The device type classification. |
| `model` | [DeviceModel](#devicemodel)! | The specific device model. |
| `status` | [DeviceStatus](#devicestatus)! | The current operational status. |
| `identifiers` | [[DeviceIdentifier](#deviceidentifier)!]! | The hardware identifiers for this device (IMEI, serial number, MAC address, etc.). |
| `asset` | [Asset](../assets/README.md#asset) | The asset this device is currently linked to. |
| `inventory` | [Inventory](inventory.md#inventory) | The inventory this device is currently assigned to. |
| `relationsFrom` | [[DeviceRelation](#devicerelation)!]! | The outgoing relationships from this device to other devices. |
| `relationsTo` | [[DeviceRelation](#devicerelation)!]! | The incoming relationships from other devices to this device. |
| `inventoryHistory` | [DeviceInventoryRelationConnection](inventory.md#deviceinventoryrelationconnection)! | The history of inventory assignments for this device. |

</details>

---

### devices

Lists devices for a workspace.

```graphql
devices(
    workspaceId: ID!
    filter: DeviceFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: DeviceOrder = { field: TITLE, direction: ASC }
  ): DeviceConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve devices for. |
| `filter` | `DeviceFilter` | Filtering options for the returned devices. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `DeviceOrder` | The ordering options for the returned devices. |

**Input types:**

<details>

<summary>DeviceFilter</summary>

Filtering options for devices.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `typeIds` | `[ID!]` | Filter by device types (OR within field). |
| `modelIds` | `[ID!]` | Filter by device models (OR within field). |
| `statusIds` | `[ID!]` | Filter by statuses (OR within field). |
| `vendorIds` | `[ID!]` | Filter by vendors (OR within field). |
| `identifierContains` | `String` | Partial match on device identifier value (case-insensitive contains). |
| `inventoryIds` | `[ID!]` | Filter by inventories (OR within field). |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |

</details>

<details>

<summary>DeviceOrder</summary>

Ordering options for devices.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [DeviceOrderField](#deviceorderfield) | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>DeviceConnection</summary>

A paginated list of Device items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceEdge](#deviceedge)!]! | A list of edges. |
| `nodes` | [[Device](#device)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Mutations

### deviceCreate

Creates a new device.

```graphql
deviceCreate(
    input: DeviceCreateInput!
  ): DevicePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceCreateInput!` | The input fields for creating the device. |

**Input types:**

<details>

<summary>DeviceCreateInput</summary>

Input for creating a new device.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the device. |
| `typeId` | `ID!` | The device type ID. |
| `modelId` | `ID!` | The device model ID. |
| `title` | `String` | The device display name. If omitted or blank, the server generates "<vendor.title> <model.title> <identifier.value>" where the identifier is chosen by type priority: IMEI > SERIAL_NUMBER > MAC_ADDRESS, with fallback to identifiers[0] when none of the priority types are present. With no identifiers at all the generated title is "<vendor.title> <model.title>". |
| `identifiers` | [[DeviceIdentifierInput](#deviceidentifierinput)!] | The hardware identifiers. Optional: a device may be registered before its hardware is known. Identifiers can be added later with `deviceIdentifierAdd`. |
| `inventoryId` | `ID` | Optionally assign the new device to an existing inventory. When provided, the inventory must belong to the same workspace; the assignment is atomic with device creation (the device is not created if the assignment fails). |

</details>

<details>

<summary>DeviceIdentifierInput</summary>

Input for a device identifier.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `type` | [DeviceIdType](#deviceidtype)! | The type of identifier. |
| `value` | `String!` | The identifier value. |
| `namespace` | [Code](../common.md#code) | The namespace for uniqueness scope. Null means globally unique. |

</details>

**Output types:**

<details>

<summary>DevicePayload</summary>

The result of a device mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `device` | [Device](#device)! | The created or updated device. |

</details>

<details>

<summary>Device (entity)</summary>

A tracking device such as a GPS tracker, sensor, or beacon.

**Implements:** [Node](../common.md#node), [Titled](../common.md#titled), [Versioned](../common.md#versioned), [InventoryItem](inventory.md#inventoryitem)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this device. |
| `type` | [DeviceType](#devicetype)! | The device type classification. |
| `model` | [DeviceModel](#devicemodel)! | The specific device model. |
| `status` | [DeviceStatus](#devicestatus)! | The current operational status. |
| `identifiers` | [[DeviceIdentifier](#deviceidentifier)!]! | The hardware identifiers for this device (IMEI, serial number, MAC address, etc.). |
| `asset` | [Asset](../assets/README.md#asset) | The asset this device is currently linked to. |
| `inventory` | [Inventory](inventory.md#inventory) | The inventory this device is currently assigned to. |
| `relationsFrom` | [[DeviceRelation](#devicerelation)!]! | The outgoing relationships from this device to other devices. |
| `relationsTo` | [[DeviceRelation](#devicerelation)!]! | The incoming relationships from other devices to this device. |
| `inventoryHistory` | [DeviceInventoryRelationConnection](inventory.md#deviceinventoryrelationconnection)! | The history of inventory assignments for this device. |

</details>

---

### deviceUpdate

Updates an existing device.

```graphql
deviceUpdate(
    input: DeviceUpdateInput!
  ): DevicePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceUpdateInput!` | The input fields for updating the device. |

**Input types:**

<details>

<summary>DeviceUpdateInput</summary>

Input for updating an existing device.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The device ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `modelId` | `ID` | The new device model. |
| `title` | `String` | The new display name. |

</details>

**Output types:**

<details>

<summary>DevicePayload</summary>

The result of a device mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `device` | [Device](#device)! | The created or updated device. |

</details>

<details>

<summary>Device (entity)</summary>

A tracking device such as a GPS tracker, sensor, or beacon.

**Implements:** [Node](../common.md#node), [Titled](../common.md#titled), [Versioned](../common.md#versioned), [InventoryItem](inventory.md#inventoryitem)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this device. |
| `type` | [DeviceType](#devicetype)! | The device type classification. |
| `model` | [DeviceModel](#devicemodel)! | The specific device model. |
| `status` | [DeviceStatus](#devicestatus)! | The current operational status. |
| `identifiers` | [[DeviceIdentifier](#deviceidentifier)!]! | The hardware identifiers for this device (IMEI, serial number, MAC address, etc.). |
| `asset` | [Asset](../assets/README.md#asset) | The asset this device is currently linked to. |
| `inventory` | [Inventory](inventory.md#inventory) | The inventory this device is currently assigned to. |
| `relationsFrom` | [[DeviceRelation](#devicerelation)!]! | The outgoing relationships from this device to other devices. |
| `relationsTo` | [[DeviceRelation](#devicerelation)!]! | The incoming relationships from other devices to this device. |
| `inventoryHistory` | [DeviceInventoryRelationConnection](inventory.md#deviceinventoryrelationconnection)! | The history of inventory assignments for this device. |

</details>

---

### deviceDelete

Deletes a device.

  Every `DEVICE` custom-field value pointing at it is detached in the same transaction, on assets
  and on geo objects alike, so no holder is left naming a device that is gone.

  Rejected with `VALIDATION_ERROR` when one of those fields `isRequired`: detaching would leave
  that holder invalid. Nothing is detached in that case, and the message names the holder and the
  field code — a delete can therefore fail because of a field on an entirely different object.

```graphql
deviceDelete(
    input: DeviceDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceDeleteInput!` | The input fields for deleting the device. |

**Input types:**

<details>

<summary>DeviceDeleteInput</summary>

Input for deleting a device.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The device ID to delete. |
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

### deviceIdentifierAdd

Adds an identifier to a device.

```graphql
deviceIdentifierAdd(
    input: DeviceIdentifierAddInput!
  ): DeviceIdentifierPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceIdentifierAddInput!` | The input fields for adding the identifier. |

**Input types:**

<details>

<summary>DeviceIdentifierAddInput</summary>

Input for adding an identifier to a device.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceId` | `ID!` | The device ID. |
| `identifier` | [DeviceIdentifierInput](#deviceidentifierinput)! | The identifier details. |

</details>

<details>

<summary>DeviceIdentifierInput</summary>

Input for a device identifier.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `type` | [DeviceIdType](#deviceidtype)! | The type of identifier. |
| `value` | `String!` | The identifier value. |
| `namespace` | [Code](../common.md#code) | The namespace for uniqueness scope. Null means globally unique. |

</details>

**Output types:**

<details>

<summary>DeviceIdentifierPayload</summary>

The result of a device identifier mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceIdentifier` | [DeviceIdentifier](#deviceidentifier)! | The added device identifier. |

</details>

<details>

<summary>DeviceIdentifier (entity)</summary>

A hardware identifier for a device.

**Implements:** [Node](../common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `device` | [Device](#device)! | The device this identifier belongs to. |
| `type` | [DeviceIdType](#deviceidtype)! | The type of identifier. |
| `value` | `String!` | The identifier value. |
| `namespace` | [Code](../common.md#code) | The namespace for uniqueness scope. Null means the identifier is globally unique. |

</details>

---

### deviceIdentifierRemove

Removes an identifier from a device.

```graphql
deviceIdentifierRemove(
    input: DeviceIdentifierRemoveInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceIdentifierRemoveInput!` | The input fields for removing the identifier. |

**Input types:**

<details>

<summary>DeviceIdentifierRemoveInput</summary>

Input for removing an identifier from a device.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `identifierId` | `ID!` | The identifier ID to remove. |

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

### deviceRelationCreate

Creates a relationship between devices.

```graphql
deviceRelationCreate(
    input: DeviceRelationCreateInput!
  ): DeviceRelationPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceRelationCreateInput!` | The input fields for creating the relationship. |

**Input types:**

<details>

<summary>DeviceRelationCreateInput</summary>

Input for creating a relationship between devices.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `firstId` | `ID!` | The first device ID. |
| `secondId` | `ID!` | The second device ID. |
| `typeId` | `ID!` | The relationship type ID. |

</details>

**Output types:**

<details>

<summary>DeviceRelationPayload</summary>

The result of a device relation mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceRelation` | [DeviceRelation](#devicerelation)! | The created device relationship. |

</details>

<details>

<summary>DeviceRelation (entity)</summary>

A relationship between two devices.

**Implements:** [Node](../common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `first` | [Device](#device)! | The first device in the relationship. |
| `second` | [Device](#device)! | The second device in the relationship. |
| `type` | [DeviceRelationType](#devicerelationtype)! | The type of relationship. |

</details>

---

### deviceRelationRemove

Removes a device relationship.

```graphql
deviceRelationRemove(
    input: DeviceRelationRemoveInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceRelationRemoveInput!` | The input fields for removing the relationship. |

**Input types:**

<details>

<summary>DeviceRelationRemoveInput</summary>

Input for removing a device relationship.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The relationship ID to remove. |

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

### deviceTypeCreate

Creates a new device type.

```graphql
deviceTypeCreate(
    input: DeviceTypeCreateInput!
  ): DeviceTypePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceTypeCreateInput!` | The input fields for creating the device type. |

**Input types:**

<details>

<summary>DeviceTypeCreateInput</summary>

Input for creating a device type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the item. |
| `code` | [Code](../common.md#code) | The machine-readable code. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

</details>

<details>

<summary>CatalogItemMetaInput</summary>

Display properties for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `description` | `String` | The description. |
| `hidden` | `Boolean` | Whether the item is hidden from regular UI lists. |

</details>

**Output types:**

<details>

<summary>DeviceTypePayload</summary>

The result of a device type mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceType` | [DeviceType](#devicetype)! | The created or updated device type. |

</details>

<details>

<summary>DeviceType (entity)</summary>

A classification type for devices.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |

</details>

---

### deviceTypeUpdate

Updates a device type.

```graphql
deviceTypeUpdate(
    input: DeviceTypeUpdateInput!
  ): DeviceTypePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceTypeUpdateInput!` | The input fields for updating the device type. |

**Input types:**

<details>

<summary>DeviceTypeUpdateInput</summary>

Input for updating a device type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

</details>

<details>

<summary>CatalogItemMetaInput</summary>

Display properties for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `description` | `String` | The description. |
| `hidden` | `Boolean` | Whether the item is hidden from regular UI lists. |

</details>

**Output types:**

<details>

<summary>DeviceTypePayload</summary>

The result of a device type mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceType` | [DeviceType](#devicetype)! | The created or updated device type. |

</details>

<details>

<summary>DeviceType (entity)</summary>

A classification type for devices.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |

</details>

---

### deviceTypeDelete

Deletes a device type.

```graphql
deviceTypeDelete(
    input: CatalogItemDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `CatalogItemDeleteInput!` | The input fields for deleting the device type. |

**Input types:**

<details>

<summary>CatalogItemDeleteInput</summary>

Input for deleting a catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The catalog item ID to delete. |
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

### deviceStatusCreate

Creates a new device status.

```graphql
deviceStatusCreate(
    input: DeviceStatusCreateInput!
  ): DeviceStatusPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceStatusCreateInput!` | The input fields for creating the device status. |

**Input types:**

<details>

<summary>DeviceStatusCreateInput</summary>

Input for creating a device status.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the item. |
| `code` | [Code](../common.md#code) | The machine-readable code. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

</details>

<details>

<summary>CatalogItemMetaInput</summary>

Display properties for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `description` | `String` | The description. |
| `hidden` | `Boolean` | Whether the item is hidden from regular UI lists. |

</details>

**Output types:**

<details>

<summary>DeviceStatusPayload</summary>

The result of a device status mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceStatus` | [DeviceStatus](#devicestatus)! | The created or updated device status. |

</details>

<details>

<summary>DeviceStatus (entity)</summary>

An operational status for devices.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |

</details>

---

### deviceStatusUpdate

Updates a device status.

```graphql
deviceStatusUpdate(
    input: DeviceStatusUpdateInput!
  ): DeviceStatusPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `DeviceStatusUpdateInput!` | The input fields for updating the device status. |

**Input types:**

<details>

<summary>DeviceStatusUpdateInput</summary>

Input for updating a device status.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

</details>

<details>

<summary>CatalogItemMetaInput</summary>

Display properties for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `description` | `String` | The description. |
| `hidden` | `Boolean` | Whether the item is hidden from regular UI lists. |

</details>

**Output types:**

<details>

<summary>DeviceStatusPayload</summary>

The result of a device status mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceStatus` | [DeviceStatus](#devicestatus)! | The created or updated device status. |

</details>

<details>

<summary>DeviceStatus (entity)</summary>

An operational status for devices.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |

</details>

---

### deviceStatusDelete

Deletes a device status.

```graphql
deviceStatusDelete(
    input: CatalogItemDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `CatalogItemDeleteInput!` | The input fields for deleting the device status. |

**Input types:**

<details>

<summary>CatalogItemDeleteInput</summary>

Input for deleting a catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The catalog item ID to delete. |
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

## Objects

<a id="devicevendor"></a>

### DeviceVendor

A device manufacturer or vendor.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `models` | [DeviceModelConnection](#devicemodelconnection)! | Device models produced by this vendor. |

---

<a id="devicemodel"></a>

### DeviceModel

A specific device model produced by a vendor.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `vendor` | [DeviceVendor](#devicevendor)! | The vendor that manufactures this model. |

---

<a id="devicetype"></a>

### DeviceType

A classification type for devices.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |

---

<a id="devicestatus"></a>

### DeviceStatus

An operational status for devices.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |

---

<a id="devicerelationtype"></a>

### DeviceRelationType

A type of relationship between two devices.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |

---

<a id="device"></a>

### Device

A tracking device such as a GPS tracker, sensor, or beacon.

**Implements:** [Node](../common.md#node), [Titled](../common.md#titled), [Versioned](../common.md#versioned), [InventoryItem](inventory.md#inventoryitem)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this device. |
| `type` | [DeviceType](#devicetype)! | The device type classification. |
| `model` | [DeviceModel](#devicemodel)! | The specific device model. |
| `status` | [DeviceStatus](#devicestatus)! | The current operational status. |
| `identifiers` | [[DeviceIdentifier](#deviceidentifier)!]! | The hardware identifiers for this device (IMEI, serial number, MAC address, etc.). |
| `asset` | [Asset](../assets/README.md#asset) | The asset this device is currently linked to. |
| `inventory` | [Inventory](inventory.md#inventory) | The inventory this device is currently assigned to. |
| `relationsFrom` | [[DeviceRelation](#devicerelation)!]! | The outgoing relationships from this device to other devices. |
| `relationsTo` | [[DeviceRelation](#devicerelation)!]! | The incoming relationships from other devices to this device. |
| `inventoryHistory` | [DeviceInventoryRelationConnection](inventory.md#deviceinventoryrelationconnection)! | The history of inventory assignments for this device. |

---

<a id="deviceidentifier"></a>

### DeviceIdentifier

A hardware identifier for a device.

**Implements:** [Node](../common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `device` | [Device](#device)! | The device this identifier belongs to. |
| `type` | [DeviceIdType](#deviceidtype)! | The type of identifier. |
| `value` | `String!` | The identifier value. |
| `namespace` | [Code](../common.md#code) | The namespace for uniqueness scope. Null means the identifier is globally unique. |

---

<a id="devicerelation"></a>

### DeviceRelation

A relationship between two devices.

**Implements:** [Node](../common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `first` | [Device](#device)! | The first device in the relationship. |
| `second` | [Device](#device)! | The second device in the relationship. |
| `type` | [DeviceRelationType](#devicerelationtype)! | The type of relationship. |

---

<a id="devicepayload"></a>

### DevicePayload

The result of a device mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `device` | [Device](#device)! | The created or updated device. |

---

<a id="deviceidentifierpayload"></a>

### DeviceIdentifierPayload

The result of a device identifier mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceIdentifier` | [DeviceIdentifier](#deviceidentifier)! | The added device identifier. |

---

<a id="devicerelationpayload"></a>

### DeviceRelationPayload

The result of a device relation mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceRelation` | [DeviceRelation](#devicerelation)! | The created device relationship. |

---

<a id="devicetypepayload"></a>

### DeviceTypePayload

The result of a device type mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceType` | [DeviceType](#devicetype)! | The created or updated device type. |

---

<a id="devicestatuspayload"></a>

### DeviceStatusPayload

The result of a device status mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceStatus` | [DeviceStatus](#devicestatus)! | The created or updated device status. |

---

## Inputs

<a id="devicefilter"></a>

### DeviceFilter

Filtering options for devices.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `typeIds` | `[ID!]` | Filter by device types (OR within field). |
| `modelIds` | `[ID!]` | Filter by device models (OR within field). |
| `statusIds` | `[ID!]` | Filter by statuses (OR within field). |
| `vendorIds` | `[ID!]` | Filter by vendors (OR within field). |
| `identifierContains` | `String` | Partial match on device identifier value (case-insensitive contains). |
| `inventoryIds` | `[ID!]` | Filter by inventories (OR within field). |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |

---

<a id="deviceorder"></a>

### DeviceOrder

Ordering options for devices.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [DeviceOrderField](#deviceorderfield) | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

---

<a id="devicemodelfilter"></a>

### DeviceModelFilter

Filtering options for device models.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `vendorIds` | `[ID!]` | Filter by vendors (OR within field). |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `code` | [Code](../common.md#code) | Exact code match. |

---

<a id="devicecreateinput"></a>

### DeviceCreateInput

Input for creating a new device.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the device. |
| `typeId` | `ID!` | The device type ID. |
| `modelId` | `ID!` | The device model ID. |
| `title` | `String` | The device display name. If omitted or blank, the server generates "<vendor.title> <model.title> <identifier.value>" where the identifier is chosen by type priority: IMEI > SERIAL_NUMBER > MAC_ADDRESS, with fallback to identifiers[0] when none of the priority types are present. With no identifiers at all the generated title is "<vendor.title> <model.title>". |
| `identifiers` | [[DeviceIdentifierInput](#deviceidentifierinput)!] | The hardware identifiers. Optional: a device may be registered before its hardware is known. Identifiers can be added later with `deviceIdentifierAdd`. |
| `inventoryId` | `ID` | Optionally assign the new device to an existing inventory. When provided, the inventory must belong to the same workspace; the assignment is atomic with device creation (the device is not created if the assignment fails). |

---

<a id="deviceupdateinput"></a>

### DeviceUpdateInput

Input for updating an existing device.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The device ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `modelId` | `ID` | The new device model. |
| `title` | `String` | The new display name. |

---

<a id="devicedeleteinput"></a>

### DeviceDeleteInput

Input for deleting a device.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The device ID to delete. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

---

<a id="deviceidentifierinput"></a>

### DeviceIdentifierInput

Input for a device identifier.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `type` | [DeviceIdType](#deviceidtype)! | The type of identifier. |
| `value` | `String!` | The identifier value. |
| `namespace` | [Code](../common.md#code) | The namespace for uniqueness scope. Null means globally unique. |

---

<a id="deviceidentifieraddinput"></a>

### DeviceIdentifierAddInput

Input for adding an identifier to a device.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deviceId` | `ID!` | The device ID. |
| `identifier` | [DeviceIdentifierInput](#deviceidentifierinput)! | The identifier details. |

---

<a id="deviceidentifierremoveinput"></a>

### DeviceIdentifierRemoveInput

Input for removing an identifier from a device.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `identifierId` | `ID!` | The identifier ID to remove. |

---

<a id="devicerelationcreateinput"></a>

### DeviceRelationCreateInput

Input for creating a relationship between devices.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `firstId` | `ID!` | The first device ID. |
| `secondId` | `ID!` | The second device ID. |
| `typeId` | `ID!` | The relationship type ID. |

---

<a id="devicerelationremoveinput"></a>

### DeviceRelationRemoveInput

Input for removing a device relationship.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The relationship ID to remove. |

---

<a id="devicetypecreateinput"></a>

### DeviceTypeCreateInput

Input for creating a device type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the item. |
| `code` | [Code](../common.md#code) | The machine-readable code. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

---

<a id="devicetypeupdateinput"></a>

### DeviceTypeUpdateInput

Input for updating a device type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

---

<a id="devicestatuscreateinput"></a>

### DeviceStatusCreateInput

Input for creating a device status.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the item. |
| `code` | [Code](../common.md#code) | The machine-readable code. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

---

<a id="devicestatusupdateinput"></a>

### DeviceStatusUpdateInput

Input for updating a device status.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

---

## Enums

<a id="deviceidtype"></a>

### DeviceIdType

The type of hardware identifier used to identify a device.

| Value | Description |
| ----- | ----------- |
| `GUID` | A GUID/UUID identifier. |
| `IMEI` | International Mobile Equipment Identity. A 15-digit number. |
| `MEID_HEX` | Mobile Equipment Identifier in hexadecimal format. |
| `MEID_DEC` | Mobile Equipment Identifier in decimal format. |
| `MAC_ADDRESS` | Media Access Control address of a network interface. |
| `SERIAL_NUMBER` | Manufacturer-assigned serial number. |
| `CUSTOM` | A custom identifier type defined by the workspace. |

---

<a id="deviceorderfield"></a>

### DeviceOrderField

Fields available for ordering devices.

| Value | Description |
| ----- | ----------- |
| `TITLE` | Order by title. |

---

## Pagination types

<a id="deviceconnection"></a>

### DeviceConnection

A paginated list of Device items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceEdge](#deviceedge)!]! | A list of edges. |
| `nodes` | [[Device](#device)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="deviceedge"></a>

### DeviceEdge

An edge in the Device connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [Device](#device)! | The device at the end of the edge. |

---

<a id="devicetypeconnection"></a>

### DeviceTypeConnection

A paginated list of DeviceType items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceTypeEdge](#devicetypeedge)!]! | A list of edges. |
| `nodes` | [[DeviceType](#devicetype)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="devicetypeedge"></a>

### DeviceTypeEdge

An edge in the DeviceType connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [DeviceType](#devicetype)! | The device type at the end of the edge. |

---

<a id="devicestatusconnection"></a>

### DeviceStatusConnection

A paginated list of DeviceStatus items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceStatusEdge](#devicestatusedge)!]! | A list of edges. |
| `nodes` | [[DeviceStatus](#devicestatus)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="devicestatusedge"></a>

### DeviceStatusEdge

An edge in the DeviceStatus connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [DeviceStatus](#devicestatus)! | The device status at the end of the edge. |

---

<a id="devicemodelconnection"></a>

### DeviceModelConnection

A paginated list of DeviceModel items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceModelEdge](#devicemodeledge)!]! | A list of edges. |
| `nodes` | [[DeviceModel](#devicemodel)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="devicemodeledge"></a>

### DeviceModelEdge

An edge in the DeviceModel connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [DeviceModel](#devicemodel)! | The device model at the end of the edge. |

---

<a id="devicevendorconnection"></a>

### DeviceVendorConnection

A paginated list of DeviceVendor items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceVendorEdge](#devicevendoredge)!]! | A list of edges. |
| `nodes` | [[DeviceVendor](#devicevendor)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="devicevendoredge"></a>

### DeviceVendorEdge

An edge in the DeviceVendor connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [DeviceVendor](#devicevendor)! | The device vendor at the end of the edge. |

---

<a id="devicerelationtypeconnection"></a>

### DeviceRelationTypeConnection

A paginated list of DeviceRelationType items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[DeviceRelationTypeEdge](#devicerelationtypeedge)!]! | A list of edges. |
| `nodes` | [[DeviceRelationType](#devicerelationtype)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="devicerelationtypeedge"></a>

### DeviceRelationTypeEdge

An edge in the DeviceRelationType connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [DeviceRelationType](#devicerelationtype)! | The device relation type at the end of the edge. |

---
