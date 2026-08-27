---
description: >-
  Predefined system catalog items available to all workspaces. These read-only
  reference types cannot be modified or deleted.
---

# System catalogs

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

System-defined catalog items that cannot be modified by users.

## Objects

<a id="module"></a>

### Module

A system module that groups related functionality and permission scopes.
Examples: repo (core), fleet_management (FSM), iot (devices), reports, billing.

**Implements:** [CatalogItem](catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |

---

<a id="entitytype"></a>

### EntityType

A definition of an entity type in the system.

**Implements:** [CatalogItem](catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `uuidDiscriminator` | `String!` | The 4-character code embedded in UUIDs for entities of this type. |
| `isCustomizable` | `Boolean!` | Whether entities of this type support custom fields. |
| `customFieldDefinitions` | [[CustomFieldDefinition](../custom-fields.md#customfielddefinition)!]! | Custom field definitions for entities of this type, ordered by display order. |

---

<a id="country"></a>

### Country

A country reference data item.

**Implements:** [CatalogItem](catalog-items.md#catalogitem), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](../common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](../workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `alpha2Code` | [CountryCode](../common.md#countrycode)! | The [ISO 3166](https://www.iso.org/standard/3166.html)-1 alpha-2 country code. |

---
