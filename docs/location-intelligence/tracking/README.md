---
description: >-
  The Tracking page: the asset list, asset statuses, the map, map settings,
  asset details, trip history and trips on the map, and geo objects.
tags:
  - reference
  - beta
noIndex: true
---

# Tracking

Tracking shows where your assets are and what their devices report, live on the map. Use it to see which vehicles are moving and which are parked, to check a trailer's last position and readings, or to review the trips a courier made today.

Tracking shows your workspace's assets created on the **Assets** page. An asset appears on the map at the position of its primary GPS device, so an asset without a device has no position. To learn how assets and devices work together, see [Assets](../bdr/assets/).

This page describes the **Tracking** page, its panels, and its settings. To learn how to work with it, see [Track assets](track-assets.md).

## Tracking page

The Tracking page is where you find your assets on the map, follow them as they move, and review their trips. To open it, click **Tracking** on the sidebar.

<figure><img src="../.gitbook/assets/tracking-page.png" alt="The Tracking page. A map fills the page, with the Assets panel on one side and the Geo objects panel on the other. The Assets panel is on the All tab and lists assets under a heading for each asset type. Each entry shows the asset&#x27;s name, its speed, its connection status, and its asset group. The panel header has a filter icon, a sort icon, and a three-dot icon for the map settings. On the map, markers with name labels show where the assets are and what they are doing: an arrow points where a moving asset is heading, and other shapes stand for assets that are idling, parked, or offline. The Geo objects panel lists geo objects under a heading for each geo object type. Zoom buttons and a compass sit at the corner of the map."><figcaption><p>The Tracking page with the Assets and Geo objects panels open</p></figcaption></figure>

Here's what you can do:

* Find assets: The **Assets** panel lists your assets by asset type or by asset group. Filter and sort the list from the panel header, and search it with <picture><source srcset="../.gitbook/assets/icons/search-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/search.svg" alt="Search" data-size="line"></picture> under the tabs.
* See assets on the map: Every asset in the list has a marker that shows where it is and what it is doing. Select an asset to move the map to it.
* Check an asset's details: Open the details panel with <picture><source srcset="../.gitbook/assets/icons/info-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/info.svg" alt="Show details" data-size="line"></picture> on a list entry or by clicking a marker. It shows the asset's devices, their status, their position, and their latest readings.
* Review trip history: Select one or more assets, then click **Trip history** to see their trips and stops for a period.
* Show geo objects: Select geo objects in the **Geo objects** panel to draw their shapes on the map.

## Asset list

The **Assets** panel lists your workspace's assets. You can switch between tabs, search, filter, sort, and select assets.

Click the panel title to choose how the list is organized:

* **By type**: One tab per asset type. The **All** tab shows every asset under a heading for its type, and the panel is titled **Assets**.
* **By asset group**: An **All** tab, then one tab per asset group, and the panel is titled **Asset groups**. An asset that belongs to several groups appears in each of them, and selecting it in one group selects it in all of them. The search in this view also finds group names and then lists every asset of the group.

