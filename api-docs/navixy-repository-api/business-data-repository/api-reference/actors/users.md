---
description: >-
  Reference for user accounts and user-defined catalog items: the read-only User
  object and the mutations that manage hierarchical catalog entries.
---

# Users

{% include "../../../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

User accounts representing human operators who access the system through the UI or API.

## Mutations

### userCatalogItemCreate

Creates a new user catalog item.

```graphql
userCatalogItemCreate(
    input: UserCatalogItemCreateInput!
  ): UserCatalogItemPayload
```

**Arguments**

| Name    | Type                          | Description                             |
| ------- | ----------------------------- | --------------------------------------- |
| `input` | `UserCatalogItemCreateInput!` | The input fields for creating the item. |

**Input types:**

<details>

<summary>UserCatalogItemCreateInput</summary>

Input for creating a user catalog item.

| Field         | Type                                                                      | Description                                                                                               |
| ------------- | ------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| `workspaceId` | `ID!`                                                                     | The workspace that will own the item.                                                                     |
| `catalogId`   | `ID!`                                                                     | The catalog to add the item to.                                                                           |
| `code`        | [Code](../../../core-api-reference/common.md#code)                        | The machine-readable code, unique within the catalog and workspace. Auto-generated from title if omitted. |
| `title`       | `String!`                                                                 | The display name.                                                                                         |
| `order`       | `Int`                                                                     | The display order. Auto-calculated as last position if omitted.                                           |
| `parentId`    | `ID`                                                                      | The parent item ID for hierarchical catalogs.                                                             |
| `meta`        | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties.                                                                                   |

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

<summary>UserCatalogItemPayload</summary>

The result of a user catalog item mutation.

| Field  | Type                                         | Description                               |
| ------ | -------------------------------------------- | ----------------------------------------- |
| `item` | [UserCatalogItem](users.md#usercatalogitem)! | The created or updated user catalog item. |

</details>

<details>

<summary>UserCatalogItem (entity)</summary>

A user-defined catalog item that supports hierarchical workspace.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [HierarchicalCatalogItem](../catalogs/catalog-items.md#hierarchicalcatalogitem), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field       | Type                                                             | Description                                                                     |
| ----------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| `id`        | `ID!`                                                            | A globally unique identifier.                                                   |
| `version`   | `Int!`                                                           | The version number for optimistic locking.                                      |
| `title`     | `String!`                                                        | The human-readable display name. Can be localized.                              |
| `code`      | [Code](../../../core-api-reference/common.md#code)!              | A machine-readable code, unique within the catalog scope.                       |
| `order`     | `Int!`                                                           | The display order within the same level or category.                            |
| `catalog`   | [Catalog](../catalogs/catalog-items.md#catalog)!                 | The catalog this item belongs to.                                               |
| `workspace` | [Workspace](../workspaces/#workspace)                            | The workspace that owns this item. Null for system items.                       |
| `meta`      | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `parent`    | [UserCatalogItem](users.md#usercatalogitem)                      | The parent item in the hierarchy. Null for root items.                          |
| `children`  | [UserCatalogItemConnection](users.md#usercatalogitemconnection)! | The child items in the hierarchy.                                               |

</details>

***

### userCatalogItemUpdate

Updates a user catalog item.

```graphql
userCatalogItemUpdate(
    input: UserCatalogItemUpdateInput!
  ): UserCatalogItemPayload
```

**Arguments**

| Name    | Type                          | Description                             |
| ------- | ----------------------------- | --------------------------------------- |
| `input` | `UserCatalogItemUpdateInput!` | The input fields for updating the item. |

**Input types:**

<details>

<summary>UserCatalogItemUpdateInput</summary>

Input for updating a user catalog item.

| Field      | Type                                                                      | Description                                                                                     |
| ---------- | ------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `id`       | `ID!`                                                                     | The item ID to update.                                                                          |
| `version`  | `Int`                                                                     | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title`    | `String`                                                                  | The new display name.                                                                           |
| `order`    | `Int`                                                                     | The new display order.                                                                          |
| `parentId` | `ID`                                                                      | The new parent ID for hierarchical items.                                                       |
| `meta`     | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties.                                                                         |

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

<summary>UserCatalogItemPayload</summary>

The result of a user catalog item mutation.

| Field  | Type                                         | Description                               |
| ------ | -------------------------------------------- | ----------------------------------------- |
| `item` | [UserCatalogItem](users.md#usercatalogitem)! | The created or updated user catalog item. |

</details>

<details>

<summary>UserCatalogItem (entity)</summary>

A user-defined catalog item that supports hierarchical workspace.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [HierarchicalCatalogItem](../catalogs/catalog-items.md#hierarchicalcatalogitem), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field       | Type                                                             | Description                                                                     |
| ----------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| `id`        | `ID!`                                                            | A globally unique identifier.                                                   |
| `version`   | `Int!`                                                           | The version number for optimistic locking.                                      |
| `title`     | `String!`                                                        | The human-readable display name. Can be localized.                              |
| `code`      | [Code](../../../core-api-reference/common.md#code)!              | A machine-readable code, unique within the catalog scope.                       |
| `order`     | `Int!`                                                           | The display order within the same level or category.                            |
| `catalog`   | [Catalog](../catalogs/catalog-items.md#catalog)!                 | The catalog this item belongs to.                                               |
| `workspace` | [Workspace](../workspaces/#workspace)                            | The workspace that owns this item. Null for system items.                       |
| `meta`      | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `parent`    | [UserCatalogItem](users.md#usercatalogitem)                      | The parent item in the hierarchy. Null for root items.                          |
| `children`  | [UserCatalogItemConnection](users.md#usercatalogitemconnection)! | The child items in the hierarchy.                                               |

</details>

***

### userCatalogItemDelete

Deletes a user catalog item.

```graphql
userCatalogItemDelete(
    input: CatalogItemDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name    | Type                      | Description                             |
| ------- | ------------------------- | --------------------------------------- |
| `input` | `CatalogItemDeleteInput!` | The input fields for deleting the item. |

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

### UserCatalogItem

A user-defined catalog item that supports hierarchical workspace.

**Implements:** [CatalogItem](../catalogs/catalog-items.md#catalogitem), [HierarchicalCatalogItem](../catalogs/catalog-items.md#hierarchicalcatalogitem), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field       | Type                                                             | Description                                                                     |
| ----------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| `id`        | `ID!`                                                            | A globally unique identifier.                                                   |
| `version`   | `Int!`                                                           | The version number for optimistic locking.                                      |
| `title`     | `String!`                                                        | The human-readable display name. Can be localized.                              |
| `code`      | [Code](../../../core-api-reference/common.md#code)!              | A machine-readable code, unique within the catalog scope.                       |
| `order`     | `Int!`                                                           | The display order within the same level or category.                            |
| `catalog`   | [Catalog](../catalogs/catalog-items.md#catalog)!                 | The catalog this item belongs to.                                               |
| `workspace` | [Workspace](../workspaces/#workspace)                            | The workspace that owns this item. Null for system items.                       |
| `meta`      | [CatalogItemMeta](../catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `parent`    | [UserCatalogItem](users.md#usercatalogitem)                      | The parent item in the hierarchy. Null for root items.                          |
| `children`  | [UserCatalogItemConnection](users.md#usercatalogitemconnection)! | The child items in the hierarchy.                                               |

***

### User

A human user account authenticated via an identity provider.

**Implements:** [Actor](./#actor), [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field                | Type                                                                | Description                                                                                                                                                                                               |
| -------------------- | ------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`                 | `ID!`                                                               | A globally unique identifier.                                                                                                                                                                             |
| `version`            | `Int!`                                                              | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title`              | `String!`                                                           | The display name for the user. This is the user's full name for display purposes.                                                                                                                         |
| `name`               | [PersonName](./#personname)!                                        | The structured name components from the identity provider.                                                                                                                                                |
| `identityProvider`   | `String!`                                                           | The identity provider name (keycloak, auth0, okta, etc.).                                                                                                                                                 |
| `identityProviderId` | `String!`                                                           | The user's unique ID in the identity provider.                                                                                                                                                            |
| `email`              | [EmailAddress](../../../core-api-reference/common.md#emailaddress)! | The user's primary email address.                                                                                                                                                                         |
| `externalId`         | `String`                                                            | An external system identifier for integration purposes.                                                                                                                                                   |
| `isActive`           | `Boolean!`                                                          | Whether this user account is active.                                                                                                                                                                      |
| `memberships`        | [MemberConnection](../workspaces/members.md#memberconnection)!      | The workspace memberships for this user.                                                                                                                                                                  |

***

### UserCatalogItemPayload

The result of a user catalog item mutation.

| Field  | Type                                         | Description                               |
| ------ | -------------------------------------------- | ----------------------------------------- |
| `item` | [UserCatalogItem](users.md#usercatalogitem)! | The created or updated user catalog item. |

***

## Inputs

### UserCatalogItemCreateInput

Input for creating a user catalog item.

| Field         | Type                                                                      | Description                                                                                               |
| ------------- | ------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| `workspaceId` | `ID!`                                                                     | The workspace that will own the item.                                                                     |
| `catalogId`   | `ID!`                                                                     | The catalog to add the item to.                                                                           |
| `code`        | [Code](../../../core-api-reference/common.md#code)                        | The machine-readable code, unique within the catalog and workspace. Auto-generated from title if omitted. |
| `title`       | `String!`                                                                 | The display name.                                                                                         |
| `order`       | `Int`                                                                     | The display order. Auto-calculated as last position if omitted.                                           |
| `parentId`    | `ID`                                                                      | The parent item ID for hierarchical catalogs.                                                             |
| `meta`        | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties.                                                                                   |

***

### UserCatalogItemUpdateInput

Input for updating a user catalog item.

| Field      | Type                                                                      | Description                                                                                     |
| ---------- | ------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `id`       | `ID!`                                                                     | The item ID to update.                                                                          |
| `version`  | `Int`                                                                     | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title`    | `String`                                                                  | The new display name.                                                                           |
| `order`    | `Int`                                                                     | The new display order.                                                                          |
| `parentId` | `ID`                                                                      | The new parent ID for hierarchical items.                                                       |
| `meta`     | [CatalogItemMetaInput](../catalogs/catalog-items.md#catalogitemmetainput) | The display properties.                                                                         |

***

## Pagination types

### UserCatalogItemConnection

A paginated list of UserCatalogItem items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field      | Type                                                         | Description                                                |
| ---------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| `edges`    | \[[UserCatalogItemEdge](users.md#usercatalogitemedge)!]!     | A list of edges.                                           |
| `nodes`    | \[[UserCatalogItem](users.md#usercatalogitem)!]!             | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)!  | Information about the current page.                        |
| `total`    | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter.              |

***

### UserCatalogItemEdge

An edge in the UserCatalogItem connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field    | Type                                         | Description                                   |
| -------- | -------------------------------------------- | --------------------------------------------- |
| `cursor` | `String!`                                    | An opaque cursor for this edge.               |
| `node`   | [UserCatalogItem](users.md#usercatalogitem)! | The user catalog item at the end of the edge. |

***

### UserConnection

A paginated list of User items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field      | Type                                                         | Description                                                |
| ---------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| `edges`    | \[[UserEdge](users.md#useredge)!]!                           | A list of edges.                                           |
| `nodes`    | \[[User](users.md#user)!]!                                   | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)!  | Information about the current page.                        |
| `total`    | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter.              |

***

### UserEdge

An edge in the User connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field    | Type                   | Description                      |
| -------- | ---------------------- | -------------------------------- |
| `cursor` | `String!`              | An opaque cursor for this edge.  |
| `node`   | [User](users.md#user)! | The user at the end of the edge. |

***
