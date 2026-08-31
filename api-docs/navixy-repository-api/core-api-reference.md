---
description: >-
  How the Navixy Repository API reference is organized: one page per category,
  each covering the category's queries, mutations, and types.
---

# Core API reference

The Core API reference documents every operation and type of Navixy Repository API grouped into one page per category. Each category page covers its queries, mutations, input and object types, and enums, with a field table for every type.

The reference states what each operation and field is. For how to use them together, see the [guides](guides/README.md), and for the GraphQL fundamentals behind the notation, see [GraphQL basics](graphql-basics/README.md), including the [type syntax](graphql-basics/README.md#type-syntax) that the field tables use.

## Categories

| Category | Covers |
| --- | --- |
| [Common resources](common.md) | The `node`/`nodes` lookup queries, shared scalars (`Code`, `DateTime`, `JSON`, `HexColorCode`), interfaces (`Node`, `Titled`, `Versioned`, `Customizable`), and shared types (`PageInfo`, `CountInfo`, `Money`). |
| [Directives](directives.md) | The standard GraphQL directives plus the custom `@trim`. |
| [Workspaces](workspaces/README.md) | `Workspace` (read-only: Navixy Console owns the lifecycle) and [members](workspaces/members.md) (`Member`, member CRUD). |
| [Actors](actors/README.md) | The `Actor` interface and the `me` query, with [users](actors/users.md) (`User`, memberships, user catalog items) and [integrations](actors/integrations.md) (`Integration`, service accounts). |
| [Devices](devices/README.md) | `Device`, `DeviceIdentifier`, `DeviceRelation`, the device type/model/vendor/status catalogs, and [inventory](devices/inventory.md) (`Inventory`, assignment history). |
| [Assets](assets/README.md) | `Asset` and `AssetType`, plus [asset groups](assets/groups.md) (`AssetGroup`, `AssetGroupType`, and `AssetGroupItem` membership history). |
| [Geo objects](geo-objects.md) | `GeoObject` and `GeoObjectType` for geofences, points of interest, and routes, with GeoJSON geometry and the `containsPoints` check. |
| [Schedules](schedules.md) | `Schedule` and the `ScheduleData` calendar value, compatible with iCalendar. |
| [Access control](access-control.md) | `Role`, `PermissionScope`, role assignments (`ActorRole`), role permissions (`RolePermission`), and user scopes. |
| [Custom fields](custom-fields.md) | `CustomFieldDefinition`, the `FieldType` catalog, the typed `CustomFieldValue` variants, and the patch inputs used in create and update mutations. |
| [Audit](audit.md) | `AuditEvent`, the `auditEvents` and `entityHistory` queries, and the event and source type enums. |
| [Catalogs](catalogs/README.md) | `Catalog` and [catalog items](catalogs/catalog-items.md) (`CatalogItem`, hierarchical `UserCatalogItem`), [tags](catalogs/tags.md), and [system catalogs](catalogs/system.md). |

## How the reference pages are structured

Every category page follows the same order: queries, then mutations, then the category's objects, inputs, and enums, with pagination types in a section of their own at the end. Field tables use the GraphQL type notation, where `!` marks a value that is never null and `[...]` marks a list: see [Type syntax](graphql-basics/README.md#type-syntax).

## Other ways to explore the API

- [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox): Run queries and mutations interactively.
- [Public GraphQL schema](https://raw.githubusercontent.com/Navixy/navixy-api/refs/heads/master/docs/navixy-repository-api/graphql-schema/schema.graphql): The full type system in one file.
- [Introspection](graphql-basics/README.md#introspection): Explore the schema from your own GraphQL client.

## See also

- [GraphQL basics](graphql-basics/README.md): Learn GraphQL fundamentals, from queries and mutations to the type system
- [Limits](limits.md): Request, pagination, and input size limits, and the errors they return
- [Error handling](error-handling.md): Understand error structure, codes, and common error scenarios
