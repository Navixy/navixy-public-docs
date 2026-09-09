---
description: >-
  How the Navixy GraphQL API reference is organized: the types shared across
  products, then one page per category within each product.
---

# Core API reference

The Core API reference documents every operation and type of Navixy GraphQL API, grouped into one page per category. Each category page covers its queries, mutations, input and object types, and enums, with a field table for every type.

The reference states what each operation and field is. For how to use them together, see the guides in each product section, and for the GraphQL fundamentals behind the notation, see [GraphQL basics](../graphql-basics/), including the [type syntax](../graphql-basics/#type-syntax) that the field tables use.

## Shared across products

These two pages belong to no single product. Every product of Navixy GraphQL API uses what they document.

| Page | Covers |
| --- | --- |
| [Common resources](common.md) | The `node`/`nodes` lookup queries, the shared scalars (`Code`, `DateTime`, `Long`, `Decimal`, `JSON`, `HexColorCode`), the interfaces (`Node`, `Titled`, `Versioned`, `Customizable`), and the pagination types (`PageInfo`, `CountInfo`). |
| [Directives](directives.md) | The standard GraphQL directives plus the custom `@trim`. |

## Business Data Repository

Entry point: `bdr`. For what the product covers and how to start, see the [product overview](../business-data-repository/overview.md).

| Category | Covers |
| --- | --- |
| [Workspaces](../business-data-repository/api-reference/workspaces/) | `Workspace` (read-only: Navixy Console owns the lifecycle) and [members](../business-data-repository/api-reference/workspaces/members.md) (`Member`, member CRUD). |
| [Actors](../business-data-repository/api-reference/actors/) | The `Actor` interface, with [users](../business-data-repository/api-reference/actors/users.md) (`User`, memberships, user catalog items) and [integrations](../business-data-repository/api-reference/actors/integrations.md) (`Integration`, service accounts). |
| [Devices](../business-data-repository/api-reference/devices/) | `Device`, `DeviceIdentifier`, `DeviceRelation`, the device type/model/vendor/status catalogs, and [inventory](../business-data-repository/api-reference/devices/inventory.md) (`Inventory`, assignment history). |
| [Assets](../business-data-repository/api-reference/assets/) | `Asset` and `AssetType`, plus [asset groups](../business-data-repository/api-reference/assets/groups.md) (`AssetGroup`, `AssetGroupType`, and `AssetGroupItem` membership history). |
| [Geo objects](../business-data-repository/api-reference/geo-objects.md) | `GeoObject` and `GeoObjectType` for geofences, points of interest, and routes, with GeoJSON geometry and the `containsPoints` check. |
| [Schedules](../business-data-repository/api-reference/schedules.md) | `Schedule` and the `ScheduleData` calendar value, compatible with iCalendar. |
| [Custom fields](../business-data-repository/api-reference/custom-fields.md) | `CustomFieldDefinition`, the `FieldType` catalog, the typed `CustomFieldValue` variants, and the patch inputs used in create and update mutations. |
| [Audit](../business-data-repository/api-reference/audit.md) | `AuditEvent`, the `auditEvents` and `entityHistory` queries, and the event and source type enums. |
| [Catalogs](../business-data-repository/api-reference/catalogs/) | `Catalog` and [catalog items](../business-data-repository/api-reference/catalogs/catalog-items.md) (`CatalogItem`, hierarchical `UserCatalogItem`), [tags](../business-data-repository/api-reference/catalogs/tags.md), and [system catalogs](../business-data-repository/api-reference/catalogs/system.md). |

## Other products

Tracking, IoT Logic, Alerts, and Reports are in preparation. Their reference pages appear here as each product becomes available.

## How the reference pages are structured

Every category page follows the same order: queries, then mutations, then the category's objects, inputs, and enums, with pagination types in a section of their own at the end. Field tables use the GraphQL type notation, where `!` marks a value that is never null and `[...]` marks a list: see [Type syntax](../graphql-basics/#type-syntax).

## Other ways to explore the API

* [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox): Run queries and mutations interactively.
* [Public GraphQL schema](https://raw.githubusercontent.com/Navixy/navixy-api/refs/heads/master/docs/navixy-graphql-api/graphql-schema/schema.graphql): The full type system in one file.
* [Introspection](../graphql-basics/#introspection): Explore the schema from your own GraphQL client.

## See also

* [GraphQL basics](../graphql-basics/): Learn GraphQL fundamentals, from queries and mutations to the type system
* [Limits](../limits.md): Request, pagination, and input size limits, and the errors they return
* [Error handling](../error-handling.md): Understand error structure, codes, and common error scenarios
