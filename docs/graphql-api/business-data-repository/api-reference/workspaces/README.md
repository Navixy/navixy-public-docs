---
description: >-
  Workspaces are isolated tenants that own all business data: members, devices,
  assets, geo objects, and configuration. The API acts in the workspace that
  your token names and exposes it read-only. Navixy Console creates workspaces.
tags:
  - reference
  - beta
---

# Workspaces

A workspace is an isolated tenant that owns all business data: its own members, devices, assets, geo objects, and configuration. Every request acts in the workspace that the caller's token names, and the `workspace` query returns it. Navixy Console creates workspaces, so they are read-only in Navixy GraphQL API.

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

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `organizationId` | `ID!` | The Console-owned organization this workspace belongs to, by the same id as `AccessibleOrganization.id`. One organization may own many workspaces. |
| `externalId` | `String` | An external system identifier for integration purposes. |
| `isActive` | `Boolean!` | Whether this workspace is active. |
| `members` | [MemberConnection](members.md#memberconnection)! | The members of this workspace. |
| `devices` | [DeviceConnection](../devices/README.md#deviceconnection)! | The devices owned by this workspace. |
| `assets` | [AssetConnection](../assets/README.md#assetconnection)! | The assets owned by this workspace. |
| `geoObjects` | [GeoObjectConnection](../geo-objects.md#geoobjectconnection)! | The geographic objects owned by this workspace. |
| `schedules` | [ScheduleConnection](../schedules.md#scheduleconnection)! | The schedules owned by this workspace. |

</details>

---

## Objects

<a id="workspace"></a>

### Workspace

A workspace that owns entities and users.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Versioned](../../../core-api-reference/common.md#versioned), [Titled](../../../core-api-reference/common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `organizationId` | `ID!` | The Console-owned organization this workspace belongs to, by the same id as `AccessibleOrganization.id`. One organization may own many workspaces. |
| `externalId` | `String` | An external system identifier for integration purposes. |
| `isActive` | `Boolean!` | Whether this workspace is active. |
| `members` | [MemberConnection](members.md#memberconnection)! | The members of this workspace. |
| `devices` | [DeviceConnection](../devices/README.md#deviceconnection)! | The devices owned by this workspace. |
| `assets` | [AssetConnection](../assets/README.md#assetconnection)! | The assets owned by this workspace. |
| `geoObjects` | [GeoObjectConnection](../geo-objects.md#geoobjectconnection)! | The geographic objects owned by this workspace. |
| `schedules` | [ScheduleConnection](../schedules.md#scheduleconnection)! | The schedules owned by this workspace. |

---

## See also

* [Pagination](../../../pagination.md)
* [Filtering and sorting](../../../filtering-and-sorting/README.md)
* [Error handling](../../../error-handling.md)
* [Optimistic locking](../../../optimistic-locking.md)
* [Limits](../../../limits.md)
