---
description: >-
  How the Business Data Repository reference is organized: one page per
  category for workspaces, actors, devices, assets, geo objects, schedules,
  custom fields, audit, and catalogs.
---

# Business Data Repository API reference

{% include "../../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

The Business Data Repository API reference documents every operation and type of Business Data Repository, grouped into one page per category. Each category page covers its queries, mutations, input and object types, and enums, with a field table for every type.

The reference states what each operation and field is. For how to use them together, see the [Business Data Repository guides](../guides/README.md), and for the GraphQL fundamentals behind the notation, see [GraphQL basics](../../graphql-basics/), including the [type syntax](../../graphql-basics/#type-syntax) that the field tables use.

## Where the operations live

All Business Data Repository queries and mutations are nested under the `bdr` field of the `Query` and `Mutation` types, and every response has the same shape under `data.bdr`. Every query and mutation that lists or creates entities takes a `workspaceId` argument. For what the product covers and how to start, see the [product overview](../overview.md).

## Categories

<table><thead><tr><th width="186">Category</th><th>Covers</th></tr></thead><tbody><tr><td><a href="workspaces/">Workspaces</a></td><td><code>Workspace</code> (read-only: Navixy Console owns the lifecycle) and <a href="workspaces/members.md">members</a> (<code>Member</code>, member CRUD).</td></tr><tr><td><a href="actors/">Actors</a></td><td>The <code>Actor</code> interface, with <a href="actors/users.md">users</a> (<code>User</code>, memberships, user catalog items) and <a href="actors/integrations.md">integrations</a> (<code>Integration</code>, service accounts).</td></tr><tr><td><a href="devices/">Devices</a></td><td><code>Device</code>, <code>DeviceIdentifier</code>, <code>DeviceRelation</code>, the device type/model/vendor/status catalogs, and <a href="devices/inventory.md">inventory</a> (<code>Inventory</code>, assignment history).</td></tr><tr><td><a href="assets/">Assets</a></td><td><code>Asset</code> and <code>AssetType</code>, plus <a href="assets/groups.md">asset groups</a> (<code>AssetGroup</code>, <code>AssetGroupType</code>, and <code>AssetGroupItem</code> membership history).</td></tr><tr><td><a href="geo-objects.md">Geo objects</a></td><td><code>GeoObject</code> and <code>GeoObjectType</code> for geofences, points of interest, and routes, with GeoJSON geometry and the <code>containsPoints</code> check.</td></tr><tr><td><a href="schedules.md">Schedules</a></td><td><code>Schedule</code> and the <code>ScheduleData</code> calendar value, compatible with iCalendar.</td></tr><tr><td><a href="custom-fields.md">Custom fields</a></td><td><code>CustomFieldDefinition</code>, the <code>FieldType</code> catalog, the typed <code>CustomFieldValue</code> variants, and the patch inputs used in create and update mutations.</td></tr><tr><td><a href="audit.md">Audit</a></td><td><code>AuditEvent</code>, the <code>auditEvents</code> and <code>entityHistory</code> queries, and the event and source type enums.</td></tr><tr><td><a href="catalogs/">Catalogs</a></td><td><code>Catalog</code> and <a href="catalogs/catalog-items.md">catalog items</a> (<code>CatalogItem</code>, hierarchical <code>UserCatalogItem</code>), <a href="catalogs/tags.md">tags</a>, <a href="catalogs/system.md">system catalogs</a>, and the <a href="catalogs/catalog-reference.md">catalog reference</a> that says which catalog backs a given field.</td></tr></tbody></table>

## Shared types

The scalars, interfaces, and pagination types that Business Data Repository shares with the other products are on the [Common resources](../../core-api-reference/common.md) page. Filtering by custom field values is on [Custom field filtering and sorting](../../filtering-and-sorting/custom-field-filtering.md). The [Core API reference](../../core-api-reference/README.md) indexes the reference pages of every product.

## How the reference pages are structured

Every category page follows the same order: queries, then mutations, then the category's objects, inputs, and enums, with pagination types in a section of their own at the end. Field tables use the GraphQL type notation, where `!` marks a value that is never null and `[...]` marks a list: see [Type syntax](../../graphql-basics/#type-syntax).

## Other ways to explore the API

* [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox): Run queries and mutations interactively.
* [Public GraphQL schema](../../developer-resources/public-graphql-schema.md): The full type system split into several files.
* [Introspection](../../graphql-basics/#introspection): Explore the schema from your own GraphQL client.

## See also

* [Business Data Repository overview](../overview.md): What the product covers and how to start
* [Business Data Repository guides](../guides/README.md): Scenario walkthroughs built from these operations
* [Optimistic locking](../../optimistic-locking.md): The version check that every update and delete mutation makes
* [Error handling](../../error-handling.md): Understand error structure, codes, and common error scenarios
