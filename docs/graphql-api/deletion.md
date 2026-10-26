---
description: >-
  What a delete does in Navixy GraphQL API, which references block a delete, and
  what you can't get back afterward.
tags:
  - fundamentals
  - beta
---

# Deleting records

A delete in Navixy GraphQL API is final for you: no operation returns or restores a deleted record. While other records still refer to a record, the API refuses to delete it with an [`IN_USE`](error-handling.md#in-use-409) error. This page explains both rules and the few operations that work differently.

The rules apply to Business Data Repository and to IoT Logic flows. Tracking stores no records of its own, and Billing and Console have no delete operations. For the IAM operations that delete roles and revoke them, see [Roles](iam/api-reference/roles.md) and [Role assignments](iam/api-reference/role-assignments.md).

## What a delete does

`memberRemove` and every `*Delete` mutation hide the record. It no longer appears in any query, list, or `total`, and a lookup by its ID returns `null`. The record stays in storage, so the [audit log](business-data-repository/api-reference/audit.md) can still name it, but the API has no operation that returns the record itself. `flowDelete` works the same way: it hides the flow and removes the links between the flow and its devices.

Three operations erase data permanently instead:

| Operation | What it erases |
| --- | --- |
| `deviceIdentifierRemove` | The identifier of a device |
| `deviceRelationRemove` | The relation between two devices |
| `assetTypeUpdate` or `geoObjectTypeUpdate` that deletes a custom field definition with `onValues: CASCADE` | The values of that field on every asset or geo object that had one |

Some changes look like deletes but keep a record. `assetGroupItemsRemove` ends a membership and keeps it in the group's `history`. `deviceInventoryUnlink` ends an inventory assignment and keeps it in the device's `inventoryHistory`. Archiving a custom field definition keeps the definition, with `isArchived` set to `true`, and keeps its values. You can restore an archived field.

### What a delete changes in other records

Some deletes also update the records that referred to the deleted record:

* `deviceDelete` removes the device from every `DEVICE` custom field that isn't required. A required field blocks the delete instead, as described below.
* `assetDelete` releases the devices assigned to the asset, so you can assign them to another asset.
* After `assetGroupDelete`, the group no longer appears in the `groups` of its assets.

### Codes and identifiers after a delete

A deleted record no longer reserves its code. You can create a type, tag, catalog item, or custom field definition with the code of a deleted record. The new record doesn't get the data of the deleted record: a new custom field definition starts with no values.

Device identifiers are different. The identifiers of a deleted device stay reserved, so no other device can use the same `type`, `value`, and `namespace`. To use the same hardware with a new device record, remove the identifiers with `deviceIdentifierRemove` before you delete the old device, or keep the old record instead of deleting it.

## When a delete is refused

A delete fails with `IN_USE` while another record that isn't deleted still refers to the record. Nothing is deleted and nothing else changes. The error has a `blockers` list that names what still refers to the record. Each entry covers one kind of record and one of its fields: `entityType` is the kind of record, `path` is the field, and `ids` lists up to 20 of those records that your role can read. An empty `ids` list means that such records exist, but you can't read them. [In use (409)](error-handling.md#in-use-409) shows a complete response.

The list has at most five entries. They come in a fixed order: the record's own fields first, then custom field values, then custom field definitions, then the other references, such as an IoT Logic flow. When there are more than five, `extensions.hasMoreBlockers` is `true`. Clear the entries you got and send the delete again: the next refusal names the next blockers, up to five, or the delete succeeds.

| Record you delete | What blocks the delete | Path in the error |
| --- | --- | --- |
| Asset type | An asset of that type | `type` |
| Asset type | An asset group type that lists it in `allowedAssetTypes` | `allowedAssetTypes` |
| Geo object type | A geo object of that type | `type` |
| Device type | A device of that type | `type` |
| Asset group type | An asset group of that type | `type` |
| Catalog | An item of the catalog | `catalog` |
| Catalog item | A catalog item that names it as its parent | `parent` |
| Asset | An asset group that has the asset among its current members | `currentAssets` |
| Inventory | A device assigned to the inventory | `inventory` |
| Device | A relation from another device to this device | `relationsFrom` |
| Device | A required `DEVICE` custom field that holds the device | `customFields.<code>` |
| Device | An IoT Logic flow that lists the device in its data source node, in a workspace that has IoT Logic | `nodes.devices` |
| Any record that a `REFERENCE` custom field can name | An asset or a geo object whose `REFERENCE` field holds it | `customFields.<code>` |
| Any record that a custom field definition can name | The asset type or geo object type that owns a field whose `refSubtypeIds` or `defaultRefIds` name it | `customFieldDefinitions.<code>.params.refSubtypes`, `customFieldDefinitions.<code>.params.defaultRefs` |

These rules apply to every row of the table:

* Only references from records that aren't deleted count. A deleted asset doesn't block the delete of its type, and a membership that ended doesn't block the delete of the asset.
* A custom field value counts even when its field is archived, because archiving keeps the value.
* A record doesn't block its own delete. An asset that names itself in a `REFERENCE` field can be deleted, and a field definition never blocks the delete of the type that owns it.
* An IoT Logic flow counts whether it's enabled or disabled. The flow IDs appear in `ids` only when your role can read IoT Logic flows.
* `flowDelete` succeeds even while the flow lists devices.
* When a delete and a change that adds a reference to the same record run at the same time, only one of them succeeds. If the delete runs first, the change fails with `VALIDATION_ERROR` or `NOT_FOUND`, because the record that it refers to no longer exists. If the change runs first, the delete fails with `IN_USE`. This doesn't apply to IoT Logic flows: a flow saved at the same moment as a device delete can still list the deleted device.

### How to clear the blockers of a delete

Change the records in `blockers` through their own mutations, then send the delete again:

| Path | How to clear it |
| --- | --- |
| `type` | Delete those records. The API can't change the type of an asset, a geo object, an asset group, or a device |
| `allowedAssetTypes` | Remove the type from the asset group type's `allowedAssetTypes` with `assetGroupTypeUpdate` |
| `catalog` | Delete the items of the catalog |
| `parent` | Delete the child items, or give them another parent with `parentId` in `userCatalogItemUpdate` |
| `currentAssets` | Remove the asset from the groups with `assetGroupItemsRemove` |
| `inventory` | Unassign the devices with `deviceInventoryUnlink` |
| `relationsFrom` | Remove the relations with `deviceRelationRemove` |
| `customFields.<code>` | Change the value on the asset or geo object, unset it if the field isn't required, delete the field with `onValues: CASCADE`, or delete the record that holds it. The value of an archived field can only be unset. For a required `DEVICE` field, put another device in the field or make the field not required |
| `customFieldDefinitions.<code>.params.*` | Change `refSubtypeIds` or `defaultRefIds` of the field on its type, delete the field, or delete the type |
| `nodes.devices` | Remove the device from the flow's data source node with `flowUpdate`, or delete the flow |

## Best practices

1. Send `version` with every delete. A delete without `version` can remove a record that someone changed after you last read it. `flowDelete` always requires it. See [Optimistic locking](optimistic-locking.md).
2. Read what you need first. The API doesn't return a deleted record, so fetch an asset group's `history` before you delete the group.
3. Handle `IN_USE` as a normal result of a delete in your app. Show the user the records in `blockers`, so they know what to change. When `hasMoreBlockers` is `true`, tell the user that more records refer to the record, and that the next delete attempt names them.
4. Before you delete a device whose hardware you want to use again, remove its identifiers. The identifiers of a deleted device stay reserved.

## See also

* [Error handling](error-handling.md): Learn about the `IN_USE` response and every other error code.
* [Optimistic locking](optimistic-locking.md): Protect updates and deletes with `version`.
* [Managing device records and identifiers](business-data-repository/guides/managing-device-records-and-identifiers.md#deleting-a-device): Delete a device and clear what blocks the delete.
* [Organizing assets into groups](business-data-repository/guides/organizing-assets-into-groups.md): Read a group's membership history before you delete the group.
