---
description: >-
  The Geo objects page: Geo object types and their tabs, the geo object table,
  the map panel, the geo object type editor, and the shape editor.
tags:
  - reference
  - beta
---

# Geo objects

A geo object is a place or an area that matters to your business, drawn on the map: a depot, a customer's site, a delivery zone, a road your vehicles use. It has a title and a shape, and you can assign it to your assets.

Every geo object is based on a geo object type, such as Depot or Delivery zone. The type decides which fields its geo objects have, the same way an asset type does for assets. It doesn't decide the shape: a geo object of any type can be a point, a line, or an area. A new workspace has no geo object types, so you create the ones you need.

Shapes are stored in GeoJSON, the standard format for map data. You can draw a shape on the map or enter it as GeoJSON text.

This page describes the **Geo objects** page, the geo object type editor, and the shape editor. To learn how to work with geo objects, see [Create geo object](create-geo-object.md) and [Manage geo objects](manage-geo-objects.md).

{% hint style="info" %}
**Access**

Your role decides which geo objects and geo object types you see on this page, and whether you can create, edit, or delete them. See [Access to Business Data Repository](../README.md#access-to-business-data-repository).
{% endhint %}

## Geo objects page

**Geo objects** is where you view, add, edit, and delete the geo objects of one type at a time, and see their shapes on the map. To open it, click **Geo objects** on the sidebar.

<figure><img src="../../.gitbook/assets/image (5).png" alt="The Geo objects page with one geo object type open. Tabs for the geo object types run along the top, next to a Create geo object type button. Below them, a table lists the geo objects of the open type, one row per geo object, with a checkbox and a Title column. The table header has a filter icon, a settings icon, a double-arrow button, and a Create geo object button. A bar under the table shows how many geo objects are selected and the pagination controls. Next to the table, a map panel shows the shape of the geo object selected in the table, with zoom buttons and a compass in the corner."><figcaption><p>The Geo objects page with one geo object type open</p></figcaption></figure>

Here's what you can do:

* Switch between geo object types: Each tab shows one geo object type and lists all its geo objects. Click **Create geo object type** to define a new one. <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit geo object type" data-size="line"></picture> next to the type name in the table header opens the geo object type editor, so you can rename the type or change its fields. It appears when you point at the header.
* Find and add geo objects: Filter the table and choose its columns from the toolbar. Click **Create geo object** to add a new geo object to the active tab.
* Manage geo objects: Open the geo object editor with <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> or remove a geo object with <picture><source srcset="../../.gitbook/assets/icons/delete_outline-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/delete_outline.svg" alt="Delete" data-size="line"></picture>. The **Geo data** column says that a geo object has a shape, but doesn't show it: to see or change the shape, open the geo object and click **Geo data**, or double-click the cell. Both open the [shape editor](./#shape-editor). Deleting a geo object asks for confirmation and is permanent: you can't restore it, and the assets it was assigned to lose it without a warning.
* View shapes on the map: The map panel shows the shapes of the geo objects you select in the table, including the shapes in their **Geo data** custom fields, and zooms to fit them. Hide or show it with the button in the table header, and drag its edge to resize it. When it takes half the page or more, the table shows only the **Title** column. Navixy remembers whether the panel is shown, and its width, in this browser.

## Geo object type editor

The editor opens in a side panel, titled **Create geo object type** when you add one and **Edit geo object type** when you open one.

<table><thead><tr><th width="157">Element</th><th width="380">Description</th><th>When you use it</th></tr></thead><tbody><tr><td><strong>Name</strong> (type)</td><td>The name of the geo object type. Required.</td><td>Always.</td></tr><tr><td><strong>Custom fields</strong></td><td>The fields every geo object of this type has. The first two, <strong>Title</strong> and <strong>Geo data</strong>, are built in, required, and can't be removed.</td><td>Always.</td></tr><tr><td><strong>Add field</strong></td><td>Adds a field. You select its type from a list described in <a href="./#custom-field-types">Custom field types</a>.</td><td>Whenever the type needs more than a title and a shape.</td></tr><tr><td><strong>Field name</strong></td><td>What the field is called in the geo object editor and in the table column. The box is labelled with the field type and filled in with its name, such as <strong>Text</strong>, until you replace it.</td><td>Always, for each field you add.</td></tr><tr><td><strong>Required</strong></td><td>Makes the field compulsory, so a geo object of this type can't be saved without it.</td><td>For the fields that make the record worth having, such as a site phone.</td></tr><tr><td><strong>Multiple selection</strong></td><td>Lets the field accept more than one value. Offered on <strong>Dropdown</strong>, <strong>Tags</strong>, <strong>Schedule</strong>, and <strong>Reference</strong> fields. It is on by default for <strong>Tags</strong>: with it off, a tags field accepts one existing tag and can't create new ones.</td><td>For a dropdown with several answers, or several assigned records.</td></tr></tbody></table>

When you add, remove, or reorder a field, a short message confirms it, with an **Undo** button. Nothing changes until you click **Save**. You can't delete a geo object type at the moment.

## Custom fields

A custom field is a property, a piece of your own data on a geo object, such as the opening hours of a depot, a contact phone for a customer's site, or the delivery fee for a zone. You add custom fields to a geo object type, not to a single geo object. Every geo object of this type has the same fields, and you fill in their values for each geo object.

Each custom field has a name and a field type. The field type decides what the field accepts, such as a number, a date, or a schedule. You choose the field type when you add the field to the type. You can't change it later. A geo object type can have up to 50 custom fields. A field you remove still counts towards a second limit of 100, because Navixy keeps the field and its values hidden rather than deleting them.

When you add a field to a type that already has geo objects, the field stays empty on each of them until someone fills it in. When you remove a field, it disappears from every geo object of that type.

## Custom field types

Geo object fields have the following types:

<details>

<summary>Custom field types</summary>

<table><thead><tr><th width="232">Type</th><th>Accepts</th></tr></thead><tbody><tr><td><strong>Text</strong></td><td>A short line of text.</td></tr><tr><td><strong>Text area</strong></td><td>Several lines of text.</td></tr><tr><td><strong>Decimal</strong></td><td>A number with decimal places.</td></tr><tr><td><strong>Integer</strong></td><td>A whole number.</td></tr><tr><td><strong>Checkbox</strong></td><td>A yes or no answer.</td></tr><tr><td><strong>Geo data</strong></td><td>A shape on the map, separate from the geo object's own shape.</td></tr><tr><td><strong>Dropdown</strong></td><td>One or more options from a list you define.</td></tr><tr><td><strong>Tags</strong></td><td>Tags from your workspace.</td></tr><tr><td><strong>Date</strong></td><td>A date without a time.</td></tr><tr><td><strong>Date and time</strong></td><td>A date with a time and timezone.</td></tr><tr><td><strong>Schedule</strong></td><td>A schedule from your workspace. See <a href="../schedules/">Schedules</a>.</td></tr><tr><td><strong>Reference</strong></td><td>A link to another record, such as an asset.</td></tr></tbody></table>

Two of them ask for more once you select them.

* **Dropdown** requires options. A new dropdown starts with **Option 1** and **Option 2**, which you rename. **Add option** adds another one. You can drag options by <picture><source srcset="../../.gitbook/assets/icons/drag_indicator-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/drag_indicator.svg" alt="Drag to reorder" data-size="line"></picture> to reorder them, remove an option with <picture><source srcset="../../.gitbook/assets/icons/remove_circle_outline-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/remove_circle_outline.svg" alt="Remove option" data-size="line"></picture> while more than one is left, and use <picture><source srcset="../../.gitbook/assets/icons/check_circle_outline-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/check_circle_outline.svg" alt="Set as default" data-size="line"></picture> to make one the default.
* **Reference** requires a **Refers to** target, which starts as **Asset** and can't be changed after you save the type. **Records** are assets and geo objects. **Types and lists** are inventories, asset types, asset group types, geo object types, device types, device statuses, device models, device vendors, and countries. Selecting assets or geo objects also lets you narrow the target to certain types, and leaving that empty means any type.

</details>

## Geo object editor

**Create geo object** and <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> in a row open the same side panel, empty for a new geo object and filled in for an existing one. The panel is titled with the name of the geo object type: a geo object of the Depot type opens a panel titled Depot. It shows **Title**, then **Geo data**, then every custom field of that type, in the order set by the type.

**Geo data** opens the [shape editor](./#shape-editor). A geo object can't be saved without a shape.

## Shape editor

The shape editor opens over the whole page, with a map and the name of the field you are editing. It sets the shape of a geo object and the value of any **Geo data** custom field.

<table><thead><tr><th width="232">Element</th><th>Description</th></tr></thead><tbody><tr><td>Drawing tools</td><td>Four icons: <picture><source srcset="../../.gitbook/assets/icons/fmd_good-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/fmd_good.svg" alt="Draw point" data-size="line"></picture>, <picture><source srcset="../../.gitbook/assets/icons/show_chart-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/show_chart.svg" alt="Draw line" data-size="line"></picture>, <picture><source srcset="../../.gitbook/assets/icons/pentagon-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/pentagon.svg" alt="Draw polygon" data-size="line"></picture>, and <picture><source srcset="../../.gitbook/assets/icons/radio_button_unchecked-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/radio_button_unchecked.svg" alt="Draw circle" data-size="line"></picture>. Each shape you draw is added to the others, and all of them are saved as one geo object.</td></tr><tr><td><picture><source srcset="../../.gitbook/assets/icons/delete-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/delete.svg" alt="Delete shape" data-size="line"></picture></td><td>Removes the shape you selected on the map.</td></tr><tr><td><picture><source srcset="../../.gitbook/assets/icons/code-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/code.svg" alt="Show JSON" data-size="line"></picture></td><td>Opens a panel headed <strong>JSON</strong>, with the shape as GeoJSON text. You can enter or paste GeoJSON there instead of drawing, and the map shows the shape as you enter it. While the text isn't valid JSON or isn't a shape the map can draw, an error shows under the text, and <strong>Apply</strong> is unavailable.</td></tr><tr><td><strong>Apply</strong></td><td>Keeps the shapes and returns to the geo object editor. The shape is saved when you save the geo object.</td></tr><tr><td><strong>Cancel</strong></td><td>Closes the editor without changing the shape.</td></tr></tbody></table>

Draw the shape that matches what the geo object stands for. A point marks a spot and has no size. A line shows a path and has no width. An area covers ground, such as a depot yard or a delivery zone. Only an area has an inside, so draw a zone as an area. To give a route a width, draw it as a narrow area along the road.

In GeoJSON text, coordinates go longitude first, then latitude: the Brandenburg Gate in Berlin is `[13.377704, 52.516275]`. The text accepts seven shape types: `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`, and `GeometryCollection`. It also accepts a `Feature` or a `FeatureCollection` and keeps only its shapes. A circle drawn on the map is saved as an area.

## Geo objects on assets

You assign a geo object to an asset through a field of the type **Reference** on the asset's type, with **Refers to** set to **Geo object**. **Geo object types** narrows the list to certain types, such as Depot. On the asset, that field lists the geo objects you can select. One geo object can be assigned to any number of assets.

<picture><source srcset="../../.gitbook/assets/icons/add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/add.svg" alt="Create" data-size="line"></picture> in the field makes a new geo object without leaving the asset, and clicking an assigned geo object's name opens a view of its fields, with an **Edit** button. If the field allows several geo object types, the create icon first asks which type to create. For the steps, see [Assign a geo object to an asset](manage-geo-objects.md#assign-a-geo-object-to-an-asset).

A geo object doesn't show which assets it's assigned to. If you delete a geo object that is assigned to assets, Navixy doesn't warn you, and the field on those assets shows empty. An asset like this can't be saved again until you clear the field or select another geo object in it.
