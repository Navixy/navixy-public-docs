---
description: >-
  How to create a geo object type, draw a geo
  object on the map, and assign it to an asset.
tags:
  - guide
  - beta
---

# Create geo object

Follow this guide to create your first geo object: a depot drawn as an area on the map, and assigned to a vehicle based there.

This guide builds a Depot type with one extra field, an address, then draws one depot and assigns it to a vehicle. A new workspace has no geo object types, so the first geo object always starts with its type.

Before you start, you need an asset to assign the depot to. To create one, see [Create asset](../assets/create-asset.md). You also need a role that lets you create types and geo objects and edit assets. See [Access to Business Data Repository](../README.md#access-to-business-data-repository).

## 1. Create the geo object type

A geo object type decides which properties (called [custom fields](README.md#custom-fields)) its geo objects have. Every type starts with two built-in fields, **Title** and **Geo data**, the geo object's name and its shape. Both have to be filled in. The fields you add come after them.

To create the type, follow these steps:

1. Click **Geo objects** on the sidebar.
2. Click **Create geo object type**. A panel titled **Create geo object type** opens.
3. Enter a **Name** for the type, for example, Depot.
4. Under **Custom fields**, click **Add field**.
5. Select **Text**.
6. In the field's name box, labelled **Text**, enter a name, for example, Address.
7. Add any other field you want. All field types are described on the [Geo objects](README.md#custom-field-types) page.
8. Click **Save**.

Depot appears as a tab on the **Geo objects** page.

## 2. Draw the geo object

A depot is an area, so draw it as one. A point would mark only a spot, and only an area has an inside that a vehicle can be in.

To draw the depot, follow these steps:

1. On the **Geo objects** page, click the **Depot** tab.
2. Click **Create geo object**. A panel titled **Depot** opens.
3. Enter a **Title**, for example, "North depot."
4. Enter the **Address**.
5. Click **Geo data**. The shape editor opens over the page.
6. Find the depot on the map.
7. Select the area tool, then click each corner of the depot yard on the map. Click the first corner again to close the area.
8. Click **Apply**.
9. Click **Save**.

North depot appears as a row in the **Depot** tab. Select its row to see its shape in the map panel.

## 3. Assign the geo object to an asset

You assign a geo object to an asset through a **Reference** field on the asset's type. This section adds one to the Vehicle type, then selects the depot on a vehicle. Skip steps 1 to 7 if the asset type already has such a field.

To assign the depot to a vehicle, follow these steps:

1. Click **Assets** on the sidebar.
2. Click the **Vehicle** tab, point at the table header, and click <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit asset type" data-size="line"></picture> next to the type name. A panel titled **Edit asset type** opens.
3. Under **Custom fields**, click **Add field**.
4. Select **Reference**.
5. In the field's name box, labelled **Reference**, enter a name, for example, Home depot.
6. In **Refers to**, select **Geo object**, then in **Geo object types**, select **Depot**. You can't change **Refers to** after you save the type.
7. Click **Save**.
8. Open the vehicle editor.
9. In the **Home depot** field, select **North depot**.
10. Click **Save**.

{% hint style="success" %}
You created a Depot geo object type, drew "North depot" as an area on the map, and assigned it to a vehicle. Every vehicle now has a **Home depot** field, and every depot you create has an **Address** field.
{% endhint %}

## Next steps

- [Manage geo objects](manage-geo-objects.md): Enter a shape as GeoJSON, change a shape, add a field later, and tag a geo object.
- [Geo objects](README.md): Learn how geo objects, their shapes, and their custom fields work, including every custom field type.
