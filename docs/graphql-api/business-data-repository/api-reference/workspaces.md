---
description: >-
  Workspaces are isolated tenants that own all business data: devices, assets,
  geo objects, and configuration. The API acts in the workspace that your token
  names and exposes it read-only. Navixy Console creates workspaces.
tags:
  - reference
  - beta
---

# Workspaces

A workspace is an isolated tenant that owns all business data: its own devices, assets, geo objects, and configuration. Every request acts in the workspace that the caller's token names, and the `workspace` query returns it. Navixy Console creates workspaces, so they are read-only in Navixy GraphQL API.

## Queries

### workspace (query)

The caller's workspace — the one their token names.

```graphql
workspace: Workspace!
```

**Output types:**

<details>

<summary>Workspace</summary>

A workspace that owns entities and users.

**Implements:** [Node](../../core-api-reference/common.md#node), [Versioned](../../core-api-reference/common.md#versioned), [Titled](../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `organizationId` | `ID!` | The Console-owned organization this workspace belongs to, by the same id as `AccessibleOrganization.id`. One organization may own many workspaces. |
| `externalId` | `String` | An external system identifier for integration purposes. |
| `isActive` | `Boolean!` | Whether this workspace is active. |
| `devices` | [DeviceConnection](devices/README.md#deviceconnection)! | The devices owned by this workspace. |
| `assets` | [AssetConnection](assets/README.md#assetconnection)! | The assets owned by this workspace. |
| `geoObjects` | [GeoObjectConnection](geo-objects.md#geoobjectconnection)! | The geographic objects owned by this workspace. |
| `schedules` | [ScheduleConnection](schedules.md#scheduleconnection)! | The schedules owned by this workspace. |

</details>

---

## Objects

<a id="workspace"></a>

### Workspace

A workspace that owns entities and users.

**Implements:** [Node](../../core-api-reference/common.md#node), [Versioned](../../core-api-reference/common.md#versioned), [Titled](../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `organizationId` | `ID!` | The Console-owned organization this workspace belongs to, by the same id as `AccessibleOrganization.id`. One organization may own many workspaces. |
| `externalId` | `String` | An external system identifier for integration purposes. |
| `isActive` | `Boolean!` | Whether this workspace is active. |
| `devices` | [DeviceConnection](devices/README.md#deviceconnection)! | The devices owned by this workspace. |
| `assets` | [AssetConnection](assets/README.md#assetconnection)! | The assets owned by this workspace. |
| `geoObjects` | [GeoObjectConnection](geo-objects.md#geoobjectconnection)! | The geographic objects owned by this workspace. |
| `schedules` | [ScheduleConnection](schedules.md#scheduleconnection)! | The schedules owned by this workspace. |

**Arguments of `devices`**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | [DeviceFilter](devices/README.md#devicefilter) | Filtering options for the returned devices. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | [DeviceOrder](devices/README.md#deviceorder) | The ordering options for the returned devices. Default: `{ field: TITLE, direction: ASC }`. |

**Arguments of `assets`**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | [AssetFilter](assets/README.md#assetfilter) | Filtering options for the returned assets. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | [AssetOrder](assets/README.md#assetorder) | The ordering options for the returned assets. Default: `{ field: TITLE, direction: ASC }`. |

**Arguments of `geoObjects`**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | [GeoObjectFilter](geo-objects.md#geoobjectfilter) | Filtering options for the returned geo objects. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | [GeoObjectOrder](geo-objects.md#geoobjectorder) | The ordering options for the returned geo objects. Default: `{ field: TITLE, direction: ASC }`. |

**Arguments of `schedules`**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | [ScheduleFilter](schedules.md#schedulefilter) | Filtering options for the returned schedules. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). |
| `orderBy` | [ScheduleOrder](schedules.md#scheduleorder) | The ordering options for the returned schedules. Default: `{ field: TITLE, direction: ASC }`. |

---

## See also

* [Pagination](../../pagination.md)
* [Filtering and sorting](../../filtering-and-sorting/README.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
