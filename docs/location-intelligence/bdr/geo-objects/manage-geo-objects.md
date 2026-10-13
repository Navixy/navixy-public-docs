---
description: >-
  How to enter a geo object's shape as GeoJSON, change a shape, add a custom
  field to a geo object type, tag a geo object, and assign a geo object to an
  asset.
tags:
  - how-to
  - beta
---

# Manage geo objects

{% hint style="info" %}
**Access**

Each task on this page needs a role that allows it. For example, to change the shape of a geo object, your role must let you view and edit geo objects. See [Access to Business Data Repository](../README.md#access-to-business-data-repository).
{% endhint %}

## Enter a shape as GeoJSON

Use this when the shape already exists as map data, for example, exported from another mapping tool. The shape editor accepts GeoJSON text instead of a drawing. Write coordinates longitude first, then latitude.

1. On the **Geo objects** page, open the tab of the geo object type, then open the geo object with <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture>, or click **Create geo object**.
2. Click **Geo data**. The shape editor opens over the page.
3. Click <picture><source srcset="../../.gitbook/assets/icons/code-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/code.svg" alt="Show JSON" data-size="line"></picture>. A panel headed **JSON** opens.
4. Paste or enter the GeoJSON. The map shows the shape as you enter it. While the text isn't valid JSON or isn't a shape the map can draw, an error shows under the text, and **Apply** stays unavailable.
5. Click **Apply**.
6. Click **Save**.

The panel accepts the seven GeoJSON shape types listed in [Geo objects](README.md#shape-editor).

## Change the shape of a geo object

You can redraw a shape at any time. To open the shape editor without opening the geo object, double-click its cell in the **Geo data** column. The shape is saved when you click **Apply**.

1. On the **Geo objects** page, open the tab of the geo object type, then open the geo object with <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture>.
2. Click **Geo data**. The shape editor opens over the page.
3. To remove a shape, select it on the map. <picture><source srcset="../../.gitbook/assets/icons/delete-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/delete.svg" alt="Delete shape" data-size="line"></picture> appears. Click it.
4. To add a shape, select a drawing tool and draw it. All the shapes you draw are saved as one geo object.
5. Click **Apply**.
6. Click **Save**.

## Add a custom field to a geo object type

A field added to a geo object type appears on every geo object of that type, including the ones that already exist. Every type starts with two built-in fields, **Title** and **Geo data**, which are required and can't be removed.

1. On the **Geo objects** page, open the tab of the geo object type, point at the table header, and click <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit geo object type" data-size="line"></picture> next to the type name.
2. Under **Custom fields**, click **Add field**.
3. Select a [field type](README.md#custom-field-types).
4. In the field's name box, labelled with the field type, enter a name for the field.
5. Select **Required** if a geo object of this type can't be saved without it.
6. Click **Save**.

To change the order the fields appear in, click a field and drag it by <picture><source srcset="../../.gitbook/assets/icons/drag_handle-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/drag_handle.svg" alt="Drag to reorder" data-size="line"></picture>. To remove one, click it and click <picture><source srcset="../../.gitbook/assets/icons/delete-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/delete.svg" alt="Remove field" data-size="line"></picture>. A removed field and its values no longer appear on any geo object of that type. Until you click **Save**, **Undo** in the message brings the field back.

## Tag a geo object

Tags are a [field type](README.md#custom-field-types) rather than a setting, so a geo object can have tags only when its type has a field for them.

1. Add a field of the type **Tags** to the geo object type, following the steps above.
2. Open a geo object of that type.
3. In the tag field, select the tags you want, or enter a new one.
4. Click **Save**.

A tags field accepts several tags at once, because **Multiple selection** is on by default for it.

## Assign a geo object to an asset

You assign a geo object to an asset through a [**Reference** field](README.md#custom-field-types) in the asset's type. If the asset type has no such field yet, follow steps 1 to 6 to add it. The field will then appear on every asset of that type, empty until someone fills it in.

1. On the **Assets** page, open the tab of the asset type, point at the table header, and click <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit asset type" data-size="line"></picture> next to the type name.
2. Under **Custom fields**, click **Add field**.
3. Select **Reference**.
4. In the field's name box, labelled **Reference**, enter a name, for example, Home depot.
5. In **Refers to**, select **Geo object**. Optional: in **Geo object types**, select the types the field offers. Left empty, it offers geo objects of any type. You can't change **Refers to** after you save the type.
6. Click **Save**.
7. Open an asset of that type with <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture>.
8. In the field, select the geo object. To create one without leaving the asset, use <picture><source srcset="../../.gitbook/assets/icons/add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/add.svg" alt="Create" data-size="line"></picture> in the field.
9. Click **Save**.

A geo object doesn't show which assets it's assigned to. You can't delete a geo object while an asset has it in a field, even in a field that was removed from the asset's type. Clear the field on those assets or select another geo object in it, and then delete the geo object.
