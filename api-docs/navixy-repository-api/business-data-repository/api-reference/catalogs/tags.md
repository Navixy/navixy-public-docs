---
description: >-
  Complete reference for tags. Create and manage flexible labels for classifying
  assets, geo objects, and other entities.
---

# Tags

{% include "../../../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

Tag management for flexible entity classification.

## Queries

### tags

Lists tags for a workspace.

```graphql
tags(
    workspaceId: ID!
    filter: TagFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: CatalogItemOrder = { field: TITLE, direction: ASC }
  ): TagConnection!
```

**Arguments**

| Name          | Type               | Description                                                                                                     |
| ------------- | ------------------ | --------------------------------------------------------------------------------------------------------------- |
| `workspaceId` | `ID!`              | The workspace to retrieve tags for.                                                                             |
| `filter`      | `TagFilter`        | Filtering options for the returned tags.                                                                        |
| `first`       | `Int`              | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination).     |
| `after`       | `String`           | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination).  |
| `last`        | `Int`              | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination).      |
| `before`      | `String`           | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy`     | `CatalogItemOrder` | The ordering options for the returned tags.                                                                     |

**Input types:**

<details>

<summary>TagFilter</summary>

Filtering options for tags.

| Field           | Type     | Description                                         |
| --------------- | -------- | --------------------------------------------------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |

</details>

<details>

<summary>CatalogItemOrder</summary>

Ordering options for catalog items.

| Field       | Type                                                                    | Description             |
| ----------- | ----------------------------------------------------------------------- | ----------------------- |
| `field`     | [CatalogItemOrderField](catalog-items.md#catalogitemorderfield)!        | The field to order by.  |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>TagConnection</summary>

A paginated list of Tag items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field      | Type                                                         | Description                                                |
| ---------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| `edges`    | \[[TagEdge](tags.md#tagedge)!]!                              | A list of edges.                                           |
| `nodes`    | \[[Tag](tags.md#tag)!]!                                      | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)!  | Information about the current page.                        |
| `total`    | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter.              |

</details>

***

## Mutations

### tagCreate

Creates a new tag.

```graphql
tagCreate(
    input: TagCreateInput!
  ): TagPayload
```

**Arguments**

| Name    | Type              | Description                            |
| ------- | ----------------- | -------------------------------------- |
| `input` | `TagCreateInput!` | The input fields for creating the tag. |

**Input types:**

<details>

<summary>TagCreateInput</summary>

Input for creating a tag.

| Field           | Type                                                          | Description                                                         |
| --------------- | ------------------------------------------------------------- | ------------------------------------------------------------------- |
| `workspaceId`   | `ID!`                                                         | The workspace that will own the item.                               |
| `code`          | [Code](../../../core-api-reference/common.md#code)            | The machine-readable code. Auto-generated from title if omitted.    |
| `title`         | `String!`                                                     | The display name.                                                   |
| `order`         | `Int`                                                         | The display order. Auto-calculated as last position if omitted.     |
| `entityTypeIds` | `[ID!]`                                                       | The entity types this tag can be applied to. Empty means universal. |
| `meta`          | [CatalogItemMetaInput](catalog-items.md#catalogitemmetainput) | The display properties.                                             |

</details>

<details>

<summary>CatalogItemMetaInput</summary>

Display properties for catalog items.

| Field         | Type      | Description                                       |
| ------------- | --------- | ------------------------------------------------- |
| `description` | `String`  | The description.                                  |
| `hidden`      | `Boolean` | Whether the item is hidden from regular UI lists. |

</details>

**Output types:**

<details>

<summary>TagPayload</summary>

The result of a tag mutation.

| Field | Type                | Description                 |
| ----- | ------------------- | --------------------------- |
| `tag` | [Tag](tags.md#tag)! | The created or updated tag. |

</details>

<details>

<summary>Tag (entity)</summary>

A tag for labeling and categorizing entities.

**Implements:** [CatalogItem](catalog-items.md#catalogitem), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field         | Type                                                 | Description                                                                     |
| ------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------- |
| `id`          | `ID!`                                                | A globally unique identifier.                                                   |
| `version`     | `Int!`                                               | The version number for optimistic locking.                                      |
| `title`       | `String!`                                            | The human-readable display name. Can be localized.                              |
| `code`        | [Code](../../../core-api-reference/common.md#code)!  | A machine-readable code, unique within the catalog scope.                       |
| `order`       | `Int!`                                               | The display order within the same level or category.                            |
| `catalog`     | [Catalog](catalog-items.md#catalog)!                 | The catalog this item belongs to.                                               |
| `workspace`   | [Workspace](../workspaces/#workspace)                | The workspace that owns this item. Null for system items.                       |
| `meta`        | [CatalogItemMeta](catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `entityTypes` | \[[EntityType](system.md#entitytype)!]!              | The entity types this tag can be applied to. Empty means the tag is universal.  |

</details>

***

### tagUpdate

Updates a tag.

```graphql
tagUpdate(
    input: TagUpdateInput!
  ): TagPayload
