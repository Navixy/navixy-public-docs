---
description: >-
  The tables of the core_bdr database in IoT Query Premium: workspaces, users,
  devices, assets, asset groups, geo objects, schedules, catalogs, and custom
  fields.
tags:
  - reference
  - preview
---

# core_bdr database

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

`core_bdr` is the database of business data in IoT Query Premium: a copy of [Business Data Repository](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/bdr) for your workspace. It has the workspace, its users, devices, assets, asset groups, geo objects, schedules, catalogs, and custom fields that you manage in Location Intelligence. This page lists its tables by category, with the columns that queries use most, and ends with example queries. It is for developers and analysts who join business data to telemetry or to calculated tables. `core_bdr` belongs to the [raw data layer](./).

## How core_bdr stores rows

Navixy copies Business Data Repository into `core_bdr` automatically. The following rules apply to the tables:

* **Read with `FINAL`.** A table can contain more than one version of a row until ClickHouse merges the versions. `FROM core_bdr.asset FINAL` returns the current version only. In the main tables, the `version` column numbers the versions, and the highest `version` is the current one. See [Data schema](../#read-the-current-version-of-each-row) to learn why a row can have several versions.
* **Deleted items stay in the main tables.** The tables of workspaces, actors, members, devices, inventories, device inventory links, assets, asset groups, geo objects, schedules, custom field definitions, and catalog items (`ci_base`) have `deleted_at`. An item that you delete in Business Data Repository keeps its row in these tables. `deleted_at` has the time of deletion, and `deleted_by` has the ID of the actor, the person or integration, who deleted it. Filter with `deleted_at IS NULL` to read only the items that exist.
* **The other tables have no deletion marker.** In tables without `deleted_at`, such as `device_identifier`, `asset_group_item`, the `custom_field_value_*` tables, and the `ci_*` extension tables, a row that Business Data Repository removes disappears. `WHERE deleted_at IS NULL` on these tables returns an error, because the column doesn't exist.
* **Times are UTC.** `created_at`, `updated_at`, and `deleted_at` are the times in Business Data Repository, in the tables that have them. `record_added_at` is the time when the row reached IoT Query Premium.
* **IDs are `UUID`.** They are the same IDs as in Business Data Repository and in the Navixy GraphQL API.

## Tables by category

The tables belong to six categories:

| Category | Tables | What they contain |
| --- | --- | --- |
| [Organization and access](#organization-and-access) | `workspace`, `actor`, `user`, `member`, `integration` | Workspaces, the people and integrations that work in them, and their membership |
| [Devices](#devices) | `device`, `device_identifier`, `device_relation`, `inventory`, `device_inventory_relation` | GPS devices, their identifiers, links between devices, and inventories |
| [Assets and groups](#assets-and-groups) | `asset`, `asset_group`, `asset_group_item`, `asset_group_type_to_asset_type_relation` | The objects that you monitor, and the groups that they belong to |
| [Geo objects and schedules](#geo-objects-and-schedules) | `geo_object`, `schedule` | Zones, points of interest, routes, and schedules |
| [Catalogs](#catalogs) | `ci_base`, `ci_catalog`, the `ci_*` tables of each catalog, and `tag_to_entity_type` | Types, statuses, models, tags, and your own catalog items |
| [Custom fields](#custom-fields) | `custom_field_definition`, `custom_field_value_*`, `custom_field_ref_history`, `i18n_text` | The custom fields of each entity type and their values, including the link from an asset to its GPS devices |

### Organization and access

A workspace is the unit that your organization divides its data into. Every business data table that belongs to a workspace has `workspace_id`.

`core_bdr.workspace` has one row per workspace:

| Column | Type | What it contains |
| --- | --- | --- |
| `id` | `UUID` | The workspace ID |
| `title` | `String` | The name of the workspace |
| `organization_id` | `UUID` | The organization that the workspace belongs to |
| `parent_workspace_id` | `Nullable(UUID)` | The parent workspace, if the workspace is nested |
| `is_active` | `UInt8` | `1` if the workspace is active |

The other tables of this category describe who works in a workspace:

* `actor` is any party that can act in the Navixy platform: a person or an integration. `actor_type` is `USER` for a person, `INTEGRATION` for an integration, or `SYSTEM` for Navixy itself. Columns such as `deleted_by` and `assigned_by` in other tables contain an actor ID.
* `user` has the people: `given_names`, `family_names`, and `email`.
* `member` links a user to a workspace with `user_id` and `workspace_id`. `assigned_at` is the time when the user joined the workspace.
* `integration` has the integrations of a workspace, with `title` and `is_active`.

### Devices

`core_bdr.device` has one row per GPS device:

| Column | Type | What it contains |
| --- | --- | --- |
| `id` | `UUID` | The device ID, the same as `device_id` in `telemetry` and `report` |
| `workspace_id` | `UUID` | The workspace of the device |
| `title` | `String` | The name of the device |
| `device_type_id` | `UUID` | The device type, a catalog item |
| `model_id` | `Nullable(UUID)` | The device model, a catalog item |
| `status_id` | `UUID` | The device status, a catalog item |

The other tables of this category link a device to its identifiers, to other devices, and to its inventory:

* `device_identifier` has the identifiers of a device, such as its IMEI or serial number. `type` is the kind of identifier: `IMEI`, `SERIAL_NUMBER`, `MAC_ADDRESS`, `GUID`, `MEID_HEX`, `MEID_DEC`, or `CUSTOM`. `value` has the identifier itself. A device can have several identifiers.
* `device_relation` links one device to another device. `first_id` and `second_id` are the IDs of the two devices, and `relation_type_id` is the kind of link, a catalog item.

An asset isn't linked to its GPS devices in this category. The link is a custom field of the asset: see [Custom fields](#custom-fields). Telemetry rows have only the device ID, so to find the asset of a telemetry row, join `custom_field_value_device`.
* `inventory` has the inventories of a workspace. `device_inventory_relation` records which device is in which inventory, and `assigned_at` has the time when the device was added to the inventory.

### Assets and groups

An asset is an object that you monitor: a vehicle, a person, a container, or any other object with a GPS device. `core_bdr.asset` has one row per asset:

| Column | Type | What it contains |
| --- | --- | --- |
| `id` | `UUID` | The asset ID, the same as `asset_id` in `report` |
| `workspace_id` | `UUID` | The workspace of the asset |
| `asset_type_id` | `UUID` | The asset type, a catalog item |
| `title` | `String` | The name of the asset |
| `custom_fields_data` | `String` | The custom field values of the asset, as JSON |

Groups collect assets:

* `asset_group` has the groups of a workspace, with `title`, `color`, and `group_type_id`.
* `asset_group_item` records membership: `group_id`, `asset_id`, `attached_at`, and `detached_at`. A row whose `detached_at` is `NULL` is a current member. The other rows are past memberships, so you can find which group an asset belonged to on a past date.
* `asset_group_type_to_asset_type_relation` lists the asset types that a group type allows. `max_items` is the largest number of assets of that type in one group of that group type. `NULL` means no limit.

### Geo objects and schedules

A geo object is a zone, a point of interest, or a route. `core_bdr.geo_object` has `id`, `workspace_id`, `title`, `geo_object_type_id`, and `custom_fields_data`. The shape of a geo object is stored in its custom field `geojson_data`, as GeoJSON. A circle is a point in `geojson_data`, with its radius in meters in the custom field `cf_radius`.

`core_bdr.schedule` has the schedules of a workspace. `schedule_data` has the definition of the schedule as JSON.

The `geo_object_id` column of `agg.device_geofence_status_hist` contains the `id` of a geo object.

### Catalogs

A catalog is a list of values that other tables refer to. Catalogs include asset types, device types, device statuses, device models and vendors, tags, geo object types, and your own catalog items. Every catalog item has a row in `core_bdr.ci_base`:

| Column | Type | What it contains |
| --- | --- | --- |
| `id` | `UUID` | The catalog item ID |
| `code` | `String` | The code of the item, stable across languages |
| `title_en` | `String` | The English name of the item |
| `catalog_id` | `UUID` | The catalog that the item belongs to, a row of `ci_catalog` |
| `workspace_id` | `Nullable(UUID)` | The workspace of the item. `NULL` for items that Navixy provides |
| `parent_id` | `Nullable(UUID)` | The parent item, in a catalog with levels |
| `is_archived` | `UInt8` | `1` if the item is archived |

Each catalog also has its own table with the same `id`, such as `ci_asset_type`, `ci_device_status`, and `ci_device_model`. These tables add the columns that only one catalog has, such as `vendor_id` in `ci_device_model`. For the name of a type or a status, join `ci_base`. `tag_to_entity_type` lists the entity types that each tag applies to, with `tag_id` and `entity_type_id`.

Names in other languages are in `i18n_text`, which has the columns `entity_id`, `field_code`, `locale`, and `value`.

### Custom fields

A custom field is a field that your organization adds to an entity type, such as a license plate on vehicles. `core_bdr.custom_field_definition` has one row per custom field:

| Column | Type | What it contains |
| --- | --- | --- |
| `id` | `UUID` | The custom field ID |
| `code` | `String` | The code of the field |
| `title_en` | `String` | The English name of the field |
| `field_type` | `String` | The type of value that the field accepts |
| `target_entity_type_id` | `UUID` | The kind of entity that the field is on, such as asset or geo object, a catalog item |
| `owner_catalog_item_id` | `UUID` | The item that owns the field: the entity type for a field of every entity of that kind, or a specific type, such as an asset type, for a field of that type only |
| `is_required` | `UInt8` | `1` if every entity must have a value |
| `params` | `String` | The settings of the field, as JSON |

The values are in one table per value type: `custom_field_value_text`, `custom_field_value_decimal`, `custom_field_value_integer`, `custom_field_value_boolean`, `custom_field_value_date`, `custom_field_value_datetime`, `custom_field_value_geojson`, `custom_field_value_option`, `custom_field_value_device`, and `custom_field_value_ref`. Each row has `entity_id` (the asset or geo object), `field_definition_id`, and `value_index`. `value_index` numbers the values when a field has more than one value. A field that refers to another item has `ref_item_id`, `ref_device_id`, or `ref_id` instead of `value`.

The GPS devices of an asset are in `custom_field_value_device`: `entity_id` is the asset, `ref_device_id` is the device, and `is_primary` is `1` for the primary device of the asset.

`custom_field_ref_history` records when a reference was assigned and removed, with `assigned_at` and `unassigned_at`. Use it to find which device a field referred to on a past date.

The same values are also in the `custom_fields_data` column of each asset and geo object, as one JSON object.

## Example queries

### List assets with their types

The following query lists the assets that exist, with the English name of the asset type:

```sql
SELECT
    a.title AS asset,
    t.title_en AS asset_type
FROM
(
    SELECT id, title, asset_type_id
    FROM core_bdr.asset FINAL
    WHERE deleted_at IS NULL
) AS a
LEFT JOIN
(
    SELECT id, title_en
    FROM core_bdr.ci_base FINAL
) AS t ON t.id = a.asset_type_id
ORDER BY asset
```

The result has one row per asset:

| asset | asset_type |
| --- | --- |
| Truck 12 | Vehicle |
| Van 3 | Vehicle |
| Warehouse container 1 | Container |

### Find the device of each asset, with its IMEI

The following query joins each asset to its devices through `custom_field_value_device`. The query then joins each device to its IMEI:

```sql
SELECT
    a.title AS asset,
    d.title AS device,
    i.value AS imei
FROM
(
    SELECT id, title
    FROM core_bdr.asset FINAL
    WHERE deleted_at IS NULL
) AS a
INNER JOIN
(
    SELECT entity_id, ref_device_id
    FROM core_bdr.custom_field_value_device FINAL
) AS r ON r.entity_id = a.id
INNER JOIN
(
    SELECT id, title
    FROM core_bdr.device FINAL
    WHERE deleted_at IS NULL
) AS d ON d.id = r.ref_device_id
LEFT JOIN
(
    SELECT device_id, value
    FROM core_bdr.device_identifier FINAL
    WHERE type = 'IMEI'
) AS i ON i.device_id = d.id
ORDER BY asset
```

The result has one row per asset and device:

| asset | device | imei |
| --- | --- | --- |
| Truck 12 | FMB920 truck 12 | 356307042441013 |
| Van 3 | FMB920 van 3 | 356307042441021 |

### Find the current members of an asset group

The following query lists the assets that belong to the group "North region" today:

```sql
SELECT a.title AS asset
FROM
(
    SELECT id
    FROM core_bdr.asset_group FINAL
    WHERE title = 'North region' AND deleted_at IS NULL
) AS g
INNER JOIN
(
    SELECT group_id, asset_id
    FROM core_bdr.asset_group_item FINAL
    WHERE detached_at IS NULL
) AS gi ON gi.group_id = g.id
INNER JOIN
(
    SELECT id, title
    FROM core_bdr.asset FINAL
    WHERE deleted_at IS NULL
) AS a ON a.id = gi.asset_id
ORDER BY asset
```

The result has one row per asset in the group:

| asset |
| --- |
| Truck 12 |
| Truck 7 |

## See also

* [telemetry database](telemetry.md): Find the columns that contain positions and sensor values.
* [report database](../transformation-layer/report.md) and [agg database](../transformation-layer/agg.md): Find the calculated tables that you join to `core_bdr` for names.
* [Business Data Repository](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/bdr): Learn how assets, geo objects, and schedules work in Location Intelligence.
