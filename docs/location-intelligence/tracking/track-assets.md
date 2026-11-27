---
description: >-
  How to find and follow an asset, check its readings, view and replay trips
  with charts, show geo objects, and go to a notification's position.
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

An asset appears on the map when it has a device and the device sent a position. Clicking an asset in the list moves the map to it.

1. Click **Tracking** on the sidebar.
2. If the **Assets** panel is collapsed, click it.
3. Open the tab of the asset's type or stay on **All**.
4. To search by name, enter part of the name in the search field.
5. Click the asset's entry in the list.

The map moves to the asset, and a small window with the asset's name and status opens next to its marker. The asset's marker is highlighted, and the other markers become paler until you close the window.

To see only some assets on the map, select their checkboxes in the list. Then click <picture><source srcset="../.gitbook/assets/icons/layers-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/layers.svg" alt="Open map settings" data-size="line"></picture> on the map toolbar and select **Show only selected assets**.

## Follow an asset as it moves

Following keeps the map centered on one asset while it moves, so the asset stays in view. It is a map setting, and it stays on until you turn it off.

1. On the map toolbar, click <picture><source srcset="../.gitbook/assets/icons/layers-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/layers.svg" alt="Open map settings" data-size="line"></picture>.
2. Select **Follow the selected asset**.
3. Click the asset's entry in the list, or click its marker on the map.

If the map already moved to an asset before you turned on the setting, the map centers on that asset.

Following stops when you drag the map or move it with the arrow keys. It also stops when you select a geo object or show trips from the trip history. To follow the asset again, click its marker or its entry in the list.

## Check an asset's current readings

The details panel shows what each device of an asset reports: its connection status, motion status, position, and latest readings, such as ignition or fuel level.

1. In the **Assets** panel, find the asset's entry.
2. Click <picture><source srcset="../.gitbook/assets/icons/info-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/info.svg" alt="Show details" data-size="line"></picture> in the entry.

You can also click the asset's marker on the map and then click **Show details**.

If the asset has more than one device, the panel has one section for each device.

Readings are listed under their titles. A reading that has no title is listed under its code name, such as **hw_mileage** for the odometer or **din channel 1** for the first input. To learn what they mean, see [Asset details](README.md#asset-details).

## Show a custom field in the asset list

By default, each list entry shows the asset's groups and tags. You can also display any other asset field, such as a license plate or a device identifier, so you can see it without opening the details panel.

1. Open the asset's details panel by clicking <picture><source srcset="../.gitbook/assets/icons/info-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/info.svg" alt="Show details" data-size="line"></picture> on the list entry.
2. Click <picture><source srcset="../.gitbook/assets/icons/star-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/star.svg" alt="Show the field for this asset in the list" data-size="line"></picture> next to the field.

The field will appear in the asset's list entry. Click <picture><source srcset="../.gitbook/assets/icons/star-filled-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/star-filled.svg" alt="Hide the field for this asset in the list" data-size="line"></picture> again to remove it.

Fields are added for one asset at a time, and only in this browser. Connection status, motion status, and readings can't be added to the list.

## View trip history

Trip history lists an asset's trips and stops for a period, with times, distances, durations, and speeds. For example, you can check when a courier started the day, or how long a truck stood at a customer's site.

1. In the **Assets** panel, select the asset. The **Trip history** button will appear at the bottom of the panel.
2. Click **Trip history**. The panel will list today's trips and show their tracks on the map.
3. To see another period, click **Yesterday** or **Last 7 days**.
4. To set a period of your own, click <picture><source srcset="../.gitbook/assets/icons/calendar_month-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/calendar_month.svg" alt="Choose a custom period" data-size="line"></picture>.
5. In the calendar, click the first day of the period, then the last day.
6. Optional: To see part of a day, set the times next to **Start** and **End**.
7. Click **Apply**.

The period goes back to today every time you open **Trip history**, and the longest period is 31 days. If you selected several assets, the panel shows one asset at a time. To switch to another one, click the asset's name at the top of the panel. If the asset made no trips in the period, the panel says so.

## Show a trip on the map

The map shows the tracks of all the period's trips when **Trip history** opens. You can look at one trip, hide the trips that you don't need, and color the tracks by speed.

1. Open **Trip history** for the asset, as described in [View trip history](#view-trip-history).
2. Click the trip in the list. The map will move to fit its track.
3. To hide a trip, clear its checkbox.
4. To color the tracks by speed, open the **Color** menu and select **Speed**.

Click the selected trip again to go back to the whole period. To see where the asset stopped, click a stop in the list. The map clears when you change the period or the asset, and when you close the panel. To learn what the other colors and the **Display** menu do, see [Trip history](README.md#trip-history).

## View an asset's readings during a trip

A chart shows how one of the asset's readings changed during a trip, such as its speed or its battery level. For example, you can check how fast a driver went through a town, or find the moment when the ignition was turned off. A chart is a trip widget: it shows in the trip replay, which moves the asset along the trip's track on the map.

1. Open **Trip history** for the asset, as described in [View trip history](#view-trip-history).
2. In the list, click the trip that you want to check.
3. Click **Replay**. A player with the **Speed** chart will open over the map. The asset will start to move along the track.
4. Click **Add a chart**.
5. In **Charts**, select the readings that you want to see. Each of them will get its own chart in the player.
6. Click a moment on a chart, or drag across the charts. The asset will move to that moment on the map. Each chart will show its value at that moment under its name.

To change a chart's type, move it up or down, or remove it, click the chart's name. Navixy remembers your charts in this browser, so the next asset of the same asset type opens with the same charts. To learn what the charts show and how they are named, see [Trip widgets](README.md#trip-widgets).

## Show geo objects on the map

Geo objects, such as depots, customer sites, and delivery zones, are hidden on the map until you select them. Displaying one helps you see whether an asset is inside a zone or near a site.

1. If the **Geo objects** panel is collapsed, click it.
2. Open the geo object type's tab or stay on **All**.
3. Select the geo objects you want to see.

The map will draw their shapes with their names and move to show all of them. Clear a geo object to hide it again.

## Go to where a notification happened

A notification from a [notification rule](../rules-and-notifications/README.md) appears on the **Tracking**, **Assets**, **Geo objects**, **Schedules**, **Device management**, **IoT Logic**, and **Notification rules** screens. On **Tracking**, a notification that has a position can also move the map to the place where the event happened.

1. Click **Tracking** on the sidebar.
2. When a notification appears, click its text.

The map moves to the event's position, and the notification closes.