```

**Arguments**

| Name    | Type              | Description                            |
| ------- | ----------------- | -------------------------------------- |
| `input` | `TagUpdateInput!` | The input fields for updating the tag. |

**Input types:**

<details>

<summary>TagUpdateInput</summary>

Input for updating a tag.

| Field           | Type                                                          | Description                                                                                     |
| --------------- | ------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `id`            | `ID!`                                                         | The item ID to update.                                                                          |
| `version`       | `Int`                                                         | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title`         | `String`                                                      | The new display name.                                                                           |
| `order`         | `Int`                                                         | The new display order.                                                                          |
| `entityTypeIds` | `[ID!]`                                                       | Replace entity types. Null means no change, empty means universal.                              |
| `meta`          | [CatalogItemMetaInput](catalog-items.md#catalogitemmetainput) | The display properties.                                                                         |

</details>

<details>

<summary>CatalogItemMetaInput</summary>

Display properties for catalog items.

| Field         | Type      | Description                                       |
| ------------- | --------- | ------------------------------------------------- |
| `description` | `String`  | The description.                                  |
| `hidden`      | `Boolean` | Whether the item is hidden from regular UI lists. |

</details>

**Output types:**

<details>

<summary>TagPayload</summary>

The result of a tag mutation.

| Field | Type                | Description                 |
| ----- | ------------------- | --------------------------- |
| `tag` | [Tag](tags.md#tag)! | The created or updated tag. |

</details>

<details>

<summary>Tag (entity)</summary>

A tag for labeling and categorizing entities.

**Implements:** [CatalogItem](catalog-items.md#catalogitem), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field         | Type                                                 | Description                                                                     |
| ------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------- |
| `id`          | `ID!`                                                | A globally unique identifier.                                                   |
| `version`     | `Int!`                                               | The version number for optimistic locking.                                      |
| `title`       | `String!`                                            | The human-readable display name. Can be localized.                              |
| `code`        | [Code](../../../core-api-reference/common.md#code)!  | A machine-readable code, unique within the catalog scope.                       |
| `order`       | `Int!`                                               | The display order within the same level or category.                            |
| `catalog`     | [Catalog](catalog-items.md#catalog)!                 | The catalog this item belongs to.                                               |
| `workspace`   | [Workspace](../workspaces/#workspace)                | The workspace that owns this item. Null for system items.                       |
| `meta`        | [CatalogItemMeta](catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `entityTypes` | \[[EntityType](system.md#entitytype)!]!              | The entity types this tag can be applied to. Empty means the tag is universal.  |

</details>

***

### tagDelete

Deletes a tag.

```graphql
tagDelete(
    input: CatalogItemDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name    | Type                      | Description                            |
| ------- | ------------------------- | -------------------------------------- |
| `input` | `CatalogItemDeleteInput!` | The input fields for deleting the tag. |

**Input types:**

<details>

<summary>CatalogItemDeleteInput</summary>

Input for deleting a catalog item.

| Field     | Type  | Description                                                                                     |
| --------- | ----- | ----------------------------------------------------------------------------------------------- |
| `id`      | `ID!` | The catalog item ID to delete.                                                                  |
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

## Objects

### Tag

A tag for labeling and categorizing entities.

**Implements:** [CatalogItem](catalog-items.md#catalogitem), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field         | Type                                                 | Description                                                                     |
| ------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------- |
| `id`          | `ID!`                                                | A globally unique identifier.                                                   |
| `version`     | `Int!`                                               | The version number for optimistic locking.                                      |
| `title`       | `String!`                                            | The human-readable display name. Can be localized.                              |
| `code`        | [Code](../../../core-api-reference/common.md#code)!  | A machine-readable code, unique within the catalog scope.                       |
| `order`       | `Int!`                                               | The display order within the same level or category.                            |
| `catalog`     | [Catalog](catalog-items.md#catalog)!                 | The catalog this item belongs to.                                               |
| `workspace`   | [Workspace](../workspaces/#workspace)                | The workspace that owns this item. Null for system items.                       |
| `meta`        | [CatalogItemMeta](catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `entityTypes` | \[[EntityType](system.md#entitytype)!]!              | The entity types this tag can be applied to. Empty means the tag is universal.  |

***

### TagPayload

The result of a tag mutation.

| Field | Type                | Description                 |
| ----- | ------------------- | --------------------------- |
| `tag` | [Tag](tags.md#tag)! | The created or updated tag. |

***

## Inputs

### TagFilter

Filtering options for tags.

| Field           | Type     | Description                                         |
| --------------- | -------- | --------------------------------------------------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |

***

### TagCreateInput

Input for creating a tag.

| Field           | Type                                                          | Description                                                         |
| --------------- | ------------------------------------------------------------- | ------------------------------------------------------------------- |
| `workspaceId`   | `ID!`                                                         | The workspace that will own the item.                               |
| `code`          | [Code](../../../core-api-reference/common.md#code)            | The machine-readable code. Auto-generated from title if omitted.    |
| `title`         | `String!`                                                     | The display name.                                                   |
| `order`         | `Int`                                                         | The display order. Auto-calculated as last position if omitted.     |
| `entityTypeIds` | `[ID!]`                                                       | The entity types this tag can be applied to. Empty means universal. |
| `meta`          | [CatalogItemMetaInput](catalog-items.md#catalogitemmetainput) | The display properties.                                             |

***

### TagUpdateInput

Input for updating a tag.

| Field           | Type                                                          | Description                                                                                     |
| --------------- | ------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `id`            | `ID!`                                                         | The item ID to update.                                                                          |
| `version`       | `Int`                                                         | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title`         | `String`                                                      | The new display name.                                                                           |
| `order`         | `Int`                                                         | The new display order.                                                                          |
| `entityTypeIds` | `[ID!]`                                                       | Replace entity types. Null means no change, empty means universal.                              |
| `meta`          | [CatalogItemMetaInput](catalog-items.md#catalogitemmetainput) | The display properties.                                                                         |

***

## Pagination types

### TagConnection

A paginated list of Tag items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field      | Type                                                         | Description                                                |
| ---------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| `edges`    | \[[TagEdge](tags.md#tagedge)!]!                              | A list of edges.                                           |
| `nodes`    | \[[Tag](tags.md#tag)!]!                                      | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)!  | Information about the current page.                        |
| `total`    | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter.              |

***

### TagEdge

An edge in the Tag connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field    | Type                | Description                     |
| -------- | ------------------- | ------------------------------- |
| `cursor` | `String!`           | An opaque cursor for this edge. |
| `node`   | [Tag](tags.md#tag)! | The tag at the end of the edge. |

***