Each list entry shows the asset's name, speed, and connection status, as well as the fields you chose to show. By default, these are the asset's groups and its tags. To learn how to show another field, see [Track assets](track-assets.md#show-a-custom-field-in-the-asset-list).

Clicking an entry selects it, and clicking it again clears it. Each time you select an asset, the map centers on it. The **Trip history** button appears at the bottom of the panel while at least one selected asset is on the current tab and matches your search.

The panel header has three menus:

<table><thead><tr><th width="167">Menu</th><th>What it does</th></tr></thead><tbody><tr><td><picture><source srcset="../.gitbook/assets/icons/filter_alt-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/filter_alt.svg" alt="Open filters" data-size="line"></picture></td><td>Shows only the assets with the statuses you select: <strong>Moving</strong>, <strong>Idling</strong>, <strong>Stopped</strong>, <strong>Parked</strong>, or <strong>Offline</strong>. With no status selected, every asset is shown. On the asset type tab, <strong>More filters…</strong> also filters the assets by their custom fields. The filter applies to the map and the list.</td></tr><tr><td><picture><source srcset="../.gitbook/assets/icons/sort-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/sort.svg" alt="Sort" data-size="line"></picture></td><td>Sorts the list by name and connection status. <strong>By connection status</strong> puts online assets first, followed by offline and then never reported.</td></tr><tr><td><picture><source srcset="../.gitbook/assets/icons/more_vert-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/more_vert.svg" alt="Open map settings" data-size="line"></picture></td><td>Opens the map settings. See <a href="./#map-settings">Map settings</a>.</td></tr></tbody></table>

The filters and the sort order last until you close or reload the page.

## Asset statuses

Every asset has two statuses, which Navixy works out from the messages of its primary device. The list entry shows the connection status, and the details panel shows both statuses for each device of the asset.

The **connection status** says whether the device is sending messages:

<table><thead><tr><th width="182">Status</th><th>Meaning</th></tr></thead><tbody><tr><td><strong>Online</strong></td><td>A message from the device arrived in the last 10 minutes.</td></tr><tr><td><strong>Offline</strong></td><td>No message from the device for 10 minutes or more.</td></tr><tr><td><strong>Never reported</strong></td><td>The device has never sent a message.</td></tr></tbody></table>

Any message keeps a device Online, including messages with readings only and repeats of the last known position. So an asset can show Online while its marker stays at an old position. Typical causes are on the device: it has no satellite fix, for example in a tunnel or an underground car park, it is in sleep or power-saving mode, or it is sending stored messages after a gap. Online depends on when a message arrived, but the details panel shows the time that the message carries. A device that sends stored messages can therefore show Online with an old last message time.

The 10 minutes are the same for every device and every workspace, and you can't change them. A device in sleep or power-saving mode, or one that reports less often than every 10 minutes, shows as Offline between its messages, although it works. It becomes Online again with its next message.

Offline means only that no message arrived. It doesn't say whether the device works. Common causes are on the device or the SIM card: a weak mobile signal, deep sleep, roaming that isn't enabled on the SIM card, a used-up data allowance or balance, or a device that sends to a different server address.

The **motion status** says whether the device is moving:

<table><thead><tr><th width="187">Status</th><th>Meaning</th></tr></thead><tbody><tr><td><strong>Moving</strong></td><td>The device is moving. The speed shows next to the status.</td></tr><tr><td><strong>Stopped</strong></td><td>The device has been standing still for less than 5 minutes, such as at a traffic light or a delivery.</td></tr><tr><td><strong>Parked</strong></td><td>The device has been standing still for 5 minutes or more.</td></tr><tr><td><strong>Unknown</strong></td><td>The device is offline, has never reported, or hasn't sent a usable GPS or network position yet. A repeated or weak fix shows on the map but doesn't set the status.</td></tr></tbody></table>

A device counts as standing still while it stays within 100 m of where it stopped and its speed is 3 km/h or less. These values are the same for every device and every workspace, and you can't change them. Ignition doesn't affect the motion status or where trips start and end.

When a device loses its satellite fix, for example indoors or with a damaged antenna, it keeps sending messages, so it stays Online. A message without coordinates doesn't move the marker. While the device stays Online, the motion status keeps the value of the last usable position, so a vehicle that lost its fix while moving still reads as **Moving**. A stopped one still becomes **Parked** after 5 minutes. While a device is offline, its motion status is **Unknown** and its speed isn't shown.

The marker on the map and the status filter combine the two:

<table><thead><tr><th width="100">Marker</th><th width="119">Status</th><th>When</th></tr></thead><tbody><tr><td><picture><source srcset="../.gitbook/assets/markers/marker-moving-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/markers/marker-moving.svg" alt="Blue arrow, pointing where the asset is heading" data-size="line"></picture></td><td><strong>Moving</strong></td><td>The device is moving.</td></tr><tr><td><picture><source srcset="../.gitbook/assets/markers/marker-idling-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/markers/marker-idling.svg" alt="Orange ring" data-size="line"></picture></td><td><strong>Idling</strong></td><td>The device is stopped or parked, and its ignition is on.</td></tr><tr><td><picture><source srcset="../.gitbook/assets/markers/marker-stopped-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/markers/marker-stopped.svg" alt="Red square" data-size="line"></picture></td><td><strong>Stopped</strong></td><td>The device is stopped, and its ignition is off or the device doesn't report ignition.</td></tr><tr><td><picture><source srcset="../.gitbook/assets/markers/marker-parked-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/markers/marker-parked.svg" alt="Dark gray square" data-size="line"></picture></td><td><strong>Parked</strong></td><td>The device is parked, and its ignition is off or the device doesn't report ignition. An online device whose motion status is unknown also shows as parked.</td></tr><tr><td><picture><source srcset="../.gitbook/assets/markers/marker-offline-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/markers/marker-offline.svg" alt="Gray arrow, faded" data-size="line"></picture></td><td><strong>Offline</strong></td><td>The device is offline, has never reported, or hasn't sent its state yet. The arrow points in the last known direction.</td></tr></tbody></table>

Navixy doesn't calculate ignition. **Idling** uses the ignition reading that the device reports. How the device decides that the ignition is on is set in its own configuration, for example from a wire to the ignition circuit, the board voltage, or movement. A device that never reports ignition shows **Stopped** or **Parked** when it stands still, never **Idling**.

## Map

The map shows a marker for every asset in the list at the position of its primary device. It shows the assets of the open tab that match your search, so an asset that isn't in the list isn't on the map. Markers move as new positions arrive.

The marker shows the newest position that the device sent, including a repeated or weak fix. GPS drift can therefore move the marker of a parked vehicle. Weak fixes and repeats never change the motion status. A usable position changes it to **Moving** when it is more than 100 m from where the stop began or reports more than 3 km/h, and it adds a short trip to **Trip history**. Navixy has no setting that holds the position of a parked vehicle. Many devices have one of their own, set in the device configuration.

A selected asset's marker is circled. Clicking a marker selects only that asset, clears the rest of the selection, moves the map to it, and opens its details panel.

The controls at the corner of the map zoom in and out, and the compass turns the map back to north. Navixy remembers the map's position and zoom in your browser and shares them with the other Navixy maps in the same browser.

## Map settings

The map settings change what the map shows and how it reacts. To open them, click <picture><source srcset="../.gitbook/assets/icons/more_vert-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/more_vert.svg" alt="Open map settings" data-size="line"></picture> in the header of the **Assets** panel.

<table><thead><tr><th width="149">Setting</th><th width="545">What it does</th><th>Default</th></tr></thead><tbody><tr><td><strong>Show asset names</strong></td><td>Shows each asset's name next to its marker. If viewed <strong>By asset group</strong>, the name takes the color of the asset's group.</td><td>On</td></tr><tr><td><strong>Show only selected assets</strong></td><td>Hides the markers of every unselected asset. With nothing selected, the map shows no markers.</td><td>Off</td></tr><tr><td><strong>Show details on click</strong></td><td>Opens the details panel when you select an asset in the list.</td><td>Off</td></tr><tr><td><strong>Follow the selected asset</strong></td><td>Keeps the map centered on the selected asset as it moves. Following stops when you drag the map or move it with the arrow keys, uncheck the asset, select a geo object, or show a trip or a stop from the trip history on the map. It starts again when you select the asset again or click its marker.</td><td>Off</td></tr></tbody></table>

## Asset details

The details panel shows everything an asset's devices report. It opens next to the **Assets** panel and shares the title with the asset's name. Open it by clicking <picture><source srcset="../.gitbook/assets/icons/info-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/info.svg" alt="Show details" data-size="line"></picture> on a list entry, which appears when you hover over the entry, or by clicking the asset's marker. It closes when the asset leaves the list, for example, when you switch tabs or search for another name.

The panel has one section for each **Device** field of the asset. The section of the primary device is open, and the others are collapsed. Each section shows:

<table><thead><tr><th width="220">Row</th><th>Contains</th></tr></thead><tbody><tr><td><strong>Model</strong></td><td>The device model.</td></tr><tr><td><strong>Device identifier</strong></td><td>The device's identifier, such as its IMEI, with the identifier type under it.</td></tr><tr><td><strong>Device status</strong></td><td>The status of the device record, such as whether it is activated.</td></tr><tr><td><strong>Connection status</strong></td><td>The connection status and when the device recorded its last message.</td></tr><tr><td><strong>Motion status</strong></td><td>The motion status, the speed while the device is moving, and when the status changed.</td></tr><tr><td><strong>Coordinates</strong></td><td>The latitude and longitude of the last position.</td></tr><tr><td>Readings</td><td>The latest values the device reported, such as ignition, fuel level, or battery level. Which readings appear depends on the device.</td></tr></tbody></table>

Each reading is listed under its code name, such as **hw_mileage**. When a device has several readings of one kind, the name adds a channel number, such as **din channel 1**. A reading that is on or off shows Yes or No. Some readings need a word of explanation:

* **Odometer:** **hw_mileage** is the count that the device itself reports, in kilometers. Navixy doesn't calculate an odometer and has no setting to correct it or set its start value, so it can differ from the vehicle's dashboard odometer.
* **Inputs and outputs:** A discrete input, such as a door sensor, an ignition wire, or an emergency button, appears as **din** with its input number as the channel, such as **din channel 1**. An output appears as **dout**. An analog input appears as **analog**, such as **analog channel 1**, in volts. The input numbers are the ones in the device manufacturer's documentation.
* **Doors, hood, and trunk:** These states are usually available only when the vehicle sends them over the CAN bus and the device passes them on. Not every vehicle model does. They appear as **door_open** with the door number as the channel, **hood_open**, and **trunk_open**.

<picture><source srcset="../.gitbook/assets/icons/star_border-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/star_border.svg" alt="Show the field for this asset in the list" data-size="line"></picture> will show that field in the asset's list entry. Pinned fields are saved for each asset in this browser only: another person, or you on another computer, will see the default fields. You can pin every row except **Connection status**, **Motion status**, and the readings.

<picture><source srcset="../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/edit.svg" alt="Edit asset" data-size="line"></picture> in the panel header opens the asset editor, the same one as on the **Assets** page. You can't delete the asset from here. To learn what its fields do, see [Assets](../bdr/assets/#asset-editor).

## Trip history

The **Trip history** panel lists the trips and stops of the assets you selected for the period you choose, and shows them on the map. It opens next to the **Assets** panel, in place of the details panel. It closes when you clear the selection, when none of the selected assets are on the current tab or match your search, and when you open a details panel.

The default period when you open the panel is today. To choose another period, click the date range at the top of the panel. You can enter the dates and times in **Start Date** and **End Date**, or click a day in the calendar.

The list has one section for each selected asset with the number of its trips and a heading for each day. It has two kinds of entries:

* **Trip:** When the trip started and ended, with the coordinates of both places, the distance, and how long it took.
* **Stop:** Where the asset stopped, with the stop's coordinates, and how long it stayed. Only a stop of 5 minutes or more is listed. A shorter stop, such as at a traffic light, is part of the trip.

Trip history builds trips from the usable positions that the device sent. A trip's distance is the sum of the straight-line distances between them. A trip starts when a new position is more than 100 m from where the device stopped or reports a speed above 3 km/h, and data that stops arriving doesn't end it. When a device reports rarely, its route is a straight line between reports, so the distance is shorter than the distance driven, and a short stop between two reports isn't listed. How often a device sends positions is set on the device. Reports build trips by their own rules, so Tracking and Reports can show different trips for such a device.

Each asset's trips come from its primary device, so an asset with two trackers lists every trip once. History covers the selected assets on the current tab that match your search. The status filter doesn't narrow it. If the assets made no trips or stops in the period, the panel says so.

Trips and stops appear on the map when you choose them in the list:

* Clicking a trip shows only that trip: its track, with a pin at the start and at the end. The map moves to fit the track into the part of the map that the panels leave open.
* The checkbox of a trip adds it to the trips on the map or removes it. You can show several trips at once.
* Clicking a stop marks it on the map and moves the map to it. Only one stop is marked at a time. Click it again to remove the mark.

The map clears when you change the period or the selected assets, and when the panel closes.

<picture><source srcset="../.gitbook/assets/icons/palette-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/palette.svg" alt="Color tracks" data-size="line"></picture> in the panel header sets the colors of the tracks:

<table><thead><tr><th width="149">Option</th><th>What it does</th></tr></thead><tbody><tr><td><strong>By trip</strong></td><td>Gives each trip its own color, in the order of the list. After 17 trips, the colors repeat. This is the default.</td></tr><tr><td><strong>Manually</strong></td><td>Starts from the <strong>By trip</strong> colors and lets you change them: click <picture><source srcset="../.gitbook/assets/icons/route-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/route.svg" alt="Change the trip color" data-size="line"></picture> next to a trip and select a color. The colors you select are lost when the list reloads.</td></tr><tr><td><strong>By speed</strong></td><td>Colors each part of a track by the speed there: green up to the low end of <strong>Speed range</strong>, then yellow, then red from the high end. A gray part has no speed. The slider goes from 0 to 150 km/h, and the default range is 0 to 100 km/h.</td></tr></tbody></table>

Navixy remembers the coloring and the speed range in your browser.

## Geo objects

The **Geo objects** panel lists the geo objects of your workspace, with one tab per geo object type and the **All** tab. You can search the list, sort it by name, and filter it by the selected type's custom fields.

Selecting a geo object draws its shape on the map and moves the map to show every selected geo object. To learn how to create geo objects and their types, see [Geo objects](../bdr/geo-objects/).

## Access to Tracking

Tracking is in the menu when your workspace includes it. What you see in Tracking follows your access to assets, devices, and geo objects in [Business Data Repository](../bdr/README.md#access-to-business-data-repository):

* The **Assets** panel and the map show only the assets that your role lets you see.
* The details panel of an asset shows only the devices that your role lets you see.
* The **Geo objects** panel lists only the geo objects that your role lets you see.
* <picture><source srcset="../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/edit.svg" alt="Edit asset" data-size="line"></picture> in the details panel is shown to everyone. Saving changes to the asset needs a role that lets you edit assets.

To learn more, see [Access and roles](../access-and-roles.md).
