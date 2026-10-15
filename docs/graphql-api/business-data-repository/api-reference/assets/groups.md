---
description: >-
  Complete reference for asset groups: group types, groups, memberships, and
  history.
tags:
  - reference
  - beta
---

# Asset groups

Asset groups allow organizing assets into logical collections for easier management and reporting.

## Queries

### assetGroupTypes

Lists asset group types in the caller's workspace.

```graphql
assetGroupTypes(
    filter: CatalogItemFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: CatalogItemOrder = { field: ORDER, direction: ASC }
  ): AssetGroupTypeConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `CatalogItemFilter` | Filtering options for the returned asset group types. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../../pagination.md). |
| `orderBy` | `CatalogItemOrder` | The ordering options for the returned asset group types. |

**Input types:**

<details>

<summary>CatalogItemFilter</summary>

Filtering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `codes` | [[Code](../../../core-api-reference/common.md#code)!] | Match any of these codes. |

</details>

<details>

<summary>CatalogItemOrder</summary>

Ordering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [CatalogItemOrderField](../catalogs/catalog-items.md#catalogitemorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>AssetGroupTypeConnection</summary>

A paginated list of AssetGroupType items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AssetGroupTypeEdge](#assetgrouptypeedge)!]! | A list of edges. |
| `nodes` | [[AssetGroupType](#assetgrouptype)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### assetGroup (query)

Retrieves an asset group by its ID.

```graphql
assetGroup(id: ID!): AssetGroup
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The ID of the asset group to retrieve. |

**Output types:**

<details>

<summary>AssetGroup</summary>

