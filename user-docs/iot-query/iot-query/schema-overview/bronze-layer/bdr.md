---
description: >-
  BDR organizes devices and business entities into assets, with custom fields, relationships, and audit trails. Browse the schema definition and key table descriptions
---

# BDR schema

{% hint style="warning" %}
**This schema is currently in development.** BDR (Business Data Repository) organizes data around assets: a unified combination of a device and a business entity, such as a vehicle and its driver, or a device and its sensors. IoT Query reads from BDR to expose this data for analytics. If you're interested in early access or have questions about this functionality, please contact [iotquery@navixy.com](mailto:iotquery@navixy.com).
{% endhint %}

BDR provides a comprehensive framework for managing organizational structures, actors, devices, assets, and their relationships in multi-tenant environments. Built on PostgreSQL 18 with the `ltree` and `pg_trgm` extensions, the schema supports hierarchical organizations, custom field definitions for any entity type, actor-based role access control with object-level restrictions, optimistic locking for concurrent edits, and complete audit trails with field-level change tracking. All entities can be extended without schema modifications, localized for international deployments, and linked through flexible polymorphic relationships.

The schema addresses complex data management scenarios including fleet asset hierarchies across organizational levels, multi-tenant SaaS platforms requiring data isolation, compliance-driven operations with detailed audit requirements, and systems needing dynamic data models adaptable through custom fields rather than database migrations.

