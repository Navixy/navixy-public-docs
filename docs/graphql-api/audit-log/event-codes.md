---
description: >-
  The event codes that each product of Navixy GraphQL API records in its audit
  log, with the operation and the aggregate type of each code.
tags:
  - reference
  - beta
---

# Audit event codes

Every audit event has an `eventCode`, the name that the product gave to the change, and an `operation`, one of `CREATED`, `UPDATED`, `DELETED`, or `RESTORED`. The tables below list the codes of each product with their operations. [Audit log](README.md#event-codes-and-operations) explains how the two fields relate.

The `eventCodes` filter matches a code exactly as it's listed here, letter case included. A product adds a code when it starts recording a new kind of change, and a code that was recorded once is never renamed or reused. So show a code that isn't listed here as it is. Two products can record the same code, so read a code together with `product`.

The "Aggregate type" column gives the `aggregateType` of the event, which is also the value to send in the `aggregateTypes` filter. `aggregateId` is the ID of that record unless the table says otherwise.

## Business Data Repository

The product code is `bdr`. The following table lists every code that Business Data Repository records, with its operation, its aggregate type, and its meaning:

| Code | Operation | Aggregate type | Meaning |
| --- | --- | --- | --- |
| `asset_created` | `CREATED` | `asset` | An asset was created |
| `asset_updated` | `UPDATED` | `asset` | An asset was changed |
| `asset_deleted` | `DELETED` | `asset` | An asset was deleted |
| `asset_group_created` | `CREATED` | `asset_group` | An asset group was created |
| `asset_group_updated` | `UPDATED` | `asset_group` | An asset group was changed |
| `asset_group_deleted` | `DELETED` | `asset_group` | An asset group was deleted |
| `catalog_item_created` | `CREATED` | `catalog_item` | A catalog item was created |
| `catalog_item_updated` | `UPDATED` | `catalog_item` | A catalog item was changed |
| `catalog_item_deleted` | `DELETED` | `catalog_item` | A catalog item was deleted |
| `custom_field_def_created` | `CREATED` | `custom_field_definition` | A custom field definition was created |
| `custom_field_def_updated` | `UPDATED` | `custom_field_definition` | A custom field definition was changed |
| `custom_field_def_archived` | `UPDATED` | `custom_field_definition` | A custom field definition was archived |
| `custom_field_def_restored` | `UPDATED` | `custom_field_definition` | A custom field definition was restored from the archive |
| `custom_field_def_deleted` | `DELETED` | `custom_field_definition` | A custom field definition was deleted |
| `device_created` | `CREATED` | `device` | A device was created |
| `device_updated` | `UPDATED` | `device` | A device was changed |
| `device_deleted` | `DELETED` | `device` | A device was deleted |
| `geo_object_created` | `CREATED` | `geo_object` | A geo object was created |
| `geo_object_updated` | `UPDATED` | `geo_object` | A geo object was changed |
| `geo_object_deleted` | `DELETED` | `geo_object` | A geo object was deleted |
| `inventory_created` | `CREATED` | `inventory` | An inventory was created |
| `inventory_updated` | `UPDATED` | `inventory` | An inventory was changed |
| `inventory_deleted` | `DELETED` | `inventory` | An inventory was deleted |
| `schedule_created` | `CREATED` | `schedule` | A schedule was created |
| `schedule_updated` | `UPDATED` | `schedule` | A schedule was changed |
| `schedule_deleted` | `DELETED` | `schedule` | A schedule was deleted |
| `workspace_created` | `CREATED` | `workspace` | A workspace was created |
| `workspace_updated` | `UPDATED` | `workspace` | A workspace, its Console state, or its organization was changed |
| `workspace_deleted` | `DELETED` | `workspace` | A workspace was deleted |
| `workspace_restored` | `RESTORED` | `workspace` | A deleted workspace was restored |

The following table lists the codes that older events of Business Data Repository can have. Nothing records them now:

| Code | Operation | Aggregate type | Meaning |
| --- | --- | --- | --- |
| `integration_created` | `CREATED` | `integration` | An integration was created |
| `integration_updated` | `UPDATED` | `integration` | An integration was changed |
| `integration_deleted` | `DELETED` | `integration` | An integration was deleted |
| `member_created` | `CREATED` | `member` | A member was created |
| `member_updated` | `UPDATED` | `member` | A member was changed |
| `member_deleted` | `DELETED` | `member` | A member was deleted |

## Billing

The product code is `billing`. The following table lists every code that Billing records, with its operation, its aggregate type, and its meaning:

| Code | Operation | Aggregate type | Meaning |
| --- | --- | --- | --- |
| `invoice_issued` | `CREATED` | `invoice` | An invoice was issued |
| `manual_adjustment_created` | `CREATED` | `manual_adjustment` | Navixy adjusted a balance by hand |
| `manual_payment_created` | `CREATED` | `manual_payment` | Navixy recorded a payment by hand |
| `metric_created` | `CREATED` | `metric` | A usage metric was created |
| `metric_updated` | `UPDATED` | `metric` | A usage metric was changed |
| `metric_schema_created` | `CREATED` | `metric_schema` | A metric schema was created |
| `metric_schema_updated` | `UPDATED` | `metric_schema` | A metric schema was changed |
| `plan_created` | `CREATED` | `plan` | A plan was created |
| `plan_updated` | `UPDATED` | `plan` | A plan was changed |
| `plan_branch_created` | `CREATED` | `plan_branch` | A plan branch was created |
| `plan_branch_updated` | `UPDATED` | `plan_branch` | A plan branch was changed |
| `product_domain_created` | `CREATED` | `product_domain` | A product domain was created |
| `product_domain_updated` | `UPDATED` | `product_domain` | A product domain was changed |
| `subscription_created` | `CREATED` | `subscription` | A workspace subscribed to a plan |
| `subscription_cancelled` | `UPDATED` | `subscription` | A subscription was cancelled |

## IAM

The product code is `iam`. Every IAM event belongs to an organization and names no workspace.

The aggregate ID of a `role_assignment` event is the role's ID, not the ID of the person or API key that received the role. The aggregate ID of an `access_record` or `personal_resource_filter` event is the ID of the person or API key. The following table lists every code that IAM records, with its operation, its aggregate type, and its meaning:

| Code | Operation | Aggregate type | Meaning |
| --- | --- | --- | --- |
| `role_created` | `CREATED` | `role` | A role was created |
| `role_updated` | `UPDATED` | `role` | A role or the workspaces it covers were changed |
| `role_deleted` | `DELETED` | `role` | A role was deleted |
| `role_assigned` | `CREATED` | `role_assignment` | A role was given to a person or an API key that didn't have it |
| `role_reassigned` | `UPDATED` | `role_assignment` | A role that a person or an API key already had was given again with another expiry |
| `role_revoked` | `DELETED` | `role_assignment` | A role was taken away: revoked, expired, or ended because the person left the organization |
| `access_record_created` | `CREATED` | `access_record` | A person's or an API key's access record in an organization was created |
| `access_record_updated` | `UPDATED` | `access_record` | A person's or an API key's access record in an organization was changed |
| `access_record_deleted` | `DELETED` | `access_record` | A person's or an API key's access record in an organization was deleted |
| `personal_resource_filter_deleted` | `DELETED` | `personal_resource_filter` | A person's or an API key's personal resource filter in an organization was deleted |

## Console

The product code is `console`. An invitation event is filed under the invited person: its aggregate type is `member` and its aggregate ID is the person's ID. The following table lists every code that Console records, with its operation, its aggregate type, and its meaning:

| Code | Operation | Aggregate type | Meaning |
| --- | --- | --- | --- |
| `organization_created` | `CREATED` | `organization` | An organization was created |
| `organization_attached` | `UPDATED` | `organization` | An organization was attached to a parent organization |
| `organization_updated` | `UPDATED` | `organization` | An organization was changed |
| `organization_detached` | `UPDATED` | `organization` | An organization was detached from its parent organization |
| `organization_deleted` | `DELETED` | `organization` | An organization was deleted |
| `workspace_created` | `CREATED` | `workspace` | A workspace was created |
| `workspace_updated` | `UPDATED` | `workspace` | A workspace was changed |
| `workspace_termination_requested` | `UPDATED` | `workspace` | The termination of a workspace was requested |
| `workspace_terminated` | `DELETED` | `workspace` | A workspace was terminated |
| `workspace_product_enable_requested` | `UPDATED` | `workspace` | Enabling a product in a workspace was requested |
| `workspace_product_disable_requested` | `UPDATED` | `workspace` | Disabling a product in a workspace was requested |
| `member_removed` | `DELETED` | `member` | A member was removed from an organization |
| `member_renamed` | `UPDATED` | `member` | A member was renamed |
| `invitation_sent` | `CREATED` | `member` | A person was invited |
| `invitation_revoked` | `DELETED` | `member` | An invitation was revoked |
| `invitation_accepted` | `UPDATED` | `member` | An invitation was accepted |
| `invitation_expired` | `UPDATED` | `member` | An invitation expired |
| `api_key_created` | `CREATED` | `api_key` | An API key was created |
| `api_key_revoked` | `DELETED` | `api_key` | An API key was revoked |

## See also

* [Audit log](README.md): How the audit log works and how each product reads its log
* [Audit](../core-api-reference/audit.md): The event type, its filter, and its order