A group of assets.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this group. |
| `type` | [AssetGroupType](#assetgrouptype) | The group type. Immutable after creation. |
| `color` | [HexColorCode](../../../core-api-reference/common.md#hexcolorcode) | The color for UI display in hexadecimal format. |
| `currentAssets` | [AssetConnection](README.md#assetconnection)! | The assets currently in this group. |
| `history` | [AssetGroupItemConnection](#assetgroupitemconnection)! | The full membership history for this group. |

</details>

---

### assetGroups

Lists asset groups in the caller's workspace.

```graphql
assetGroups(
    filter: AssetGroupFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: AssetGroupOrder = { field: TITLE, direction: ASC }
  ): AssetGroupConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `AssetGroupFilter` | Filtering options for the returned asset groups. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../../pagination.md). |
| `orderBy` | `AssetGroupOrder` | The ordering options for the returned asset groups. |

**Input types:**

<details>

<summary>AssetGroupFilter</summary>

Filtering options for asset groups.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `typeIds` | `[ID!]` | Filter by group types (OR within field). |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |

</details>

<details>

<summary>AssetGroupOrder</summary>

Ordering options for asset groups.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AssetGroupOrderField](#assetgrouporderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>AssetGroupConnection</summary>

A paginated list of AssetGroup items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AssetGroupEdge](#assetgroupedge)!]! | A list of edges. |
| `nodes` | [[AssetGroup](#assetgroup)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Mutations

### assetGroupCreate

Creates a new asset group.

```graphql
assetGroupCreate(
    input: AssetGroupCreateInput!
  ): AssetGroupPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `AssetGroupCreateInput!` | The input fields for creating the asset group. |

**Input types:**

<details>

<summary>AssetGroupCreateInput</summary>

Input for creating a new asset group.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `typeId` | `ID` | The group type ID. Immutable after creation. |
| `title` | `String!` | The group display name. |
| `color` | [HexColorCode](../../../core-api-reference/common.md#hexcolorcode) | The color for UI display. |
| `assetIds` | `[ID!]` | Initial list of asset IDs to add to the group. |

</details>

**Output types:**

<details>

<summary>AssetGroupPayload</summary>

The result of an asset group mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetGroup` | [AssetGroup](#assetgroup)! | The created or updated asset group. |

</details>

<details>

<summary>AssetGroup (entity)</summary>

A group of assets.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this group. |
| `type` | [AssetGroupType](#assetgrouptype) | The group type. Immutable after creation. |
| `color` | [HexColorCode](../../../core-api-reference/common.md#hexcolorcode) | The color for UI display in hexadecimal format. |
| `currentAssets` | [AssetConnection](README.md#assetconnection)! | The assets currently in this group. |
| `history` | [AssetGroupItemConnection](#assetgroupitemconnection)! | The full membership history for this group. |

</details>

---

### assetGroupUpdate

Updates an existing asset group.

```graphql
assetGroupUpdate(
    input: AssetGroupUpdateInput!
  ): AssetGroupPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `AssetGroupUpdateInput!` | The input fields for updating the asset group. |

**Input types:**

<details>

<summary>AssetGroupUpdateInput</summary>

Input for updating an existing asset group.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The asset group ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `color` | [HexColorCode](../../../core-api-reference/common.md#hexcolorcode) | The new color. |
| `assetIds` | `[ID!]` | Full replacement list of asset IDs in the group. If provided, replaces all current memberships. |

</details>

**Output types:**

<details>

<summary>AssetGroupPayload</summary>

The result of an asset group mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetGroup` | [AssetGroup](#assetgroup)! | The created or updated asset group. |

</details>

<details>

<summary>AssetGroup (entity)</summary>

A group of assets.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this group. |
| `type` | [AssetGroupType](#assetgrouptype) | The group type. Immutable after creation. |
| `color` | [HexColorCode](../../../core-api-reference/common.md#hexcolorcode) | The color for UI display in hexadecimal format. |
| `currentAssets` | [AssetConnection](README.md#assetconnection)! | The assets currently in this group. |
| `history` | [AssetGroupItemConnection](#assetgroupitemconnection)! | The full membership history for this group. |

</details>

---

### assetGroupDelete

Deletes an asset group.

  Refused with `IN_USE` while another live entity points at it through one of its own fields,
  custom-field values and field definitions included; the error's `blockers` name the first five
  of them, and `hasMoreBlockers` says whether it left any out.

```graphql
assetGroupDelete(
    input: AssetGroupDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `AssetGroupDeleteInput!` | The input fields for deleting the asset group. |

**Input types:**

<details>

<summary>AssetGroupDeleteInput</summary>

Input for deleting an asset group.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The asset group ID to delete. |
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

### assetGroupItemsAdd

Adds assets to a group.

```graphql
assetGroupItemsAdd(
    input: AssetGroupItemsAddInput!
  ): AssetGroupPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `AssetGroupItemsAddInput!` | The input fields for adding assets to the group. |

**Input types:**

<details>

<summary>AssetGroupItemsAddInput</summary>

Input for adding assets to a group.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `groupId` | `ID!` | The group ID. |
| `assetIds` | `[ID!]!` | The asset IDs to add. |

</details>

**Output types:**

<details>

<summary>AssetGroupPayload</summary>

The result of an asset group mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetGroup` | [AssetGroup](#assetgroup)! | The created or updated asset group. |

</details>

<details>

<summary>AssetGroup (entity)</summary>

A group of assets.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this group. |
| `type` | [AssetGroupType](#assetgrouptype) | The group type. Immutable after creation. |
| `color` | [HexColorCode](../../../core-api-reference/common.md#hexcolorcode) | The color for UI display in hexadecimal format. |
| `currentAssets` | [AssetConnection](README.md#assetconnection)! | The assets currently in this group. |
| `history` | [AssetGroupItemConnection](#assetgroupitemconnection)! | The full membership history for this group. |

</details>

---

### assetGroupItemsRemove

Removes assets from a group.

```graphql
assetGroupItemsRemove(
    input: AssetGroupItemsRemoveInput!
  ): AssetGroupPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `AssetGroupItemsRemoveInput!` | The input fields for removing assets from the group. |

**Input types:**

<details>

<summary>AssetGroupItemsRemoveInput</summary>

Input for removing assets from a group.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `groupId` | `ID!` | The group ID. |
| `assetIds` | `[ID!]!` | The asset IDs to remove. |

</details>

**Output types:**

<details>

<summary>AssetGroupPayload</summary>

The result of an asset group mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetGroup` | [AssetGroup](#assetgroup)! | The created or updated asset group. |

</details>

<details>

<summary>AssetGroup (entity)</summary>

A group of assets.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this group. |
| `type` | [AssetGroupType](#assetgrouptype) | The group type. Immutable after creation. |
| `color` | [HexColorCode](../../../core-api-reference/common.md#hexcolorcode) | The color for UI display in hexadecimal format. |
| `currentAssets` | [AssetConnection](README.md#assetconnection)! | The assets currently in this group. |
| `history` | [AssetGroupItemConnection](#assetgroupitemconnection)! | The full membership history for this group. |

</details>

---

### assetGroupTypeCreate

Creates a new asset group type.

```graphql
assetGroupTypeCreate(
    input: AssetGroupTypeCreateInput!
  ): AssetGroupTypePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `AssetGroupTypeCreateInput!` | The input fields for creating the asset group type. |

**Input types:**

<details>

<summary>AssetGroupTypeCreateInput</summary>

Input for creating an asset group type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../../../core-api-reference/common.md#code) | The machine-readable code. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `allowedAssetTypes` | [[AssetGroupTypeConstraintInput](#assetgrouptypeconstraintinput)!] | The asset types that groups of this type are meant to hold. The API stores the list but doesn't check it when assets are added. Naming an asset type twice returns `DUPLICATE`. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

</details>

<details>

<summary>AssetGroupTypeConstraintInput</summary>

One entry of an asset group type's `allowedAssetTypes` list.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetTypeId` | `ID!` | The asset type ID. |
| `maxItems` | `Int` | How many assets of this type one group is meant to hold. Stored and returned, but not checked when assets are added. Null means no number is set. |

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

<summary>AssetGroupTypePayload</summary>

The result of an asset group type mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetGroupType` | [AssetGroupType](#assetgrouptype)! | The created or updated asset group type. |

</details>

<details>

<summary>AssetGroupType (entity)</summary>

A catalog item that classifies asset groups.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../../../core-api-reference/common.md#code)! | A machine-readable code, unique within the catalog for a given workspace. A workspace item may reuse the code of the system item it overrides. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `allowedAssetTypes` | [[AssetGroupTypeConstraint](#assetgrouptypeconstraint)!]! | The asset types that groups of this type are meant to hold, each with an optional `maxItems`. The API stores and returns them but doesn't check them when assets are added. |

</details>

---

### assetGroupTypeUpdate

Updates an asset group type.

```graphql
assetGroupTypeUpdate(
    input: AssetGroupTypeUpdateInput!
  ): AssetGroupTypePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `AssetGroupTypeUpdateInput!` | The input fields for updating the asset group type. |

**Input types:**

<details>

<summary>AssetGroupTypeUpdateInput</summary>

Input for updating an asset group type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `allowedAssetTypes` | [[AssetGroupTypeConstraintInput](#assetgrouptypeconstraintinput)!] | Replaces the whole stored list. Null means no change, and an empty list clears it. Naming an asset type twice returns `DUPLICATE`. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

</details>

<details>

<summary>AssetGroupTypeConstraintInput</summary>

One entry of an asset group type's `allowedAssetTypes` list.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetTypeId` | `ID!` | The asset type ID. |
| `maxItems` | `Int` | How many assets of this type one group is meant to hold. Stored and returned, but not checked when assets are added. Null means no number is set. |

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

<summary>AssetGroupTypePayload</summary>

The result of an asset group type mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetGroupType` | [AssetGroupType](#assetgrouptype)! | The created or updated asset group type. |

</details>

<details>

<summary>AssetGroupType (entity)</summary>

A catalog item that classifies asset groups.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../../../core-api-reference/common.md#code)! | A machine-readable code, unique within the catalog for a given workspace. A workspace item may reuse the code of the system item it overrides. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `allowedAssetTypes` | [[AssetGroupTypeConstraint](#assetgrouptypeconstraint)!]! | The asset types that groups of this type are meant to hold, each with an optional `maxItems`. The API stores and returns them but doesn't check them when assets are added. |

</details>

---

### assetGroupTypeDelete

Deletes an asset group type.

  Refused with `IN_USE` while another live entity points at it through one of its own fields,
  custom-field values and field definitions included; the error's `blockers` name the first five
  of them, and `hasMoreBlockers` says whether it left any out.

```graphql
assetGroupTypeDelete(
    input: CatalogItemDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `CatalogItemDeleteInput!` | The input fields for deleting the asset group type. |

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

<a id="assetgrouptype"></a>

### AssetGroupType

A catalog item that classifies asset groups.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../../../core-api-reference/common.md#code)! | A machine-readable code, unique within the catalog for a given workspace. A workspace item may reuse the code of the system item it overrides. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `allowedAssetTypes` | [[AssetGroupTypeConstraint](#assetgrouptypeconstraint)!]! | The asset types that groups of this type are meant to hold, each with an optional `maxItems`. The API stores and returns them but doesn't check them when assets are added. |

---

<a id="assetgrouptypeconstraint"></a>

### AssetGroupTypeConstraint

One entry of an asset group type's `allowedAssetTypes` list.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetType` | [AssetType](README.md#assettype)! | The asset type that groups of this type are meant to hold. |
| `maxItems` | `Int` | How many assets of this type one group is meant to hold. Stored and returned, but not checked when assets are added. Null means no number is set. |

---

<a id="assetgroup"></a>

### AssetGroup

A group of assets.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace that owns this group. |
| `type` | [AssetGroupType](#assetgrouptype) | The group type. Immutable after creation. |
| `color` | [HexColorCode](../../../core-api-reference/common.md#hexcolorcode) | The color for UI display in hexadecimal format. |
| `currentAssets` | [AssetConnection](README.md#assetconnection)! | The assets currently in this group. |
| `history` | [AssetGroupItemConnection](#assetgroupitemconnection)! | The full membership history for this group. |

**Arguments of `currentAssets`**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | [AssetFilter](README.md#assetfilter) | Filtering options for the returned assets. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../../pagination.md). |
| `orderBy` | [AssetOrder](README.md#assetorder) | The ordering options for the returned assets. Default: `{ field: TITLE, direction: ASC }`. |

**Arguments of `history`**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | [AssetGroupItemFilter](#assetgroupitemfilter) | Filtering options for the returned history. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../../pagination.md). |
| `orderBy` | [AssetGroupItemOrder](#assetgroupitemorder) | The ordering options for the returned history. Default: `{ field: ATTACHED_AT, direction: DESC }`. |

---

<a id="assetgroupitem"></a>

### AssetGroupItem

A record of an asset's membership in a group.

**Implements:** [Node](../../../core-api-reference/common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `group` | [AssetGroup](#assetgroup)! | The group containing the asset. |
| `asset` | [Asset](README.md#asset)! | The asset in the group. |
| `attachedAt` | [DateTime](../../../core-api-reference/common.md#datetime)! | The date and time when the asset was added to the group. |
| `detachedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | The date and time when the asset was removed from the group. Null means the asset is currently attached. |

---

<a id="assetgrouppayload"></a>

### AssetGroupPayload

The result of an asset group mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetGroup` | [AssetGroup](#assetgroup)! | The created or updated asset group. |

---

<a id="assetgrouptypepayload"></a>

### AssetGroupTypePayload

The result of an asset group type mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetGroupType` | [AssetGroupType](#assetgrouptype)! | The created or updated asset group type. |

---

## Inputs

<a id="assetgroupfilter"></a>

### AssetGroupFilter

Filtering options for asset groups.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `typeIds` | `[ID!]` | Filter by group types (OR within field). |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |

---

<a id="assetgrouporder"></a>

### AssetGroupOrder

Ordering options for asset groups.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AssetGroupOrderField](#assetgrouporderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

<a id="assetgroupitemfilter"></a>

### AssetGroupItemFilter

Filtering options for asset group items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `activeOnly` | `Boolean` | If true, return only currently attached items. |

---

<a id="assetgroupitemorder"></a>

### AssetGroupItemOrder

Ordering options for asset group items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AssetGroupItemOrderField](#assetgroupitemorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

<a id="assetgroupcreateinput"></a>

### AssetGroupCreateInput

Input for creating a new asset group.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `typeId` | `ID` | The group type ID. Immutable after creation. |
| `title` | `String!` | The group display name. |
| `color` | [HexColorCode](../../../core-api-reference/common.md#hexcolorcode) | The color for UI display. |
| `assetIds` | `[ID!]` | Initial list of asset IDs to add to the group. |

---

<a id="assetgroupupdateinput"></a>

### AssetGroupUpdateInput

Input for updating an existing asset group.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The asset group ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `color` | [HexColorCode](../../../core-api-reference/common.md#hexcolorcode) | The new color. |
| `assetIds` | `[ID!]` | Full replacement list of asset IDs in the group. If provided, replaces all current memberships. |

---

<a id="assetgroupdeleteinput"></a>

### AssetGroupDeleteInput

Input for deleting an asset group.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The asset group ID to delete. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

---

<a id="assetgroupitemsaddinput"></a>

### AssetGroupItemsAddInput

Input for adding assets to a group.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `groupId` | `ID!` | The group ID. |
| `assetIds` | `[ID!]!` | The asset IDs to add. |

---

<a id="assetgroupitemsremoveinput"></a>

### AssetGroupItemsRemoveInput

Input for removing assets from a group.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `groupId` | `ID!` | The group ID. |
| `assetIds` | `[ID!]!` | The asset IDs to remove. |

---

<a id="assetgrouptypecreateinput"></a>

### AssetGroupTypeCreateInput

Input for creating an asset group type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../../../core-api-reference/common.md#code) | The machine-readable code. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `allowedAssetTypes` | [[AssetGroupTypeConstraintInput](#assetgrouptypeconstraintinput)!] | The asset types that groups of this type are meant to hold. The API stores the list but doesn't check it when assets are added. Naming an asset type twice returns `DUPLICATE`. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

---

<a id="assetgrouptypeupdateinput"></a>

### AssetGroupTypeUpdateInput

Input for updating an asset group type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `allowedAssetTypes` | [[AssetGroupTypeConstraintInput](#assetgrouptypeconstraintinput)!] | Replaces the whole stored list. Null means no change, and an empty list clears it. Naming an asset type twice returns `DUPLICATE`. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

---

<a id="assetgrouptypeconstraintinput"></a>

### AssetGroupTypeConstraintInput

One entry of an asset group type's `allowedAssetTypes` list.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetTypeId` | `ID!` | The asset type ID. |
| `maxItems` | `Int` | How many assets of this type one group is meant to hold. Stored and returned, but not checked when assets are added. Null means no number is set. |

---

## Enums

<a id="assetgrouporderfield"></a>

### AssetGroupOrderField

Fields available for ordering asset groups.

| Value | Description |
| ----- | ----------- |
| `TITLE` | Order by title. |

---

<a id="assetgroupitemorderfield"></a>

### AssetGroupItemOrderField

Fields available for ordering asset group items.

| Value | Description |
| ----- | ----------- |
| `ATTACHED_AT` | Order by attachment date. |

---

## Pagination types

<a id="assetgroupconnection"></a>

### AssetGroupConnection

A paginated list of AssetGroup items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AssetGroupEdge](#assetgroupedge)!]! | A list of edges. |
| `nodes` | [[AssetGroup](#assetgroup)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="assetgroupedge"></a>

### AssetGroupEdge

An edge in the AssetGroup connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [AssetGroup](#assetgroup)! | The asset group at the end of the edge. |

---

<a id="assetgroupitemconnection"></a>

### AssetGroupItemConnection

A paginated list of AssetGroupItem items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AssetGroupItemEdge](#assetgroupitemedge)!]! | A list of edges. |
| `nodes` | [[AssetGroupItem](#assetgroupitem)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="assetgroupitemedge"></a>

### AssetGroupItemEdge

An edge in the AssetGroupItem connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [AssetGroupItem](#assetgroupitem)! | The asset group item at the end of the edge. |

---

<a id="assetgrouptypeconnection"></a>

### AssetGroupTypeConnection

A paginated list of AssetGroupType items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AssetGroupTypeEdge](#assetgrouptypeedge)!]! | A list of edges. |
| `nodes` | [[AssetGroupType](#assetgrouptype)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="assetgrouptypeedge"></a>

### AssetGroupTypeEdge

An edge in the AssetGroupType connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [AssetGroupType](#assetgrouptype)! | The asset group type at the end of the edge. |

---

## See also

* [Pagination](../../../pagination.md)
* [Filtering and sorting](../../../filtering-and-sorting/README.md)
* [Error handling](../../../error-handling.md)
* [Optimistic locking](../../../optimistic-locking.md)
* [Limits](../../../limits.md)