{% hint style="info" %}
The interactive diagram of the BDR data schema is available on **dbdiagram.io**: [https://dbdiagram.io/d/Navixy-Repo-data-schema-68ad788c1e7a611967a0930e](https://dbdiagram.io/d/Navixy-Repo-data-schema-68ad788c1e7a611967a0930e)
{% endhint %}

Find the BDR schema details below.

{% code title="bdr data schema" expandable="true" %}
```sql
// ============================================
// BDR (Business Data Repository) Schema - Customer Journey
// PostgreSQL 18 with ltree, pg_trgm extensions
// Version: 2.2
// ============================================

// ============================================
// BASE REFERENCE TABLES (ci_base hierarchy - Single Table Inheritance)
// ============================================

Table ci_base {
  id uuid [primary key, note: '4-char entity type code embedded in UUID bytes 5-8, extracted via uuid_type_code()']
  code text [not null, note: 'Machine-readable code, must start with a letter']
  title_en text [not null, note: 'Case-insensitive collation (ICU)']
  description_en text
  order int [not null, default: 0]
  is_system boolean [not null, default: false]
  discriminator text [not null, note: 'Auto-populated from the id via trg_ci_set_discriminator']
  catalog_id uuid [note: 'Parent catalog for user catalog items. NULL for system catalogs and catalog definitions themselves']
  organization_id uuid
  parent_id uuid
  path ltree [note: 'Auto-maintained by trigger when is_hierarchical = true']
  is_hierarchical boolean [not null, default: false]
  extra jsonb [not null, default: `{}`]
  version int [not null, default: 1, note: 'Optimistic locking']
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  updated_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  deleted_at timestamptz

  indexes {
    (id) [name: 'idx_ci_active', note: 'partial: WHERE deleted_at IS NULL']
    (parent_id) [name: 'idx_ci_parent']
    (path) [type: gist, name: 'idx_ci_path_gist']
    (catalog_id) [name: 'idx_ci_catalog']
    (organization_id) [name: 'idx_ci_org']
    (discriminator) [name: 'idx_ci_discriminator']
    (title_en) [name: 'idx_ci_title_en']
    (discriminator, code) [unique, name: 'uq_ci_code_discriminator']
    (catalog_id, code) [unique, name: 'uq_ci_code_catalog']
  }
}

Table ci_module {
  id uuid [primary key]
}

Table ci_catalog_category {
  id uuid [primary key]
}

Table ci_country {
  id uuid [primary key]
}

Table ci_role {
  id uuid [primary key]
}

Table ci_entity_type {
  id uuid [primary key]
  uuid_discriminator char(4) [not null, note: '4-char code embedded in entity UUIDs']
  is_customizable boolean [not null, default: true, note: 'Whether entities of this type support custom fields']
}

Table ci_device_status {
  id uuid [primary key]
}

Table ci_permission_scope {
  id uuid [primary key]
  module_id uuid [not null]
  entity_type_id uuid [not null]
  category text [not null]
}

Table ci_device_vendor {
  id uuid [primary key, note: 'Device manufacturers/vendors']
}

Table ci_device_model {
  id uuid [primary key]
  vendor_id uuid [not null]
}

Table ci_device_type {
  id uuid [primary key]
}

Table ci_asset_type {
  id uuid [primary key, note: 'Hierarchical via ci_base parent_id/path']
}

Table ci_geo_object_type {
  id uuid [primary key]
}

Table ci_schedule_type {
  id uuid [primary key]
}

Table ci_asset_group_type {
  id uuid [primary key]
}

Table asset_group_type_to_asset_type_relation {
  id uuid [primary key]
  group_type_id uuid [not null]
  asset_type_id uuid [not null]
  max_items int [note: 'Max assets of this type in group. NULL = unlimited']

  indexes {
    (group_type_id) [name: 'idx_agt_at_rel_group']
    (asset_type_id) [name: 'idx_agt_at_rel_asset']
    (group_type_id, asset_type_id) [unique]
  }
}

Table ci_device_relation_type {
  id uuid [primary key]
}

Table ci_tag {
  id uuid [primary key]
  entity_type_id uuid [note: 'Applicable entity type. NULL = universal tag']
}

Table ci_user_catalog_item {
  id uuid [primary key, note: 'Elements of user-defined catalogs']
}

Table ci_catalog {
  id uuid [primary key, note: 'Catalog definitions. Catalogs are themselves catalog items']
  organization_id uuid [not null]
  module_id uuid [not null]
  category_id uuid
  fields_schema jsonb [note: 'JSON Schema for item.extra validation and UI']

  indexes {
    (organization_id) [name: 'idx_ci_catalog_org']
    (module_id) [name: 'idx_ci_catalog_module']
  }
}

Table ci_custom_field_definition {
  id uuid [primary key]
  owner_catalog_item_id uuid [not null, note: 'Owner: entity_type (system) or a specific type such as asset_type']
  target_entity_type_id uuid [not null]
  field_type text [not null, note: 'STRING, TEXT, NUMBER, BOOLEAN, DATE, DATETIME, GEOJSON, SCHEDULE, OPTIONS, DEVICE, REFERENCE, CATALOG, TAG']
  is_required boolean [not null, default: false]
  params jsonb [note: 'Type-specific parameters (min/max, options, ref, etc.)']

  indexes {
    (owner_catalog_item_id) [name: 'idx_ci_cfd_owner']
    (target_entity_type_id) [name: 'idx_ci_cfd_target']
  }
}

// ============================================
// CORE ENTITIES
// ============================================

Table organization {
  id uuid [primary key]
  parent_id uuid
  path ltree
  code text [not null]
  title_en text [not null]
  external_id text [note: 'External system identifier for integrations']
  is_active boolean [not null, default: true]
  is_dealer boolean [not null, default: false, note: 'Whether org can create child organizations']
  version int [not null, default: 1]
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  updated_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  deleted_at timestamptz
  deleted_by uuid

  indexes {
    (parent_id) [name: 'idx_org_parent']
    (path) [type: gist, name: 'idx_org_path_gist']
    (code) [unique, name: 'uq_org_code']
    (external_id) [name: 'idx_org_external_id']
    (title_en) [name: 'idx_org_title', note: 'Source DDL targets a non-existent title column, corrected here to title_en']
  }
}

Table actor {
  id uuid [primary key, note: 'Abstract entity for users and integrations. Base for ACL']
  actor_type text [not null, note: 'USER, INTEGRATION, or SYSTEM']
  version int [not null, default: 1]
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  updated_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  deleted_at timestamptz
  deleted_by uuid

  indexes {
    (actor_type) [name: 'idx_actor_type']
  }
}

Table user {
  id uuid [primary key]
  identity_provider text [not null, default: 'keycloak']
  identity_provider_id uuid [not null]
  full_name text [not null]
  external_id text
  is_active boolean [not null, default: true]
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  updated_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  deleted_at timestamptz
  deleted_by uuid

  indexes {
    (identity_provider, identity_provider_id) [unique, name: 'uq_user_idp']
    (external_id) [name: 'idx_user_external_id']
  }
}

Table member {
  id uuid [primary key, note: 'Membership of a user in an organization']
  user_id uuid [not null]
  organization_id uuid [not null]
  is_active boolean [not null, default: true]
  custom_fields_data jsonb [not null, default: `{}`, note: 'Member custom fields (position, department, etc.)']
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  assigned_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  deleted_at timestamptz
  deleted_by uuid

  indexes {
    (user_id) [name: 'idx_member_user']
    (organization_id) [name: 'idx_member_org']
    (user_id, organization_id) [unique, name: 'uq_member_user_org']
  }
}

Table integration {
  id uuid [primary key, note: 'External system integration actor']
  name text [not null]
  credential_ref text [note: 'Reference to credentials in a secure vault']
  is_active boolean [not null, default: true]
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  updated_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  deleted_at timestamptz
  deleted_by uuid
}

// ============================================
// ACCESS CONTROL (ACL)
// ============================================

Table actor_role {
  id uuid [primary key]
  actor_id uuid [not null]
  role_id uuid [not null]
  assigned_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  assigned_by uuid
  expire_date timestamptz [note: 'NULL = permanent']

  indexes {
    (actor_id) [name: 'idx_actor_role_actor']
    (role_id) [name: 'idx_actor_role_role']
    (actor_id, role_id) [unique]
  }
}

Table acl_role_permission {
  id uuid [primary key]
  role_id uuid [not null]
  permission_scope_id uuid [not null]
  target_entity_id uuid [note: 'Specific entity. NULL = all entities of type. Polymorphic, no FK']
  actions int [not null, note: 'Bitmask: READ=1, CREATE=2, UPDATE=4, DELETE=8']
  granted_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  granted_by uuid

  indexes {
    (role_id) [name: 'idx_acl_role_perm_role']
    (permission_scope_id) [name: 'idx_acl_role_perm_scope']
    (role_id, permission_scope_id, target_entity_id) [unique]
  }
}

Table acl_user_scope {
  id uuid [primary key, note: 'Whitelist filter. Empty = full role access. Filled = intersection with roles']
  actor_id uuid [not null]
  permission_scope_id uuid [not null]
  target_entity_id uuid [not null]
  actions int [not null]

  indexes {
    (actor_id, permission_scope_id) [name: 'idx_acl_user_scope_actor']
    (actor_id, permission_scope_id, target_entity_id) [unique]
  }
}

// ============================================
// BUSINESS ENTITIES
// ============================================

Table device {
  id uuid [primary key]
  organization_id uuid [not null]
  device_type_id uuid [not null]
  model_id uuid [note: 'Optional']
  status_id uuid [not null]
  title text [not null]
  custom_fields_data jsonb [not null, default: `{}`]
  version int [not null, default: 1]
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  updated_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  deleted_at timestamptz
  deleted_by uuid

  indexes {
    (organization_id) [name: 'idx_device_org']
    (device_type_id) [name: 'idx_device_type']
    (model_id) [name: 'idx_device_model']
    (status_id) [name: 'idx_device_status']
    (title) [type: gin, name: 'idx_device_title_trgm', note: 'gin_trgm_ops for fuzzy search']
  }
}

Table device_identifier {
  id uuid [primary key]
  device_id uuid [not null]
  type text [not null, note: 'UUID, IMEI, MEID_HEX, MEID_DEC, MAC_ADDRESS, SERIAL_NUMBER, CUSTOM']
  value text [not null]
  namespace text [note: 'Uniqueness scope. NULL = global']
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  updated_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]

  indexes {
    (device_id) [name: 'idx_device_identifier_device']
    (value) [name: 'idx_device_identifier_value']
    (type, value) [unique, name: 'uq_device_identifier_global', note: 'partial: WHERE namespace IS NULL']
    (type, value, namespace) [unique, name: 'uq_device_identifier_namespaced', note: 'partial: WHERE namespace IS NOT NULL']
  }
}

Table device_relation {
  id uuid [primary key]
  first_id uuid [not null]
  second_id uuid [not null]
  relation_type_id uuid [not null, note: 'Check constraint: first_id <> second_id']

  indexes {
    (first_id) [name: 'idx_device_relation_first']
    (second_id) [name: 'idx_device_relation_second']
    (first_id, second_id, relation_type_id) [unique]
  }
}

Table inventory {
  id uuid [primary key]
  organization_id uuid [not null]
  title text [not null]
  code text [not null]
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  updated_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  deleted_at timestamptz
  deleted_by uuid

  indexes {
    (organization_id) [name: 'idx_inventory_org']
    (organization_id, code) [unique, name: 'uq_inventory_org_code']
  }
}

Table device_inventory_relation {
  id uuid [primary key]
  device_id uuid [not null]
  inventory_id uuid [not null]
  assigned_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  assigned_by uuid
  deleted_at timestamptz
  deleted_by uuid

  indexes {
    (device_id) [unique, name: 'uq_device_inventory_active', note: 'partial: WHERE deleted_at IS NULL - one active inventory per device']
    (inventory_id) [name: 'idx_device_inventory_rel_inventory']
  }
}

Table asset {
  id uuid [primary key]
  organization_id uuid [not null]
  asset_type_id uuid [not null]
  title text [not null]
  custom_fields_data jsonb [not null, default: `{}`]
  version int [not null, default: 1]
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  updated_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  deleted_at timestamptz
  deleted_by uuid

  indexes {
    (organization_id) [name: 'idx_asset_org']
    (asset_type_id) [name: 'idx_asset_type']
    (title) [type: gin, name: 'idx_asset_title_trgm']
  }
}

Table asset_group {
  id uuid [primary key]
  organization_id uuid [not null]
  group_type_id uuid [not null]
  title_en text [not null]
  color text
  version int [not null, default: 1]
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  updated_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  deleted_at timestamptz
  deleted_by uuid

  indexes {
    (organization_id) [name: 'idx_asset_group_org']
    (group_type_id) [name: 'idx_asset_group_type']
  }
}

Table asset_group_item {
  id uuid [primary key]
  group_id uuid [not null]
  asset_id uuid [not null]
  attached_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  detached_at timestamptz [note: 'NULL = currently attached']

  indexes {
    (group_id) [name: 'idx_asset_group_item_group']
    (asset_id) [name: 'idx_asset_group_item_asset']
    (group_id, asset_id, detached_at) [unique]
  }
}

Table geo_object {
  id uuid [primary key, note: 'Geofences, points of interest, routes']
  organization_id uuid [not null]
  geo_object_type_id uuid [not null]
  title text [not null]
  custom_fields_data jsonb [not null, default: `{}`, note: 'Includes geojson geometry']
  version int [not null, default: 1]
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  updated_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  deleted_at timestamptz
  deleted_by uuid

  indexes {
    (organization_id) [name: 'idx_geo_object_org']
    (geo_object_type_id) [name: 'idx_geo_object_type']
    (title) [type: gin, name: 'idx_geo_object_title_trgm']
  }
}

Table schedule {
  id uuid [primary key]
  organization_id uuid [not null]
  schedule_type_id uuid [not null]
  title text [not null]
  custom_fields_data jsonb [not null, default: `{}`, note: 'Schedule data itself lives here']
  version int [not null, default: 1]
  created_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  updated_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]
  deleted_at timestamptz
  deleted_by uuid

  indexes {
    (organization_id) [name: 'idx_schedule_org']
    (schedule_type_id) [name: 'idx_schedule_type']
    (title) [type: gin, name: 'idx_schedule_title_trgm']
  }
}

// ============================================
// LOCALIZATION
// ============================================

Table i18n_text {
  entity_id uuid [pk]
  field_code text [pk]
  locale text [pk]
  value text [not null]

  indexes {
    (entity_id) [name: 'idx_i18n_entity']
    (locale) [name: 'idx_i18n_locale']
    (value) [type: gin, name: 'idx_i18n_text_trgm']
  }
}

// ============================================
// CUSTOM FIELDS - VALUES (cache tables, one per field_type)
// ============================================

Table custom_field_value_text {
  entity_id uuid [pk]
  field_definition_id uuid [pk]
  value_index smallint [pk, default: 0]
  value text [not null]

  indexes {
    (field_definition_id, value) [name: 'idx_cfv_text_value']
  }
}

Table custom_field_value_number {
  entity_id uuid [pk]
  field_definition_id uuid [pk]
  value_index smallint [pk, default: 0]
  value numeric [not null]
}

Table custom_field_value_boolean {
  entity_id uuid [pk]
  field_definition_id uuid [pk]
  value_index smallint [pk, default: 0]
  value boolean [not null]
}

Table custom_field_value_date {
  entity_id uuid [pk]
  field_definition_id uuid [pk]
  value_index smallint [pk, default: 0]
  value date [not null]
}

Table custom_field_value_datetime {
  entity_id uuid [pk]
  field_definition_id uuid [pk]
  value_index smallint [pk, default: 0]
  value timestamptz [not null]
}

Table custom_field_value_geojson {
  entity_id uuid [pk]
  field_definition_id uuid [pk]
  value_index smallint [pk, default: 0]
  value jsonb [not null]
}

Table custom_field_value_schedule {
  entity_id uuid [pk]
  field_definition_id uuid [pk]
  value_index smallint [pk, default: 0]
  value jsonb [not null]
}

Table custom_field_value_option {
  entity_id uuid [pk]
  field_definition_id uuid [pk]
  value_index smallint [pk, default: 0]
  ref_item_id uuid [not null, note: 'References ci_user_catalog_item']
}

Table custom_field_value_device {
  entity_id uuid [pk]
  field_definition_id uuid [pk]
  value_index smallint [pk, default: 0]
  ref_device_id uuid [not null]
}

Table custom_field_value_entity {
  entity_id uuid [pk]
  field_definition_id uuid [pk]
  value_index smallint [pk, default: 0]
  ref_entity_id uuid [not null, note: 'Polymorphic reference, no FK']
}

Table custom_field_value_catalog {
  entity_id uuid [pk]
  field_definition_id uuid [pk]
  value_index smallint [pk, default: 0]
  ref_item_id uuid [not null, note: 'References ci_base']
}

Table custom_field_value_tag {
  entity_id uuid [pk]
  field_definition_id uuid [pk]
  value_index smallint [pk, default: 0]
  ref_tag_id uuid [not null]
}

// ============================================
// AUDIT
// ============================================

Enum source_type {
  WEB
  MOBILE
  API
  INTERNAL
  INTEGRATION
}

Table audit_event {
  id uuid [not null]
  organization_id uuid
  event_category text [not null, note: 'auth or domain']
  actor_id uuid
  ip_address inet
  user_agent text
  source_type source_type [not null, default: 'API']
  source_name text [note: 'Name of the source application']
  trace_id uuid [note: 'Distributed tracing ID for log correlation']
  aggregate_type text [note: 'Entity type: device, asset, user, etc.']
  aggregate_id uuid [note: 'Polymorphic, no FK']
  event_type text [not null, note: 'CREATED, UPDATED, DELETED, RESTORED, ROLE_ASSIGNED, etc.']
  event_data jsonb [note: 'Payload with changed_fields delta']
  occurred_at timestamptz [not null, default: `CURRENT_TIMESTAMP`]

  Note: 'Partitioned BY RANGE (occurred_at), monthly partitions, auto-created via create_audit_partition_if_needed()'

  indexes {
    (id, occurred_at) [pk]
    (actor_id, occurred_at) [name: 'idx_audit_event_actor']
    (aggregate_type, aggregate_id, occurred_at) [name: 'idx_audit_event_aggregate']
    (event_category, occurred_at) [name: 'idx_audit_event_category']
    (event_type, occurred_at) [name: 'idx_audit_event_type']
    (organization_id, occurred_at) [name: 'idx_audit_event_org']
    (trace_id) [name: 'idx_audit_event_trace']
  }
}

// ============================================
// RELATIONSHIPS
// ============================================

Ref: ci_base.catalog_id > ci_catalog.id
Ref: ci_base.organization_id > organization.id
Ref: ci_base.parent_id > ci_base.id

Ref: ci_module.id - ci_base.id
Ref: ci_catalog_category.id - ci_base.id
Ref: ci_country.id - ci_base.id
Ref: ci_role.id - ci_base.id
Ref: ci_entity_type.id - ci_base.id
Ref: ci_device_status.id - ci_base.id
Ref: ci_permission_scope.id - ci_base.id
Ref: ci_device_vendor.id - ci_base.id
Ref: ci_device_model.id - ci_base.id
Ref: ci_device_type.id - ci_base.id
Ref: ci_asset_type.id - ci_base.id
Ref: ci_geo_object_type.id - ci_base.id
Ref: ci_schedule_type.id - ci_base.id
Ref: ci_asset_group_type.id - ci_base.id
Ref: ci_device_relation_type.id - ci_base.id
Ref: ci_tag.id - ci_base.id
Ref: ci_user_catalog_item.id - ci_base.id
Ref: ci_catalog.id - ci_base.id
Ref: ci_custom_field_definition.id - ci_base.id

Ref: ci_device_model.vendor_id > ci_device_vendor.id
Ref: ci_permission_scope.module_id > ci_module.id
Ref: ci_permission_scope.entity_type_id > ci_entity_type.id
Ref: asset_group_type_to_asset_type_relation.group_type_id > ci_asset_group_type.id
Ref: asset_group_type_to_asset_type_relation.asset_type_id > ci_asset_type.id
Ref: ci_tag.entity_type_id > ci_entity_type.id
Ref: ci_catalog.organization_id > organization.id
Ref: ci_catalog.module_id > ci_module.id
Ref: ci_catalog.category_id > ci_catalog_category.id
Ref: ci_custom_field_definition.owner_catalog_item_id > ci_base.id
Ref: ci_custom_field_definition.target_entity_type_id > ci_entity_type.id

Ref: organization.parent_id > organization.id
Ref: organization.deleted_by > actor.id

Ref: actor.deleted_by > actor.id

Ref: user.id - actor.id
Ref: user.deleted_by > actor.id

Ref: member.user_id > user.id
Ref: member.organization_id > organization.id
Ref: member.deleted_by > actor.id

Ref: integration.id - actor.id
Ref: integration.deleted_by > actor.id

Ref: actor_role.actor_id > actor.id
Ref: actor_role.role_id > ci_role.id
Ref: actor_role.assigned_by > actor.id

Ref: acl_role_permission.role_id > ci_role.id
Ref: acl_role_permission.permission_scope_id > ci_permission_scope.id
Ref: acl_role_permission.granted_by > actor.id

Ref: acl_user_scope.actor_id > actor.id
Ref: acl_user_scope.permission_scope_id > ci_permission_scope.id

Ref: device.organization_id > organization.id
Ref: device.device_type_id > ci_device_type.id
Ref: device.model_id > ci_device_model.id
Ref: device.status_id > ci_device_status.id
Ref: device.deleted_by > actor.id

Ref: device_identifier.device_id > device.id

Ref: device_relation.first_id > device.id
Ref: device_relation.second_id > device.id
Ref: device_relation.relation_type_id > ci_device_relation_type.id

Ref: inventory.organization_id > organization.id
Ref: inventory.deleted_by > actor.id

Ref: device_inventory_relation.device_id > device.id
Ref: device_inventory_relation.inventory_id > inventory.id
Ref: device_inventory_relation.assigned_by > actor.id
Ref: device_inventory_relation.deleted_by > actor.id

Ref: asset.organization_id > organization.id
Ref: asset.asset_type_id > ci_asset_type.id
Ref: asset.deleted_by > actor.id

Ref: asset_group.organization_id > organization.id
Ref: asset_group.group_type_id > ci_asset_group_type.id
Ref: asset_group.deleted_by > actor.id

Ref: asset_group_item.group_id > asset_group.id
Ref: asset_group_item.asset_id > asset.id

Ref: geo_object.organization_id > organization.id
Ref: geo_object.geo_object_type_id > ci_geo_object_type.id
Ref: geo_object.deleted_by > actor.id

Ref: schedule.organization_id > organization.id
Ref: schedule.schedule_type_id > ci_schedule_type.id
Ref: schedule.deleted_by > actor.id

Ref: custom_field_value_text.field_definition_id > ci_custom_field_definition.id
Ref: custom_field_value_number.field_definition_id > ci_custom_field_definition.id
Ref: custom_field_value_boolean.field_definition_id > ci_custom_field_definition.id
Ref: custom_field_value_date.field_definition_id > ci_custom_field_definition.id
Ref: custom_field_value_datetime.field_definition_id > ci_custom_field_definition.id
Ref: custom_field_value_geojson.field_definition_id > ci_custom_field_definition.id
Ref: custom_field_value_schedule.field_definition_id > ci_custom_field_definition.id
Ref: custom_field_value_option.field_definition_id > ci_custom_field_definition.id
Ref: custom_field_value_option.ref_item_id > ci_user_catalog_item.id
Ref: custom_field_value_device.field_definition_id > ci_custom_field_definition.id
Ref: custom_field_value_device.ref_device_id > device.id
Ref: custom_field_value_entity.field_definition_id > ci_custom_field_definition.id
Ref: custom_field_value_catalog.field_definition_id > ci_custom_field_definition.id
Ref: custom_field_value_catalog.ref_item_id > ci_base.id
Ref: custom_field_value_tag.field_definition_id > ci_custom_field_definition.id
Ref: custom_field_value_tag.ref_tag_id > ci_tag.id

Ref: audit_event.organization_id > organization.id
Ref: audit_event.actor_id > actor.id
```
{% endcode %}

## Update frequency

Data in BDR is synchronized in real-time with source systems. Updates occur immediately as changes happen, with audit trails capturing all modifications for compliance and historical analysis.

## `ci_base`

BDR uses a **Single Table Inheritance** pattern for all reference data through the `ci_base` table. This design consolidates system dictionaries, classifications, and user-defined reference items into one unified structure, providing consistency and flexibility across the entire schema.

**Architecture:**

The `ci_base` table serves as the foundation for all reference data, using a `discriminator` field to identify the specific reference type. Each reference type has a corresponding table (like `ci_device_type`, `ci_asset_type`) that shares the same `id` as `ci_base`, creating a type-safe inheritance relationship. The `discriminator` isn't set manually. A trigger derives it automatically from the 4-character type code embedded in the entity's UUID, and it fails the insert if the UUID's type code is unrecognized.

Hierarchies (e.g., nested asset types or tags) are handled directly on `ci_base` through its own `parent_id`/`path` columns rather than through separate category tables. A single `is_hierarchical` flag turns this on per catalog item.

**How business entities connect to ci\_base:**

All business entities in BDR reference `ci_base` subtypes to define their classification and behavior:

* `organization` and `user`/`member` are *not* typed through `ci_base`, since organizations and users are core entities in their own right, not classified by a catalog item
* `device` → references `ci_device_type`, optionally `ci_device_model` (which itself references `ci_device_vendor`), and `ci_device_status`
* `asset` → references `ci_asset_type`
* `asset_group` → references `ci_asset_group_type`, with allowed member types constrained via `asset_group_type_to_asset_type_relation`
* `geo_object` → references `ci_geo_object_type`
* `schedule` → references `ci_schedule_type`
* `inventory` has no catalog-item type of its own

**Reference type categories:**

| Category                      | Tables                                                                                                       | Purpose                                                                                |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| **System configuration**      | `ci_module`, `ci_country`, `ci_role`                                                                          | Define system modules, geographic references, and user roles                           |
| **Entity type definitions**   | `ci_entity_type`, `ci_device_type`, `ci_asset_type`, `ci_geo_object_type`, `ci_schedule_type`, `ci_asset_group_type` | Classify all business entities by type                                                 |
| **Device catalog**            | `ci_device_vendor`, `ci_device_model`, `ci_device_status`                                                    | Model devices by manufacturer, model, and current status                               |
| **Access control**            | `ci_permission_scope`                                                                                         | Define what permissions can be granted (connected to `ci_module` and `ci_entity_type`) |
| **Relationships**             | `ci_device_relation_type`                                                                                     | Define types of relationships between devices (master-slave, backup, etc.)             |
| **Categorization**            | `ci_tag`, `ci_catalog_category`, `ci_user_catalog_item`                                                       | Enable flexible tagging and user-defined catalogs                                       |
| **Catalog / custom fields**   | `ci_catalog`, `ci_custom_field_definition`                                                                    | Catalog-of-catalogs and custom field metadata, both modeled as catalog items themselves |

<details>

<summary><strong>Example query patterns</strong></summary>

```sql
-- Get all device types for an organization (system + custom)
SELECT cb.id, cb.code, cb.title_en, cb.is_system
FROM bdr.ci_base cb
JOIN bdr.ci_device_type dt ON dt.id = cb.id
WHERE cb.discriminator = 'device_type'
  AND (cb.is_system = true OR cb.organization_id = $org_id)
  AND cb.deleted_at IS NULL;

-- Get device models with their vendor
SELECT
  cb.code as model_code,
  cb.title_en as model_name,
  vendor_cb.title_en as vendor_name
FROM bdr.ci_base cb
JOIN bdr.ci_device_model dm ON dm.id = cb.id
JOIN bdr.ci_device_vendor v ON v.id = dm.vendor_id
JOIN bdr.ci_base vendor_cb ON vendor_cb.id = v.id
WHERE cb.discriminator = 'device_model'
  AND cb.deleted_at IS NULL;

-- Get hierarchical tag structure
SELECT cb.id, cb.code, cb.title_en, cb.path, cb.parent_id
FROM bdr.ci_base cb
JOIN bdr.ci_tag t ON t.id = cb.id
WHERE cb.discriminator = 'tag'
  AND cb.deleted_at IS NULL
ORDER BY cb.path;
```

</details>

## Key tables by category

The tables in BDR are organized into functional categories. The descriptions below summarize the most important tables by their business purpose.

<details>

<summary><code>organization</code></summary>

**Purpose:** Hierarchical organization management

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>id</code>, <code>parent_id</code>, <code>path</code>, <code>code</code>, <code>title_en</code>, <code>is_active</code>, <code>is_dealer</code>, <code>version</code>, <code>deleted_at</code></td></tr><tr><td><strong>Indexing</strong></td><td>GiST index on <code>path</code> for hierarchical queries, unique index on <code>code</code>, index on <code>parent_id</code></td></tr><tr><td><strong>Special notes</strong></td><td>Uses ltree for multi-level hierarchies. <code>is_dealer</code> grants the right to create child organizations. <code>version</code> supports optimistic locking</td></tr></tbody></table>

</details>

<details>

<summary><code>actor</code></summary>

**Purpose:** Abstraction shared by every ACL principal: users, integrations, and future system actors

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>id</code>, <code>actor_type</code> (<code>USER</code>, <code>INTEGRATION</code>, <code>SYSTEM</code>), <code>version</code></td></tr><tr><td><strong>Special notes</strong></td><td>All ACL tables (<code>actor_role</code>, <code>acl_role_permission</code>, <code>acl_user_scope</code>) and every <code>deleted_by</code> column key off <code>actor_id</code>, not <code>user_id</code>, so integrations can hold roles and delete records too</td></tr></tbody></table>

</details>

<details>

<summary><code>user</code> / <code>member</code></summary>

**Purpose:** User accounts and their organization memberships

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>user</code>: <code>id</code> (shared with <code>actor</code>), <code>identity_provider</code>, <code>identity_provider_id</code>, <code>full_name</code>. <code>member</code>: <code>user_id</code>, <code>organization_id</code>, <code>custom_fields_data</code></td></tr><tr><td><strong>Indexing</strong></td><td>Unique index on (<code>identity_provider</code>, <code>identity_provider_id</code>). Unique index on (<code>user_id</code>, <code>organization_id</code>) in <code>member</code></td></tr><tr><td><strong>Special notes</strong></td><td>A user's org membership is a separate row in <code>member</code>, so one user can belong to multiple organizations. Users authenticate through an external identity provider (Keycloak by default)</td></tr></tbody></table>

</details>

<details>

<summary><code>integration</code></summary>

**Purpose:** External systems acting on the platform (e.g. via API keys)

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>id</code> (shared with <code>actor</code>), <code>name</code>, <code>credential_ref</code>, <code>is_active</code></td></tr><tr><td><strong>Special notes</strong></td><td><code>credential_ref</code> points at a secure external vault, so no credentials are stored in this table</td></tr></tbody></table>

</details>

<details>

<summary><code>device</code> / <code>device_identifier</code></summary>

**Purpose:** Physical tracking devices and their hardware identifiers

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>device</code>: <code>id</code>, <code>organization_id</code>, <code>device_type_id</code>, <code>model_id</code>, <code>status_id</code>, <code>title</code>, <code>custom_fields_data</code>, <code>version</code>. <code>device_identifier</code>: <code>device_id</code>, <code>type</code>, <code>value</code>, <code>namespace</code></td></tr><tr><td><strong>Indexing</strong></td><td>GIN trigram index on <code>title</code> for fuzzy search. Unique index on (<code>type</code>, <code>value</code>) for global identifiers, or (<code>type</code>, <code>value</code>, <code>namespace</code>) when namespaced</td></tr><tr><td><strong>Special notes</strong></td><td>A device can carry multiple identifiers (IMEI, MAC, serial number, etc.) instead of a single hardware ID column. Custom fields live directly in <code>custom_fields_data</code> on the device row</td></tr></tbody></table>

</details>

<details>

<summary><code>asset</code></summary>

**Purpose:** Physical or virtual assets

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>id</code>, <code>organization_id</code>, <code>asset_type_id</code>, <code>title</code>, <code>custom_fields_data</code>, <code>version</code></td></tr><tr><td><strong>Indexing</strong></td><td>Indexes on <code>organization_id</code> and <code>asset_type_id</code>. GIN trigram index on <code>title</code></td></tr><tr><td><strong>Special notes</strong></td><td>Custom fields are stored directly on the row, not through a separate customizable-entity table</td></tr></tbody></table>

</details>

<details>

<summary><code>inventory</code> / <code>device_inventory_relation</code></summary>

**Purpose:** Inventory and warehouse records, and device assignment to inventory

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>inventory</code>: <code>id</code>, <code>organization_id</code>, <code>title</code>, <code>code</code>. <code>device_inventory_relation</code>: <code>device_id</code>, <code>inventory_id</code>, <code>assigned_at</code>, <code>deleted_at</code></td></tr><tr><td><strong>Indexing</strong></td><td>Unique index on (<code>organization_id</code>, <code>code</code>). A partial unique index ensures a device has at most one active inventory assignment</td></tr><tr><td><strong>Special notes</strong></td><td>Assignment is soft-deletable, so device-inventory history is preserved</td></tr></tbody></table>

</details>

<details>

<summary><code>geo_object</code></summary>

**Purpose:** Geofences, points of interest, and routes

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>id</code>, <code>organization_id</code>, <code>geo_object_type_id</code>, <code>title</code>, <code>custom_fields_data</code></td></tr><tr><td><strong>Special notes</strong></td><td>Geometry (formerly a dedicated <code>geojson</code> column) is now stored as a custom field inside <code>custom_fields_data</code></td></tr></tbody></table>

</details>

<details>

<summary><code>schedule</code></summary>

**Purpose:** Reusable schedules referenced by other entities

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>id</code>, <code>organization_id</code>, <code>schedule_type_id</code>, <code>title</code>, <code>custom_fields_data</code></td></tr><tr><td><strong>Special notes</strong></td><td>Schedule data itself (recurrence rules, time windows) is stored in <code>custom_fields_data</code></td></tr></tbody></table>

</details>

<details>

<summary><code>asset_group</code></summary>

**Purpose:** Asset grouping with historical tracking

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>id</code>, <code>organization_id</code>, <code>group_type_id</code>, <code>title_en</code>, <code>color</code></td></tr><tr><td><strong>Relationships</strong></td><td><code>FROM bdr.asset_group AS ag JOIN bdr.asset_group_item AS agi ON agi.group_id = ag.id JOIN bdr.asset AS a ON a.id = agi.asset_id WHERE agi.detached_at IS NULL</code></td></tr><tr><td><strong>Special notes</strong></td><td>Time-based membership via <code>asset_group_item</code>, query current members with <code>WHERE detached_at IS NULL</code>. Allowed member types per group type are constrained via <code>asset_group_type_to_asset_type_relation</code></td></tr></tbody></table>

</details>

<details>

<summary><code>ci_custom_field_definition</code></summary>

**Purpose:** Custom field definitions and metadata, modeled as a catalog item

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>id</code> (shared with <code>ci_base</code>), <code>owner_catalog_item_id</code>, <code>target_entity_type_id</code>, <code>field_type</code>, <code>is_required</code>, <code>params</code></td></tr><tr><td><strong>Content</strong></td><td>12 field types: <code>STRING</code>, <code>TEXT</code>, <code>NUMBER</code>, <code>BOOLEAN</code>, <code>DATE</code>, <code>DATETIME</code>, <code>GEOJSON</code>, <code>SCHEDULE</code>, <code>OPTIONS</code>, <code>DEVICE</code>, <code>REFERENCE</code>, <code>CATALOG</code>, <code>TAG</code></td></tr><tr><td><strong>Special notes</strong></td><td>Values are stored both as the source of truth (<code>custom_fields_data</code> JSONB on the owning entity) and, per type, in a dedicated <code>custom_field_value_*</code> cache table for filtering/sorting. <code>OPTIONS</code> is a wrapper around <code>CATALOG</code>, and each <code>OPTIONS</code> field gets its own hidden user catalog under the hood</td></tr></tbody></table>

</details>

<details>

<summary><code>acl_role_permission</code></summary>

**Purpose:** Role-based permission management

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>id</code>, <code>role_id</code>, <code>permission_scope_id</code>, <code>target_entity_id</code>, <code>actions</code></td></tr><tr><td><strong>Content</strong></td><td>Action bitmask (READ=1, CREATE=2, UPDATE=4, DELETE=8), target-specific or entity-type-wide permissions</td></tr><tr><td><strong>Relationships</strong></td><td><code>FROM bdr.actor_role AS ar JOIN bdr.acl_role_permission AS rp ON rp.role_id = ar.role_id WHERE ar.actor_id = $actor_id</code></td></tr><tr><td><strong>Special notes</strong></td><td>Effective permissions = role permissions ∩ <code>acl_user_scope</code>. An actor's <code>acl_user_scope</code> is a whitelist filter: empty means unrestricted role access, filled means access is narrowed to the listed objects</td></tr></tbody></table>

</details>

<details>

<summary><code>audit_event</code></summary>

**Purpose:** Unified audit log for all system changes

<table><thead><tr><th width="139">Attribute</th><th>Details</th></tr></thead><tbody><tr><td><strong>Key fields</strong></td><td><code>id</code>, <code>organization_id</code>, <code>event_category</code>, <code>actor_id</code>, <code>source_type</code>, <code>trace_id</code>, <code>aggregate_type</code>, <code>aggregate_id</code>, <code>event_type</code>, <code>event_data</code>, <code>occurred_at</code></td></tr><tr><td><strong>Indexing</strong></td><td>Indexes on (<code>actor_id</code>, <code>occurred_at</code>), (<code>aggregate_type</code>, <code>aggregate_id</code>, <code>occurred_at</code>), (<code>event_category</code>, <code>occurred_at</code>), (<code>organization_id</code>, <code>occurred_at</code>)</td></tr><tr><td><strong>Special notes</strong></td><td>Physically partitioned by <code>occurred_at</code> (monthly range partitions, auto-created). <code>event_category</code> is <code>auth</code> (LOGIN, LOGOUT, FAILED_LOGIN, PASSWORD_RESET, SESSION_EXPIRED) or <code>domain</code> (CREATED, UPDATED, DELETED, RESTORED, ROLE_ASSIGNED, ROLE_REVOKED, PERMISSION_GRANTED, PERMISSION_REVOKED, LINKED, UNLINKED, ATTACHED, DETACHED). <code>source_type</code> and <code>trace_id</code> support distributed tracing, and the table stores field-level change deltas in <code>event_data</code> JSONB</td></tr></tbody></table>

</details>

## Data relationships

BDR implements sophisticated relationship patterns for flexible data modeling:

**Hierarchical structures**

* Organizations use ltree paths for efficient tree queries
* Reference items (`ci_base`) support optional hierarchies through their own `parent_id`/`path`, gated by `is_hierarchical`
* Automatic path maintenance via database triggers

**Inheritance patterns**

* ID inheritance: `ci_base` → reference type tables (`ci_device_type`, `ci_asset_type`, etc.), and `actor` → `user` and `integration`
* Custom fields are attached per-entity via a `custom_fields_data` JSONB column directly on each business table (`device`, `asset`, `geo_object`, `schedule`), rather than through a shared base entity table
* Type discrimination via the `discriminator` field on `ci_base`, derived automatically from the entity's UUID

**Optimistic locking**

Business entities and `ci_base` rows carry a `version` integer. Updates use `SET version = version + 1 WHERE id = $id AND version = $expected_version`. Zero affected rows signals a version conflict, letting clients detect and resolve concurrent edits without database-level locks. `audit_event` and other append-only or bulk-operated tables don't use this pattern.

**Polymorphic relationships**

Certain tables use polymorphic references without foreign key constraints for maximum flexibility:

* `acl_role_permission.target_entity_id` and `acl_user_scope.target_entity_id` → any business entity
* `audit_event.aggregate_id` → any business entity, paired with `aggregate_type`
* `custom_field_value_entity.ref_entity_id` → any business entity

These relationships are validated at the application level.

## Additional information

### Data validation

BDR enforces data integrity through multiple mechanisms:

**Database constraints**

* UNIQUE constraints with soft delete support (partial indexes WHERE `deleted_at` IS NULL)
* CHECK constraints (e.g., `device_relation` ensures `first_id` ≠ `second_id`, and `ci_base.code` must start with a letter)
* NOT NULL constraints on required fields
* DEFAULT values for timestamps, booleans, and JSONB columns

**Application-level validation**

* Entity type validation for polymorphic references
* Catalog validation for custom field references
* Custom field type validation
* Optimistic-locking version checks on update

### Query optimization

Tables are organized with specific indexing strategies:

**Standard indexes:**

* All foreign keys have dedicated indexes
* Time-based indexes on `created_at`, `updated_at`, `deleted_at`
* Composite indexes for frequently joined columns

**Specialized indexes:**

* GiST indexes on ltree paths for hierarchical queries
* Partial unique indexes supporting soft delete
* GIN trigram (`pg_trgm`) indexes for fuzzy search on `title`/`title_en` and translated text
* Custom field value indexes for filtering and sorting
* Audit event indexes on time + entity for efficient lookups

**Performance considerations:**

* Connection pooling recommended (PgBouncer)
* Regular VACUUM maintenance for large tables
* `audit_event` is physically partitioned by month, so older partitions can be detached or archived independently
* Materialized views for complex access control calculations
