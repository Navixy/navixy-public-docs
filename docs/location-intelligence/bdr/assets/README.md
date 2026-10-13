---
description: >-
  The Assets page: asset types and their tabs, the asset table, the asset type
  editor, custom fields, and asset groups.
tags:
  - reference
  - beta
---

# Assets

An asset is something your business tracks: a vehicle, a trailer, a person, a container. You assign a device to it, and the position and readings that device sends become the asset's.

Every asset is based on a template called an asset type. This type decides which fields the asset has. You can use the [preset types](./#preset-asset-types) every workspace starts with, build your own, or both. A Vehicle type has fields for a license plate and a fuel type, while a Cargo type has fields for a unit ID and a temperature range.

This page describes the **Assets** page, the asset type editor, and the asset groups panel. To learn how to work with assets, see [Create asset](create-asset.md) and [Manage assets](manage-assets.md).

{% hint style="info" %}
**Access**

Your role decides which assets, asset types, and asset groups you see on this page, and whether you can create, edit, or delete them. See [Access to Business Data Repository](../README.md#access-to-business-data-repository).
{% endhint %}

## Assets page

**Assets** is where you view, add, edit, and delete the assets of one type at a time, and see how they're grouped. To open it, click **Assets** on the sidebar.

<figure><img src="../../.gitbook/assets/image (4).png" alt="The Assets page with one asset type open. Tabs for the asset types run along the top, next to a Create asset type button. Below them, a table lists the assets of the open type, one row per asset, with a column for each field, such as the assigned device. The table header has a filter icon, a settings icon, a double-arrow button, and a Create asset button. A pagination bar closes the table. Next to the table, a panel lists the asset groups under a Create asset group entry. Each group entry shows how many assets of each type it has."><figcaption><p>The Assets page with one asset type open</p></figcaption></figure>

Here's what you can do:

* Switch between asset types: Each tab shows one asset type and lists all its assets. Click **Create asset type** to define a new one. <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit asset type" data-size="line"></picture> next to the type name in the table header opens the asset type editor, so you can rename the type or change its fields. It appears when you point at the header.
* Find and add assets: Filter the table with <picture><source srcset="../../.gitbook/assets/icons/filter_alt-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/filter_alt.svg" alt="Show filters" data-size="line"></picture> and choose its columns with <picture><source srcset="../../.gitbook/assets/icons/settings-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/settings.svg" alt="Select columns" data-size="line"></picture>, both in the table header. Click **Create asset** to add a new asset to the active tab.
* Manage assets: Select assets with the checkboxes, open the asset editor with <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture>, or remove an asset with <picture><source srcset="../../.gitbook/assets/icons/delete_outline-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/delete_outline.svg" alt="Delete" data-size="line"></picture>. Deleting an asset is permanent: you can't restore it.
* Browse asset groups: The side panel shows your workspace's asset groups, and how many assets of each type each group has. Click **Show more** to see the assets themselves. Hide or show the panel with the double-arrow button in the table header, or drag its edge to resize it.

## Preset asset types

Every new workspace starts with three preset asset types: **Vehicle**, **Workforce**, and **Cargo**. Navixy creates them together with the workspace, so they are there the first time you open **Assets**. A workspace created before Navixy added the preset types doesn't have them, and you can build the same types yourself. Each one is an ordinary asset type in your workspace. You can rename it and add or remove properties (called [custom fields](./#custom-fields) in Navixy). The **Assets** page has no way to delete an asset type.

Every field is optional except the one that describes the asset: **Vehicle kind** on Vehicle and **Cargo unit** on Cargo. Workforce has no required fields. Every type has a **Device** field for its assigned GPS device.

Two fields let you assign an asset of one preset type to an asset of another. On a vehicle, **Driver** is where you assign a Workforce asset (the person driving the vehicle). On a cargo unit, **Transport unit** is where you assign a Vehicle asset (the transport carrying the cargo). These are fields of the type [**Reference**](./#custom-field-types). Navixy always creates both, because it creates the three types together.

A driver key that a device reads, such as an iButton, an RFID card, or a tachograph card, doesn't fill **Driver**. The device reports the key as a reading, **hardware_key** or **driver_card**, and Tracking shows it as text in the asset's details. Navixy doesn't match the key to a Workforce asset, so you assign the driver yourself.

<details>

<summary>Vehicle fields</summary>

For cars, vans, trucks, trailers, buses, and equipment.

<table><thead><tr><th width="235">Field</th><th>Contains</th></tr></thead><tbody><tr><td><strong>Vehicle kind</strong></td><td>Light-duty vehicle, Van, Truck, Trailer, Bus, Equipment, or Other. Required.</td></tr><tr><td><strong>Device</strong></td><td>The GPS device of the vehicle.</td></tr><tr><td><strong>License plate</strong></td><td>The registration plate, up to 20 characters.</td></tr><tr><td><strong>VIN</strong></td><td>The vehicle identification number, up to 17 characters.</td></tr><tr><td><strong>Manufacturer</strong></td><td>The company that built the vehicle.</td></tr><tr><td><strong>Model</strong></td><td>The model name.</td></tr><tr><td><strong>Model year</strong></td><td>The model year, from 1900 to 2100.</td></tr><tr><td><strong>Fuel type</strong></td><td>Diesel, Gasoline, Electric, Hybrid, or Other. Optional, so leave it empty for a trailer.</td></tr><tr><td><strong>Driver</strong></td><td>The person driving the vehicle now, a Workforce asset.</td></tr></tbody></table>

</details>

<details>

<summary>Workforce fields</summary>

For the people you track.

<table><thead><tr><th width="237">Field</th><th>Contains</th></tr></thead><tbody><tr><td><strong>Engagement</strong></td><td>Employee, Contractor, or Visitor.</td></tr><tr><td><strong>Device</strong></td><td>The badge, phone, or personal GPS device the person carries.</td></tr><tr><td><strong>Job title</strong></td><td>The person's role.</td></tr><tr><td><strong>Personnel ID</strong></td><td>An employee, contractor, or badge number.</td></tr><tr><td><strong>Phone</strong></td><td>A phone number for dispatch and safety alerts.</td></tr><tr><td><strong>Email</strong></td><td>An email address for notifications and reports.</td></tr></tbody></table>

</details>

<details>

<summary>Cargo fields</summary>

For containers, pallets, parcels, and returnable boxes. A Cargo asset is one physical unit that stays the same from one shipment to the next. It stores nothing that changes per shipment, such as contents or destination.

<table><thead><tr><th width="244">Field</th><th>Contains</th></tr></thead><tbody><tr><td><strong>Cargo unit</strong></td><td>Container, Pallet, Parcel, Returnable box, or Other. Required.</td></tr><tr><td><strong>Device</strong></td><td>The sensor tag or data logger attached to the unit.</td></tr><tr><td><strong>Unit ID</strong></td><td>The unit's own identifier, such as a container number, serial number, or asset tag.</td></tr><tr><td><strong>Rated min temperature, °C</strong></td><td>The lowest temperature the unit is rated for.</td></tr><tr><td><strong>Rated max temperature, °C</strong></td><td>The highest temperature the unit is rated for.</td></tr><tr><td><strong>Transport unit</strong></td><td>The vehicle carrying the unit now, a Vehicle asset.</td></tr></tbody></table>

</details>

For the steps, including building your own type instead, see [Create asset](create-asset.md).

## Asset type editor

The editor opens in a side panel, titled **Create asset type** when you add one and **Edit asset type** when you open one.

<table><thead><tr><th width="179">Element</th><th width="352">Description</th><th>When you use it</th></tr></thead><tbody><tr><td><strong>Name</strong> (asset type)</td><td>The name of the asset type. Required.</td><td>Always.</td></tr><tr><td><strong>Custom fields</strong></td><td>The fields every asset of this type has. The first one, <strong>Name</strong>, is built-in, required, and can't be removed.</td><td>Always.</td></tr><tr><td><strong>Add field</strong></td><td>Adds a field. You select its type from a list described in <a href="./#custom-field-types">Custom field types</a>.</td><td>Whenever the type needs more than a name.</td></tr><tr><td><strong>Field name</strong></td><td>What the field is called in the asset editor and in the table column. The box is labelled with the field type and filled in with its name, such as <strong>Device</strong>, until you replace it.</td><td>Always, for each field you add.</td></tr><tr><td><strong>Description</strong></td><td>A hint shown under the field in the asset editor.</td><td>When the field needs an explanation for the people who fill it in.</td></tr><tr><td><strong>Required</strong></td><td>Makes the field compulsory, so an asset of this type can't be saved without it.</td><td>For the fields that make the record worth having, such as a registration plate.</td></tr><tr><td><strong>Multiple selection</strong></td><td>Lets the field accept more than one value. Offered on <strong>Dropdown</strong>, <strong>Tags</strong>, <strong>Schedule</strong>, and <strong>Reference</strong> fields. It is on by default for <strong>Tags</strong>: with it off, a tags field accepts one existing tag and can't create new ones.</td><td>For a dropdown with several answers, or several assigned records.</td></tr></tbody></table>

When you add, remove, or reorder a field, a short message confirms it, with an **Undo** button. Nothing changes until you click **Save**. <picture><source srcset="../../.gitbook/assets/icons/drag_handle-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/drag_handle.svg" alt="Drag to reorder" data-size="line"></picture> to drag a field and <picture><source srcset="../../.gitbook/assets/icons/delete-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/delete.svg" alt="Remove field" data-size="line"></picture> to delete it appear when you click the field.

## Custom fields

A custom field is a property, a piece of your own data on an asset such as a VIN, a fuel type, or an inspection date. You can add custom fields to an asset type, but not to a single asset. Every asset of this type has the same fields, and you fill in their values for each asset.

Each custom field has a name and a field type. The field type decides what the field accepts, such as a number, a date, or a device. You choose the field type when you add the field to the type. You can't change it later. An asset type can have up to 50 custom fields. A field you remove still counts towards a second limit of 100, because Navixy keeps the field and its values hidden rather than deleting them.

When you add a field to a type that already has assets, the field stays empty on each of them until someone fills it in. When you remove a field, it disappears from every asset of that type.

## Custom field types

Asset fields have the following types:

<details>

<summary>Custom field types</summary>

<table><thead><tr><th width="233">Type</th><th>Accepts</th></tr></thead><tbody><tr><td><strong>Text</strong></td><td>A short line of text.</td></tr><tr><td><strong>Text area</strong></td><td>Several lines of text.</td></tr><tr><td><strong>Decimal</strong></td><td>A number with decimal places.</td></tr><tr><td><strong>Integer</strong></td><td>A whole number.</td></tr><tr><td><strong>Checkbox</strong></td><td>A yes or no answer.</td></tr><tr><td><strong>Geo data</strong></td><td>A shape on the map, such as a geofence.</td></tr><tr><td><strong>Dropdown</strong></td><td>One or more options from a list you define.</td></tr><tr><td><strong>Tags</strong></td><td>Tags from your workspace.</td></tr><tr><td><strong>Date</strong></td><td>A date without a time.</td></tr><tr><td><strong>Date and time</strong></td><td>A date with a time and timezone.</td></tr><tr><td><strong>Device</strong></td><td>A GPS device from your workspace.</td></tr><tr><td><strong>Schedule</strong></td><td>A schedule from your workspace. See <a href="../schedules/">Schedules</a>.</td></tr><tr><td><strong>Reference</strong></td><td>A link to another record, such as an asset.</td></tr></tbody></table>

Two of them ask for more once you select them.

* **Dropdown** requires options. A new dropdown starts with **Option 1** and **Option 2**, which you rename. **Add option** adds another one. You can drag options by <picture><source srcset="../../.gitbook/assets/icons/drag_indicator-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/drag_indicator.svg" alt="Drag to reorder" data-size="line"></picture> to reorder them, remove an option with <picture><source srcset="../../.gitbook/assets/icons/remove_circle_outline-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/remove_circle_outline.svg" alt="Remove option" data-size="line"></picture> while more than one is left, and use <picture><source srcset="../../.gitbook/assets/icons/check_circle_outline-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/check_circle_outline.svg" alt="Set as default" data-size="line"></picture> to make one the default.
* **Reference** requires a **Refers to** target, which starts as **Asset** and can't be changed after you save the type. **Records** are assets and geo objects. **Types and lists** are inventories, asset types, asset group types, geo object types, device types, device statuses, device models, device vendors, and countries. Selecting assets or geo objects also lets you narrow the target to certain types, and leaving the field empty means any type.

</details>

## Asset editor

**Create asset** and <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> in a row open the same side panel, empty for a new asset and filled in for an existing one. The panel is titled with the name of the asset type: an asset of the Vehicle type opens a panel titled Vehicle. It shows the built-in **Name** field and then every custom field of that type, in the order set by the type.

A **Device** field has an extra setting. **Primary device** indicates which device's position becomes the asset's position when the asset has more than one device. If you select a device that already belongs to another asset, Navixy asks right away whether to reassign it. Confirming moves the device rather than sharing it.

## Asset groups

An asset group is a set of assets you want to handle together. A group is not an asset type: an asset has exactly one type and can belong to any number of groups.

| Element                                                                                                                                                                                                              | Description                                                                                                                                                                                                                                                                                                                                                                                            |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Create asset group**                                                                                                                                                                                               | The first entry of the panel. Its <picture><source srcset="../../.gitbook/assets/icons/add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/add.svg" alt="Create asset group" data-size="line"></picture> opens the form for a new group. When you select assets in the table, it becomes **Add to group** and opens the form with those assets already in it.     |
| <picture><source srcset="../../.gitbook/assets/icons/add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/add.svg" alt="Add assets" data-size="line"></picture> on a group entry | Opens the group, where you choose its assets. When you select assets in the table, it becomes **Add to group**.                                                                                                                                                                                                                                                                                        |
| **Add to group**                                                                                                                                                                                                     | Puts the assets you selected in the table into a group. It doesn't appear when every selected asset is already in the group: <picture><source srcset="../../.gitbook/assets/icons/add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/add.svg" alt="Add assets" data-size="line"></picture> stays, and its tooltip says that the assets are already in the group. |
| **Remove from group**                                                                                                                                                                                                | Takes the assets you selected on a group entry out of the group. It appears as <picture><source srcset="../../.gitbook/assets/icons/delete-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/delete.svg" alt="Remove from group" data-size="line"></picture> once you select them.                                                                                  |
| **Show more**, **Show less**                                                                                                                                                                                         | Lists the assets of a group entry or folds them away.                                                                                                                                                                                                                                                                                                                                                  |

Each group entry also has <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit asset group" data-size="line"></picture> for its name, color, and assets, <picture><source srcset="../../.gitbook/assets/icons/delete-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/delete.svg" alt="Delete asset group" data-size="line"></picture> to delete it, and <picture><source srcset="../../.gitbook/assets/icons/drag_indicator-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/drag_indicator.svg" alt="Drag to reorder" data-size="line"></picture> to drag it to another place in the panel. Navixy remembers the order of the groups in this browser only.

Deleting a group requires confirmation and can't be undone. The assets themselves aren't deleted with it.
