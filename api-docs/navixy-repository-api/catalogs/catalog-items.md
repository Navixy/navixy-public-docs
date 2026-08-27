---
description: >-
  Queries, mutations, and types for catalog items, the configurable lookup
  entries used for device types, asset types, statuses, and other reference
  data.
---

# Catalog items

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

Operations and types for managing catalog items - the configurable lookup entries.

## Queries

### catalog (query)

Retrieves a catalog by its ID.

```graphql
catalog(id: ID!): Catalog
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The ID of the catalog to retrieve. |

**Output types:**

<details>

<summary>Catalog</summary>

A catalog definition that contains catalog items. Catalogs are themselves catalog items.

**Implements:** [CatalogItem](#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](#catalog)! | Self-reference for the meta-catalog. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `module` | [Module](system.md#module)! | The module this catalog is associated with. |
| `items` | [CatalogItemConnection](#catalogitemconnection)! | The items in this catalog. |

</details>

---

### catalogs

Lists catalogs for a workspace.

```graphql
catalogs(
    workspaceId: ID!
    filter: CatalogItemFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: CatalogItemOrder = { field: ORDER, direction: ASC }
  ): CatalogConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve catalogs for. |
| `filter` | `CatalogItemFilter` | Filtering options for the returned catalogs. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `CatalogItemOrder` | The ordering options for the returned catalogs. |

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
| `field` | [CatalogItemOrderField](#catalogitemorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>CatalogConnection</summary>

A paginated list of Catalog items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[CatalogEdge](#catalogedge)!]! | A list of edges. |
| `nodes` | [[Catalog](#catalog)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Mutations

### catalogCreate

Creates a new user-defined catalog.

```graphql
catalogCreate(
    input: CatalogCreateInput!
  ): CatalogPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `CatalogCreateInput!` | The input fields for creating the catalog. |

**Input types:**

<details>

<summary>CatalogCreateInput</summary>

Input for creating a user-defined catalog (a container for user catalog items).

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the catalog. |
| `moduleId` | `ID` | The module this catalog belongs to. Defaults to the platform's own module (`bdr`) if omitted. |
| `code` | [Code](../common.md#code) | The machine-readable code, unique within the workspace. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `meta` | [CatalogItemMetaInput](#catalogitemmetainput) | The display properties. |

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

<summary>CatalogPayload</summary>

The result of a catalog mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `catalog` | [Catalog](#catalog)! | The created or updated catalog. |

</details>

<details>

<summary>Catalog (entity)</summary>

A catalog definition that contains catalog items. Catalogs are themselves catalog items.

**Implements:** [CatalogItem](#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](#catalog)! | Self-reference for the meta-catalog. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `module` | [Module](system.md#module)! | The module this catalog is associated with. |
| `items` | [CatalogItemConnection](#catalogitemconnection)! | The items in this catalog. |

</details>

---

### catalogUpdate

Updates a user-defined catalog.

```graphql
catalogUpdate(
    input: CatalogUpdateInput!
  ): CatalogPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `CatalogUpdateInput!` | The input fields for updating the catalog. |

**Input types:**

<details>

<summary>CatalogUpdateInput</summary>

Input for updating a user-defined catalog.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The catalog ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `meta` | [CatalogItemMetaInput](#catalogitemmetainput) | The display properties. |

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

<summary>CatalogPayload</summary>

The result of a catalog mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `catalog` | [Catalog](#catalog)! | The created or updated catalog. |

</details>

<details>

<summary>Catalog (entity)</summary>

A catalog definition that contains catalog items. Catalogs are themselves catalog items.

**Implements:** [CatalogItem](#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](#catalog)! | Self-reference for the meta-catalog. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `module` | [Module](system.md#module)! | The module this catalog is associated with. |
| `items` | [CatalogItemConnection](#catalogitemconnection)! | The items in this catalog. |

</details>

---

### catalogDelete

Deletes a user-defined catalog.

```graphql
catalogDelete(
    input: CatalogDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `CatalogDeleteInput!` | The input fields for deleting the catalog. |

**Input types:**

<details>

<summary>CatalogDeleteInput</summary>

Input for deleting a user-defined catalog.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The catalog ID to delete. |
| `version` | `Int` | The current version for optimistic locking. If omitted, deletes regardless of version. |

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

<a id="catalogitemmeta"></a>

### CatalogItemMeta

Metadata about a catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `description` | `String` | A description of the catalog item. Can be localized. |
| `origin` | [CatalogItemOrigin](#catalogitemorigin)! | The origin indicating how this item was created. |
| `canBeDeleted` | `Boolean!` | Whether this item can be deleted. Returns `false` if the item has dependencies or is system-managed. |
| `hidden` | `Boolean!` | Whether this item is hidden from regular UI lists. |

---

<a id="catalog"></a>

### Catalog

A catalog definition that contains catalog items. Catalogs are themselves catalog items.

**Implements:** [CatalogItem](#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](#catalog)! | Self-reference for the meta-catalog. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `module` | [Module](system.md#module)! | The module this catalog is associated with. |
| `items` | [CatalogItemConnection](#catalogitemconnection)! | The items in this catalog. |

---

<a id="catalogpayload"></a>

### CatalogPayload

The result of a catalog mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `catalog` | [Catalog](#catalog)! | The created or updated catalog. |

---

## Inputs

<a id="catalogitemfilter"></a>

### CatalogItemFilter

Filtering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `codes` | [[Code](../common.md#code)!] | Match any of these codes. |

---

<a id="catalogitemchildrenfilter"></a>

### CatalogItemChildrenFilter

Filtering options for catalog item children.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |

---

<a id="catalogitemorder"></a>

### CatalogItemOrder

Ordering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [CatalogItemOrderField](#catalogitemorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

---

<a id="catalogitemmetainput"></a>

### CatalogItemMetaInput

Display properties for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `description` | `String` | The description. |
| `hidden` | `Boolean` | Whether the item is hidden from regular UI lists. |

---

<a id="catalogcreateinput"></a>

### CatalogCreateInput

Input for creating a user-defined catalog (a container for user catalog items).

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the catalog. |
| `moduleId` | `ID` | The module this catalog belongs to. Defaults to the platform's own module (`bdr`) if omitted. |
| `code` | [Code](../common.md#code) | The machine-readable code, unique within the workspace. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `meta` | [CatalogItemMetaInput](#catalogitemmetainput) | The display properties. |

---

<a id="catalogupdateinput"></a>

### CatalogUpdateInput

Input for updating a user-defined catalog.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The catalog ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `meta` | [CatalogItemMetaInput](#catalogitemmetainput) | The display properties. |

---

<a id="catalogdeleteinput"></a>

### CatalogDeleteInput

Input for deleting a user-defined catalog.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The catalog ID to delete. |
| `version` | `Int` | The current version for optimistic locking. If omitted, deletes regardless of version. |

---

<a id="catalogitemdeleteinput"></a>

### CatalogItemDeleteInput

Input for deleting a catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The catalog item ID to delete. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

---

## Enums

<a id="catalogitemorigin"></a>

### CatalogItemOrigin

The origin of a catalog item, indicating how it was created.

| Value | Description |
| ----- | ----------- |
| `SYSTEM` | Predefined by platform. Immutable and available to all workspaces. |
| `WORKSPACE` | Created by the current workspace. |

---

<a id="catalogitemorderfield"></a>

### CatalogItemOrderField

Fields available for ordering catalog items.

| Value | Description |
| ----- | ----------- |
| `ORDER` | Order by display order. |
| `CODE` | Order by code. |
| `TITLE` | Order by title. |
| `CREATED_AT` | Order by creation date and time. |

---

## Interfaces

<a id="catalogitem"></a>

### CatalogItem

A dictionary item that provides reference data for the system.

**Implements:** [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |

---

<a id="hierarchicalcatalogitem"></a>

### HierarchicalCatalogItem

A catalog item that supports parent-child hierarchy.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `parent` | [CatalogItem](#catalogitem) | The parent item in the hierarchy. Null for root items. |

---

## Pagination types

<a id="catalogitemconnection"></a>

### CatalogItemConnection

A paginated list of CatalogItem items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[CatalogItemEdge](#catalogitemedge)!]! | A list of edges. |
| `nodes` | [[CatalogItem](#catalogitem)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="catalogitemedge"></a>

### CatalogItemEdge

An edge in the CatalogItem connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [CatalogItem](#catalogitem)! | The catalog item at the end of the edge. |

---

<a id="catalogconnection"></a>

### CatalogConnection

A paginated list of Catalog items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[CatalogEdge](#catalogedge)!]! | A list of edges. |
| `nodes` | [[Catalog](#catalog)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="catalogedge"></a>

### CatalogEdge

An edge in the Catalog connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [Catalog](#catalog)! | The catalog at the end of the edge. |

---
