---
description: >-
  Reference for the me query, with the caller's organizations, workspaces,
  products, and roles, and for user-defined catalog items and their mutations.
tags:
  - reference
  - beta
---

# Users

The `me` query, which describes the caller, and the catalog items that users define in their workspace.

## Queries

### me (query)

The caller, with the organizations and workspaces they can switch to.

```graphql
me: Me!
```

**Output types:**

<details>

<summary>Me</summary>

The caller of the request, and the organizations and workspaces that the caller can switch to.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the caller. Other operations of the API use the same ID for the caller. |
| `title` | `String!` | The caller's display name, taken from the access token: the full name, or the email address when the token has no name. The API doesn't store it. |
| `organizations` | [[AccessibleOrganization](#accessibleorganization)!] | Every organization in which the caller is an active member in Navixy Console, with its workspaces except terminated ones. Each organization lists only the workspaces that the caller's roles reach. `null`, with an error at this path, when the list cannot be read: `PERMISSION_DENIED` when the access token names no caller, `NOT_FOUND` when Navixy Console has no active user with the caller's ID, and `SERVICE_UNAVAILABLE` when Navixy Console doesn't answer. |
| `products` | [[MeProduct](#meproduct)!] | The products that the caller may use in the workspace of the access token: the products of the token that a role of the caller covers. The list includes products whose `isActive` is `false`. Empty when the token's workspace isn't found or the caller has no role in it. `null`, with a `SERVICE_UNAVAILABLE` error at this path, when the product list can't be read. |
| `roles` | [[MeRole](#merole)!] | The caller's roles in the workspace of the access token: the active roles of the token's organization that reach this workspace. The API checks every permission against these roles. Empty when the token's workspace isn't found or the caller has no role in it. |

</details>

---

## Mutations

### userCatalogItemCreate

Creates a new user catalog item.

```graphql
userCatalogItemCreate(
    input: UserCatalogItemCreateInput!
  ): UserCatalogItemPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `UserCatalogItemCreateInput!` | The input fields for creating the item. |

**Input types:**

<details>

<summary>UserCatalogItemCreateInput</summary>

Input for creating a user catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `catalogId` | `ID!` | The catalog to add the item to. |
| `code` | [Code](../../../core-api-reference/common.md#code) | The machine-readable code, unique within the catalog and workspace. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `parentId` | `ID` | The parent item ID for hierarchical catalogs. |
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

<summary>UserCatalogItemPayload</summary>

The result of a user catalog item mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `item` | [UserCatalogItem](#usercatalogitem)! | The created or updated user catalog item. |

</details>

<details>

<summary>UserCatalogItem (entity)</summary>

A user-defined catalog item that supports hierarchical workspace.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [HierarchicalCatalogItem](../catalogs/catalog-items.md#hierarchicalcatalogitem), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../../../core-api-reference/common.md#code)! | A machine-readable code, unique within the catalog for a given workspace. A workspace item may reuse the code of the system item it overrides. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `parent` | [UserCatalogItem](#usercatalogitem) | The parent item in the hierarchy. Null for root items. |
| `children` | [UserCatalogItemConnection](#usercatalogitemconnection)! | The child items in the hierarchy. |

</details>

---

### userCatalogItemUpdate

Updates a user catalog item.

```graphql
userCatalogItemUpdate(
    input: UserCatalogItemUpdateInput!
  ): UserCatalogItemPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `UserCatalogItemUpdateInput!` | The input fields for updating the item. |

**Input types:**

<details>

<summary>UserCatalogItemUpdateInput</summary>

Input for updating a user catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `parentId` | `ID` | The new parent ID for hierarchical items. |
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

<summary>UserCatalogItemPayload</summary>

The result of a user catalog item mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `item` | [UserCatalogItem](#usercatalogitem)! | The created or updated user catalog item. |

</details>

<details>

<summary>UserCatalogItem (entity)</summary>

A user-defined catalog item that supports hierarchical workspace.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [HierarchicalCatalogItem](../catalogs/catalog-items.md#hierarchicalcatalogitem), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../../../core-api-reference/common.md#code)! | A machine-readable code, unique within the catalog for a given workspace. A workspace item may reuse the code of the system item it overrides. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `parent` | [UserCatalogItem](#usercatalogitem) | The parent item in the hierarchy. Null for root items. |
| `children` | [UserCatalogItemConnection](#usercatalogitemconnection)! | The child items in the hierarchy. |

</details>

---

### userCatalogItemDelete

Deletes a user catalog item.

  Refused with `IN_USE` while another live entity points at it through one of its own fields,
  custom-field values and field definitions included; the error's `blockers` name the first five
  of them, and `hasMoreBlockers` says whether it left any out.

```graphql
userCatalogItemDelete(
    input: CatalogItemDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `CatalogItemDeleteInput!` | The input fields for deleting the item. |

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

<a id="usercatalogitem"></a>

### UserCatalogItem

A user-defined catalog item that supports hierarchical workspace.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [HierarchicalCatalogItem](../catalogs/catalog-items.md#hierarchicalcatalogitem), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../../../core-api-reference/common.md#code)! | A machine-readable code, unique within the catalog for a given workspace. A workspace item may reuse the code of the system item it overrides. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](../catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `parent` | [UserCatalogItem](#usercatalogitem) | The parent item in the hierarchy. Null for root items. |
| `children` | [UserCatalogItemConnection](#usercatalogitemconnection)! | The child items in the hierarchy. |

**Arguments of `children`**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | [CatalogItemChildrenFilter](../catalogs/catalog-items.md#catalogitemchildrenfilter) | Filtering options for the returned children. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../../pagination.md). |
| `orderBy` | [CatalogItemOrder](../catalogs/catalog-items.md#catalogitemorder) | The ordering options for the returned children. Default: `{ field: ORDER, direction: ASC }`. |

---

<a id="me"></a>

### Me

The caller of the request, and the organizations and workspaces that the caller can switch to.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the caller. Other operations of the API use the same ID for the caller. |
| `title` | `String!` | The caller's display name, taken from the access token: the full name, or the email address when the token has no name. The API doesn't store it. |
| `organizations` | [[AccessibleOrganization](#accessibleorganization)!] | Every organization in which the caller is an active member in Navixy Console, with its workspaces except terminated ones. Each organization lists only the workspaces that the caller's roles reach. `null`, with an error at this path, when the list cannot be read: `PERMISSION_DENIED` when the access token names no caller, `NOT_FOUND` when Navixy Console has no active user with the caller's ID, and `SERVICE_UNAVAILABLE` when Navixy Console doesn't answer. |
| `products` | [[MeProduct](#meproduct)!] | The products that the caller may use in the workspace of the access token: the products of the token that a role of the caller covers. The list includes products whose `isActive` is `false`. Empty when the token's workspace isn't found or the caller has no role in it. `null`, with a `SERVICE_UNAVAILABLE` error at this path, when the product list can't be read. |
| `roles` | [[MeRole](#merole)!] | The caller's roles in the workspace of the access token: the active roles of the token's organization that reach this workspace. The API checks every permission against these roles. Empty when the token's workspace isn't found or the caller has no role in it. |

---

<a id="meproduct"></a>

### MeProduct

A product that the caller may use.

**Implements:** [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../../../core-api-reference/common.md#code)! | The product code. Access tokens and roles name the product by this code. |
| `title` | `String!` | The name of the product, or its code when the product has no name. |
| `description` | `String` |  |
| `url` | `String` | The product's user-facing address. |
| `internalPath` | `String` | The product's internal address: an absolute http or https URL, or a path that starts with a single `/`. Null when none is set. |
| `isActive` | `Boolean!` | Whether the product is switched on. |

---

<a id="merole"></a>

### MeRole

A role of the caller.

**Implements:** [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the role. The access token lists the caller's roles by these IDs. |
| `title` | `String!` | The human-readable display name. |
| `description` | `String` |  |
| `isSystem` | `Boolean!` | `true` for a built-in role, which no administrator can edit. |

---

<a id="accessibleorganization"></a>

### AccessibleOrganization

An organization the caller can switch to.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the organization in Navixy Console. |
| `title` | `String!` | The organization's display name in Console. |
| `workspaces` | [[AccessibleWorkspace](#accessibleworkspace)!]! | The organization's workspaces other than terminated ones. May be empty. |

---

<a id="accessibleworkspace"></a>

### AccessibleWorkspace

A workspace the caller can switch to.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The ID of the workspace in Navixy Console: the workspace ID that an access token names. When the workspace has data in Business Data Repository, its `Workspace.id` is the same. Workspaces created before the current ID format aren't listed. |
| `title` | `String!` | The workspace's name in Console. |
| `isActive` | `Boolean!` | Whether the workspace is ready: `true` when its status in Navixy Console is `active`. `Workspace.isActive` follows the same rule. A workspace still being created, stopped or failed is listed with `false`. |

---

<a id="usercatalogitempayload"></a>

### UserCatalogItemPayload

The result of a user catalog item mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `item` | [UserCatalogItem](#usercatalogitem)! | The created or updated user catalog item. |

---

## Inputs

<a id="usercatalogitemcreateinput"></a>

### UserCatalogItemCreateInput

Input for creating a user catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `catalogId` | `ID!` | The catalog to add the item to. |
| `code` | [Code](../../../core-api-reference/common.md#code) | The machine-readable code, unique within the catalog and workspace. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `parentId` | `ID` | The parent item ID for hierarchical catalogs. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

---

<a id="usercatalogitemupdateinput"></a>

### UserCatalogItemUpdateInput

Input for updating a user catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `parentId` | `ID` | The new parent ID for hierarchical items. |
| `meta` | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

---

## Pagination types

<a id="usercatalogitemconnection"></a>

### UserCatalogItemConnection

A paginated list of UserCatalogItem items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[UserCatalogItemEdge](#usercatalogitemedge)!]! | A list of edges. |
| `nodes` | [[UserCatalogItem](#usercatalogitem)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="usercatalogitemedge"></a>

### UserCatalogItemEdge

An edge in the UserCatalogItem connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [UserCatalogItem](#usercatalogitem)! | The user catalog item at the end of the edge. |

---

## See also

* [Pagination](../../../pagination.md)
* [Filtering and sorting](../../../filtering-and-sorting/README.md)
* [Error handling](../../../error-handling.md)
* [Optimistic locking](../../../optimistic-locking.md)
* [Limits](../../../limits.md)
