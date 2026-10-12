---
description: >-
  How to find an asset on the map, follow it, check its readings, show a field
  in the asset list, view trip history, show trips on the map, and show geo
  objects.
tags:
  - how-to
  - beta
---

# Track assets

{% hint style="info" %}
**Access**

You see only the assets, devices, and geo objects that your role lets you see. To change an asset from Tracking, your role must also let you edit assets. See [Access to Tracking](README.md#access-to-tracking).
{% endhint %}

## Find an asset on the map

An asset appears on the map when it has a device and the device sent a position. Selecting an asset in the list moves the map toward it.

1. Click **Tracking** on the sidebar.
2. In the **Assets** panel, open the tab of the asset's type or stay on **All**.
3. To search by name, click <picture><source srcset="../.gitbook/assets/icons/search-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/search.svg" alt="Search" data-size="line"></picture> in the panel and enter part of the name.
4. Click the asset's entry in the list.

The map moves to the asset and circles its marker. Each asset you select after that moves the map again. Selecting all assets with the **Select all** checkbox doesn't move the map.

To see only the assets you selected, click <picture><source srcset="../.gitbook/assets/icons/more_vert-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/more_vert.svg" alt="Open map settings" data-size="line"></picture> in the panel header and select **Show only selected assets**.

## Follow an asset as it moves

Following keeps the map centered on one asset while it moves, so the asset stays in view. It is a map setting, and it stays on until you turn it off.

1. In the header of the **Assets** panel, click <picture><source srcset="../.gitbook/assets/icons/more_vert-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/more_vert.svg" alt="Open map settings" data-size="line"></picture>.
2. Select **Follow the selected asset**.
3. Select the asset's entry in the list, or click its marker on the map.

If the map already moved to an asset before you turned on the setting, the map centers on that asset.

Following stops when you drag the map or move it with the arrow keys, clear the asset, select a geo object, or show a trip or a stop from the trip history on the map. To follow the asset again, click its marker or select it again in the list.

## Check an asset's current readings

The details panel shows what each device of an asset reports: its connection status, motion status, position, and latest readings, such as ignition or fuel level.

1. In the **Assets** panel, move the mouse over the asset's entry.
2. Click <picture><source srcset="../.gitbook/assets/icons/info-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/info.svg" alt="Show details" data-size="line"></picture>, which appears in the entry.

You can also click the asset's marker on the map. To open the details panel every time you select an asset in the list, turn on **Show details on click** in the map settings.

If the asset has more than one device, the panel shows one section per device. The primary device's section is open, and you can expand the others.

Readings are listed under their code names, such as **hw_mileage** for the odometer or **din channel 1** for the first input. To learn what they mean, see [Asset details](README.md#asset-details).

## Show a custom field in the asset list

By default, each list entry shows the asset's groups and tags. You can also display any other asset field, such as a license plate or a device identifier, so you can see it without opening the details panel.

1. Open the asset's details panel by clicking <picture><source srcset="../.gitbook/assets/icons/info-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/info.svg" alt="Show details" data-size="line"></picture> on the list entry.
2. Click <picture><source srcset="../.gitbook/assets/icons/star_border-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/star_border.svg" alt="Show the field for this asset in the list" data-size="line"></picture> next to the field.

The field will appear in the asset's list entry. Click <picture><source srcset="../.gitbook/assets/icons/star-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/star.svg" alt="Hide the field for this asset in the list" data-size="line"></picture> again to remove it.

Fields are added for one asset at a time, and only in this browser. Connection status, motion status, and readings can't be added to the list.

## View trip history

Trip history lists the trips and stops of the assets you select, with times, places, distances, and durations. You can view it for one asset or for several at once.

1. In the **Assets** panel, select the assets. The **Trip history** button will appear at the bottom of the panel.
2. Click **Trip history**. The list will show today's trips.
3. To see another period, click the date range at the top of the panel.
4. In **Start Date**, enter the first day of the period.
5. In **End Date**, enter the last day of the period.
6. To see part of a day, set the times next to **Start Date** and **End Date**.

The list updates as soon as you change the period or the selection. The period goes back to today every time you open **Trip history**. If the assets made no trips or stops in the period, the panel says so.

## Show a trip on the map

A trip on the map shows the route the asset took, with a pin at the start and at the end. You can show one trip or compare several.

1. Open **Trip history** for the asset, as described in [View trip history](#view-trip-history).
2. Click the trip in the list. The map will show its track and move to fit it.
3. To add more trips, select the checkboxes next to them.
4. To color the tracks by speed, click <picture><source srcset="../.gitbook/assets/icons/palette-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/palette.svg" alt="Color tracks" data-size="line"></picture> in the panel header and select **By speed**.
5. Optional: Drag the ends of **Speed range** to set which speeds are green and which are red.

Clear a trip's checkbox to remove it from the map. To see where the asset stopped, click a stop in the list. The map clears when you change the period or the selected assets, and when you close the panel.

## Show geo objects on the map

Geo objects, such as depots, customer sites, and delivery zones, are hidden on the map until you select them. Displaying one helps you see whether an asset is inside a zone or near a site.

1. In the **Geo objects** panel, open the geo object type's tab or stay on **All**.
2. Select the geo objects you want to see.

The map will draw their shapes and move to show all of them. Clear a geo object to hide it again.
