---
description: >-
  Reference for the me query, with the caller's organizations, workspaces,
  products, and roles, and for user-defined catalog items and the mutations that
  manage them.
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

The caller: the actor the token names, and the organizations and workspaces they can switch to.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The caller's actor id, the id the rest of this API uses for them: a user's id from Console, or a member of staff's, the staff token's subject. |
| `title` | `String!` | The caller's display name from the token's own claims: the full name, or the email when the token carries no name. It is read for this answer and stored nowhere. |
| `organizations` | [[AccessibleOrganization](#accessibleorganization)!] | Every organization the caller is an active member of in Console, each with its workspaces other than terminated ones. Each organization lists only the workspaces that the caller's roles reach. `null`, with an error at this path, when the list cannot be read: `PERMISSION_DENIED` when the token carries no usable `actor_id` claim, `NOT_FOUND` when Console holds no active user with that id, `SERVICE_UNAVAILABLE` when Console does not answer or this deployment does not read it. |
| `products` | [[MeProduct](#meproduct)!] | The products the caller may use in the workspace the token names, as billing's catalogue describes them: the codes of the token's `product_codes` claim that a role of the caller scopes, read through the IAM SDK. `isActive` is carried, not filtered on. Empty when the token's workspace does not resolve here or the caller holds no role in it. `null`, with a `SERVICE_UNAVAILABLE` error at this path, when this deployment has no product catalogue to read. |
| `roles` | [[MeRole](#merole)!] | The caller's roles in the workspace the token names: active, of the token's organization, and reaching that workspace — the roles every IAM check of this API is decided over. Empty when the token's workspace does not resolve here or the caller holds no role in it. |

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

The caller: the actor the token names, and the organizations and workspaces they can switch to.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The caller's actor id, the id the rest of this API uses for them: a user's id from Console, or a member of staff's, the staff token's subject. |
| `title` | `String!` | The caller's display name from the token's own claims: the full name, or the email when the token carries no name. It is read for this answer and stored nowhere. |
| `organizations` | [[AccessibleOrganization](#accessibleorganization)!] | Every organization the caller is an active member of in Console, each with its workspaces other than terminated ones. Each organization lists only the workspaces that the caller's roles reach. `null`, with an error at this path, when the list cannot be read: `PERMISSION_DENIED` when the token carries no usable `actor_id` claim, `NOT_FOUND` when Console holds no active user with that id, `SERVICE_UNAVAILABLE` when Console does not answer or this deployment does not read it. |
| `products` | [[MeProduct](#meproduct)!] | The products the caller may use in the workspace the token names, as billing's catalogue describes them: the codes of the token's `product_codes` claim that a role of the caller scopes, read through the IAM SDK. `isActive` is carried, not filtered on. Empty when the token's workspace does not resolve here or the caller holds no role in it. `null`, with a `SERVICE_UNAVAILABLE` error at this path, when this deployment has no product catalogue to read. |
| `roles` | [[MeRole](#merole)!] | The caller's roles in the workspace the token names: active, of the token's organization, and reaching that workspace — the roles every IAM check of this API is decided over. Empty when the token's workspace does not resolve here or the caller holds no role in it. |

---

<a id="meproduct"></a>

### MeProduct

A product the caller may use, as billing's catalogue describes it.

**Implements:** [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../../../core-api-reference/common.md#code)! | The product code billing stores: the value `product_codes` and IAM's product scopes name. |
| `title` | `String!` | Billing's title for the product, or its code while billing holds none. |
| `description` | `String` |  |
| `url` | `String` | The product's user-facing address. |
| `internalPath` | `String` | The product's internal path: an absolute http or https URL, or a path starting with a single `/`. Null while billing holds none. |
| `isActive` | `Boolean!` | Whether billing has the product switched on. Carried, not filtered on. |

---

<a id="merole"></a>

### MeRole

A role of the caller, as IAM holds it.

**Implements:** [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | IAM's role id, the value the token's `role_ids` carries. |
| `title` | `String!` | The human-readable display name. |
| `description` | `String` |  |
| `isSystem` | `Boolean!` | A role IAM ships and no administrator edits. |

---

<a id="accessibleorganization"></a>

### AccessibleOrganization

An organization the caller can switch to.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | Console's organization id: what the context-token exchange takes as `organization_id`. |
| `title` | `String!` | The organization's display name in Console. |
| `workspaces` | [[AccessibleWorkspace](#accessibleworkspace)!]! | The organization's workspaces other than terminated ones. May be empty. |

---

<a id="accessibleworkspace"></a>

### AccessibleWorkspace

A workspace the caller can switch to.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | Console's workspace id: what the exchange takes as `workspace_id`, and what the context token then carries in its `workspace_id` claim. Equal to `Workspace.id` once nvx4 holds a row for this workspace; present whether or not it does. A Console workspace whose id is not a workspace id of this deployment (Console keeps plain-v4 workspaces from before its current id layout) is not listed. |
| `title` | `String!` | The workspace's name in Console. |
| `isActive` | `Boolean!` | Whether the workspace is ready: `true` when Console's status for it is `active`, the rule `Workspace.isActive` follows once the workspace is entered. A workspace still being created, stopped or failed is listed with `false`. |

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
