---
description: >-
  How to assign a device to an asset, replace it, create an asset group, add assets to a
  group, add a custom field to an asset type, and tag an asset.
tags:
  - how-to
  - beta
---

# Manage assets

{% hint style="info" %}
**Access**

Each task on this page needs a role that allows it. For example, to change an asset, your role must let you view and edit assets. Asset groups and asset types have access settings of their own. See [Access to Business Data Repository](../README.md#access-to-business-data-repository).
{% endhint %}

## Assign a device to an asset

You assign a device to an asset through a **Device** field on the asset's type. If the type has no device field yet, learn how to add it in [Add a custom field to an asset type](#add-a-custom-field-to-an-asset-type). The device will then send its position and readings as that asset.

1. On the **Assets** page, open the tab of the asset type, then click <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> in the asset's row.
2. In the **Device** field, select a device from the list.
3. If the device already belongs to another asset, click **Confirm** in the message that asks to reassign it. The device moves to this asset rather than being shared.
4. Click **Save**.

An asset can have several devices, one in each **Device** field of its type. Turn on **Primary device** for the one whose position becomes the asset's position. Only one device can be primary, so turning it on for one field turns it off for the others. If you mark none, the first device is primary.

## Replace an asset's device

When you swap the device in a vehicle, select the new device on the asset. Navixy copies nothing from the old device. For example, the new device isn't in the IoT Logic flows of the old one.

1. On the **Assets** page, open the tab of the asset type, then click <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> in the asset's row.
2. In the **Device** field, select the new device.
3. Click **Save**.

The new device takes the old one's place in the field, and the field keeps its **Primary device** setting. Tracking and Reports treat the asset's history differently after the swap:

* **Tracking**: **Trip history** shows the trips of the asset's primary device at the time you open it, for the whole period you choose. If you replaced the primary device, the old device's trips no longer show on the asset, and the new device's trips show for the whole period, including the time before you assigned it.
* **Reports**: Trips and readings stay with the asset that the device was assigned to when Navixy prepared them. For details, see [How report data is prepared](../../reports/how-report-data-works.md#a-gps-device-must-be-assigned-to-the-asset-first).

If you connect a sensor to the same input number on the new device, its reading keeps the same name. The values that the old device reported don't move to the new one.

## Create an asset group

A group is a set of assets you handle together, and an asset can be in any number of groups.

1. On the **Assets** page, check that the asset groups panel is open. If it isn't, click <picture><source srcset="../../.gitbook/assets/icons/keyboard_double_arrow_left-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/keyboard_double_arrow_left.svg" alt="Show the asset groups panel" data-size="line"></picture> in the table header.
2. In the **Create asset group** entry, click <picture><source srcset="../../.gitbook/assets/icons/add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/add.svg" alt="Create asset group" data-size="line"></picture>.
3. In **Name**, replace the default text with the group's name. You can also choose a color for the group.
4. Under **Select assets to add**, choose the assets the group starts with. You can leave this empty and add them later.
5. Click **Save**.

## Add assets to a group

Use this for assets that already exist, whichever type they are.

1. On the **Assets** page, open the tab of the asset type the assets belong to.
2. Select the assets with the checkbox at the start of each row.
3. Click **Add to group** in the entry of the group you want.

**Add to group** doesn't appear when every asset you selected is already in that group. **Remove from group** in the same entry takes them back out, and the assets themselves are not deleted.

## Add a custom field to an asset type

A field added to an asset type appears on every asset of that type, including the ones that already exist. Every type starts with one built-in field, **Name**, which is required and can't be removed.

1. On the **Assets** page, open the tab of the asset type, then point at the table header and click <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit asset type" data-size="line"></picture> next to the type name.
2. Under **Custom fields**, click **Add field**.
3. Select a field type. The thirteen types are listed in [Assets](README.md#custom-field-types).
4. In the field's name box, labelled with the field type, enter a name for the field.
5. Select **Required** if an asset of this type can't be saved without it.
6. Select **Multiple selection** if the field should accept more than one value.
7. Click **Save**.

To change the order the fields appear in, click a field and drag it by <picture><source srcset="../../.gitbook/assets/icons/drag_handle-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/drag_handle.svg" alt="Drag to reorder" data-size="line"></picture>. To remove one, click it and click <picture><source srcset="../../.gitbook/assets/icons/delete-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/delete.svg" alt="Remove field" data-size="line"></picture>. A removed field and its values no longer appear on any asset of that type. Until you click **Save**, **Undo** in the message brings the field back.

## Tag an asset

Tags are a field type rather than a setting, so an asset can have tags only when its asset type has a field for them.

1. Add a field of the type **Tags** to the asset type, following the steps above.
2. Open an asset of that type.
3. In the tag field, select the tags you want, or enter a new one.
4. Click **Save**.

A tags field accepts several tags at once, so you don't need to turn on **Multiple selection** for it.

## Delete an asset

1. On the **Assets** page, open the tab of the asset type.
2. Click <picture><source srcset="../../.gitbook/assets/icons/delete_outline-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/delete_outline.svg" alt="Delete" data-size="line"></picture> at the end of the row.
3. Confirm.

A deleted asset can't be restored. Its device is released and can be assigned to another asset.
