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

<table><thead><tr><th width="212">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="common.md">Common resources</a></td><td>The <code>node</code>/<code>nodes</code> lookup queries, the shared scalars (<code>Code</code>, <code>DateTime</code>, <code>Long</code>, <code>Decimal</code>, <code>JSON</code>, <code>HexColorCode</code>), the interfaces (<code>Node</code>, <code>Titled</code>, <code>Versioned</code>, <code>Customizable</code>), and the pagination types (<code>PageInfo</code>, <code>CountInfo</code>).</td></tr><tr><td><a href="directives.md">Directives</a></td><td>The standard GraphQL directives plus the custom <code>@trim</code>.</td></tr></tbody></table>

## Business Data Repository

Entry point: `bdr`. For what the product covers and how to start, see the [product overview](../business-data-repository/overview.md).

<table><thead><tr><th width="186">Category</th><th>Covers</th></tr></thead><tbody><tr><td><a href="../business-data-repository/api-reference/workspaces/">Workspaces</a></td><td><code>Workspace</code> (read-only: Navixy Console owns the lifecycle) and <a href="../business-data-repository/api-reference/workspaces/members.md">members</a> (<code>Member</code>, member CRUD).</td></tr><tr><td><a href="../business-data-repository/api-reference/actors/">Actors</a></td><td>The <code>Actor</code> interface, with <a href="../business-data-repository/api-reference/actors/users.md">users</a> (<code>User</code>, memberships, user catalog items) and <a href="../business-data-repository/api-reference/actors/integrations.md">integrations</a> (<code>Integration</code>, service accounts).</td></tr><tr><td><a href="../business-data-repository/api-reference/devices/">Devices</a></td><td><code>Device</code>, <code>DeviceIdentifier</code>, <code>DeviceRelation</code>, the device type/model/vendor/status catalogs, and <a href="../business-data-repository/api-reference/devices/inventory.md">inventory</a> (<code>Inventory</code>, assignment history).</td></tr><tr><td><a href="../business-data-repository/api-reference/assets/">Assets</a></td><td><code>Asset</code> and <code>AssetType</code>, plus <a href="../business-data-repository/api-reference/assets/groups.md">asset groups</a> (<code>AssetGroup</code>, <code>AssetGroupType</code>, and <code>AssetGroupItem</code> membership history).</td></tr><tr><td><a href="../business-data-repository/api-reference/geo-objects.md">Geo objects</a></td><td><code>GeoObject</code> and <code>GeoObjectType</code> for geofences, points of interest, and routes, with GeoJSON geometry and the <code>containsPoints</code> check.</td></tr><tr><td><a href="../business-data-repository/api-reference/schedules.md">Schedules</a></td><td><code>Schedule</code> and the <code>ScheduleData</code> calendar value, compatible with iCalendar.</td></tr><tr><td><a href="../business-data-repository/api-reference/custom-fields.md">Custom fields</a></td><td><code>CustomFieldDefinition</code>, the <code>FieldType</code> catalog, the typed <code>CustomFieldValue</code> variants, and the patch inputs used in create and update mutations.</td></tr><tr><td><a href="../business-data-repository/api-reference/audit.md">Audit</a></td><td><code>AuditEvent</code>, the <code>auditEvents</code> and <code>entityHistory</code> queries, and the event and source type enums.</td></tr><tr><td><a href="../business-data-repository/api-reference/catalogs/">Catalogs</a></td><td><code>Catalog</code> and <a href="../business-data-repository/api-reference/catalogs/catalog-items.md">catalog items</a> (<code>CatalogItem</code>, hierarchical <code>UserCatalogItem</code>), <a href="../business-data-repository/api-reference/catalogs/tags.md">tags</a>, and <a href="../business-data-repository/api-reference/catalogs/system.md">system catalogs</a>.</td></tr></tbody></table>

## Other products

Tracking, IoT Logic, Alerts, and Reports are in preparation. Their reference pages will appear here as each product becomes available.

## How the reference pages are structured

Every category page follows the same order: queries, then mutations, then the category's objects, inputs, and enums, with pagination types in a section of their own at the end. Field tables use the GraphQL type notation, where `!` marks a value that is never null and `[...]` marks a list: see [Type syntax](../graphql-basics/#type-syntax).

## Other ways to explore the API

* [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox): Run queries and mutations interactively.
* [Public GraphQL schema](../developer-resources/public-graphql-schema.md): The full type system split into several files.
* [Introspection](../graphql-basics/#introspection): Explore the schema from your own GraphQL client.

## See also

* [GraphQL basics](../graphql-basics/): Learn GraphQL fundamentals, from queries and mutations to the type system
* [Limits](../limits.md): Request, pagination, and input size limits, and the errors they return
* [Error handling](../error-handling.md): Understand error structure, codes, and common error scenarios
