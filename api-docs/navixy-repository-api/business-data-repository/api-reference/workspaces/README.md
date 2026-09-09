---
description: >-
  Workspaces are isolated tenants that own all business data: members, devices,
  assets, geo objects, and configuration. Navixy Console provisions and owns
  them, so this API exposes workspaces read-only.
---

# Workspaces

{% include "../../../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

A workspace is an isolated tenant that owns all business data: its own members, devices, assets, geo objects, and configuration. Workspaces are provisioned and owned by Navixy Console, so they are read-only in Navixy GraphQL API.

## Queries

### workspace (query)

Retrieves a workspace by its ID.

```graphql
workspace(id: ID!): Workspace
```

**Arguments**

| Name | Type  | Description                          |
| ---- | ----- | ------------------------------------ |
| `id` | `ID!` | The ID of the workspace to retrieve. |

**Output types:**

<details>

<summary>Workspace</summary>

A workspace that owns entities and users.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field            | Type                                                          | Description                                                                                                                                                                                               |
| ---------------- | ------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`             | `ID!`                                                         | A globally unique identifier. This ID is opaque and should not be parsed by clients.                                                                                                                      |
| `version`        | `Int!`                                                        | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title`          | `String!`                                                     | The human-readable display name.                                                                                                                                                                          |
| `organizationId` | [UUID](../../../core-api-reference/common.md#uuid)!           | The Console-owned organization this workspace belongs to. Opaque here: Console owns the ID scheme, and one organization may own many workspaces.                                                          |
| `externalId`     | `String`                                                      | An external system identifier for integration purposes.                                                                                                                                                   |
| `isActive`       | `Boolean!`                                                    | Whether this workspace is active.                                                                                                                                                                         |
| `members`        | [MemberConnection](members.md#memberconnection)!              | The members of this workspace.                                                                                                                                                                            |
| `devices`        | [DeviceConnection](../devices/#deviceconnection)!             | The devices owned by this workspace.                                                                                                                                                                      |
| `assets`         | [AssetConnection](../assets/#assetconnection)!                | The assets owned by this workspace.                                                                                                                                                                       |
| `geoObjects`     | [GeoObjectConnection](../geo-objects.md#geoobjectconnection)! | The geographic objects owned by this workspace.                                                                                                                                                           |
| `schedules`      | [ScheduleConnection](../schedules.md#scheduleconnection)!     | The schedules owned by this workspace.                                                                                                                                                                    |

</details>

***

### workspaces

Lists workspaces.

```graphql
workspaces(
    filter: WorkspaceFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: WorkspaceOrder = { field: TITLE, direction: ASC }
  ): WorkspaceConnection!
```

**Arguments**

| Name      | Type              | Description                                                                                                     |
| --------- | ----------------- | --------------------------------------------------------------------------------------------------------------- |
| `filter`  | `WorkspaceFilter` | Filtering options for the returned workspaces.                                                                  |
| `first`   | `Int`             | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination).     |
| `after`   | `String`          | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination).  |
| `last`    | `Int`             | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination).      |
| `before`  | `String`          | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `WorkspaceOrder`  | The ordering options for the returned workspaces.                                                               |

**Input types:**

<details>

<summary>WorkspaceFilter</summary>

Filtering options for workspaces.

| Field      | Type      | Description              |
| ---------- | --------- | ------------------------ |
| `isActive` | `Boolean` | Filter by active status. |

</details>

<details>

<summary>WorkspaceOrder</summary>

Ordering options for workspaces.

| Field       | Type                                                                    | Description             |
| ----------- | ----------------------------------------------------------------------- | ----------------------- |
| `field`     | [WorkspaceOrderField](./#workspaceorderfield)!                          | The field to order by.  |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>WorkspaceConnection</summary>

A paginated list of Workspace items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field      | Type                                                         | Description                                                |
| ---------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| `edges`    | \[[WorkspaceEdge](./#workspaceedge)!]!                       | A list of edges.                                           |
| `nodes`    | \[[Workspace](./#workspace)!]!                               | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)!  | Information about the current page.                        |
| `total`    | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter.              |

</details>

***

## Objects

### Workspace

A workspace that owns entities and users.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field            | Type                                                          | Description                                                                                                                                                                                               |
| ---------------- | ------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`             | `ID!`                                                         | A globally unique identifier. This ID is opaque and should not be parsed by clients.                                                                                                                      |
| `version`        | `Int!`                                                        | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title`          | `String!`                                                     | The human-readable display name.                                                                                                                                                                          |
| `organizationId` | [UUID](../../../core-api-reference/common.md#uuid)!           | The Console-owned organization this workspace belongs to. Opaque here: Console owns the ID scheme, and one organization may own many workspaces.                                                          |
| `externalId`     | `String`                                                      | An external system identifier for integration purposes.                                                                                                                                                   |
| `isActive`       | `Boolean!`                                                    | Whether this workspace is active.                                                                                                                                                                         |
| `members`        | [MemberConnection](members.md#memberconnection)!              | The members of this workspace.                                                                                                                                                                            |
| `devices`        | [DeviceConnection](../devices/#deviceconnection)!             | The devices owned by this workspace.                                                                                                                                                                      |
| `assets`         | [AssetConnection](../assets/#assetconnection)!                | The assets owned by this workspace.                                                                                                                                                                       |
| `geoObjects`     | [GeoObjectConnection](../geo-objects.md#geoobjectconnection)! | The geographic objects owned by this workspace.                                                                                                                                                           |
| `schedules`      | [ScheduleConnection](../schedules.md#scheduleconnection)!     | The schedules owned by this workspace.                                                                                                                                                                    |

***

## Inputs

### WorkspaceFilter

Filtering options for workspaces.

| Field      | Type      | Description              |
| ---------- | --------- | ------------------------ |
| `isActive` | `Boolean` | Filter by active status. |

***

### WorkspaceOrder

Ordering options for workspaces.

| Field       | Type                                                                    | Description             |
| ----------- | ----------------------------------------------------------------------- | ----------------------- |
| `field`     | [WorkspaceOrderField](./#workspaceorderfield)!                          | The field to order by.  |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

***

## Enums

### WorkspaceOrderField

Fields available for ordering workspaces.

| Value   | Description     |
| ------- | --------------- |
| `TITLE` | Order by title. |

***

## Pagination types

### WorkspaceConnection

A paginated list of Workspace items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field      | Type                                                         | Description                                                |
| ---------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| `edges`    | \[[WorkspaceEdge](./#workspaceedge)!]!                       | A list of edges.                                           |
| `nodes`    | \[[Workspace](./#workspace)!]!                               | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)!  | Information about the current page.                        |
| `total`    | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter.              |

***

### WorkspaceEdge

An edge in the Workspace connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field    | Type                       | Description                           |
| -------- | -------------------------- | ------------------------------------- |
| `cursor` | `String!`                  | An opaque cursor for this edge.       |
| `node`   | [Workspace](./#workspace)! | The workspace at the end of the edge. |

***
