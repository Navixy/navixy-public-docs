---
description: >-
  The Tracking page: the asset list, asset statuses, the map, map settings,
  asset details, trip history, trip replay, trip widgets, and geo objects.
tags:
  - reference
  - beta
---

# Tracking

Tracking shows where your assets are and what their devices report, live on the map. Use it to see which vehicles are moving and which are parked, to check a trailer's last position and readings, or to review the trips a courier made today.

Tracking shows your workspace's assets created on the **Assets** page. An asset appears on the map at the position of its primary GPS device, so an asset without a device has no position. To learn how assets and devices work together, see [Assets](../bdr/assets/).

This page describes the **Tracking** page, its panels, and its settings. To learn how to work with it, see [Track assets](track-assets.md).

## Tracking page

The Tracking page is where you find your assets on the map, follow them as they move, and review their trips. To open it, click **Tracking** on the sidebar.

<figure><img src="../.gitbook/assets/tracking-page.png" alt="The Tracking page. A map fills the page, with the Assets panel on one side and the Geo objects panel on the other. The Assets panel is on the All tab and lists assets under a heading for each asset type. Each entry has a checkbox and a details icon, and shows the asset&#x27;s name, its status, and the asset groups it belongs to. The panel header has the panel&#x27;s title, which opens a menu, and a Filter and sort icon. A bar over the map counts the assets that are moving, idling, stopped, parked, and offline. On the map, each marker is a disc in the color of the asset&#x27;s status with the asset&#x27;s name next to it. The Geo objects panel lists geo objects with checkboxes under a heading for each geo object type. A toolbar at the bottom of the map has the zoom buttons, a 2D button, and a layers icon."><figcaption><p>The Tracking page with the Assets and Geo objects panels open</p></figcaption></figure>

Here's what you can do:

* Find assets: The **Assets** panel lists your assets by asset type or by asset group. You can search the list and click <picture><source srcset="../.gitbook/assets/icons/tune-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/tune.svg" alt="Filter and sort" data-size="line"></picture> to filter and sort it.
* See what your assets are doing: The bar over the map shows how many assets have each status. Click a status to see only the assets with that status.
* See assets on the map: Every asset in the list has a marker that shows where it is and what it's doing. Click an asset in the list to center the map on it.
* Check an asset's details: <picture><source srcset="../.gitbook/assets/icons/info-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/info.svg" alt="Show details" data-size="line"></picture> on a list entry opens the details panel. It shows the asset's devices, their status, their position, and their latest readings.
* Review trip history: Select an asset, then click **Trip history** to see its trips and stops for a period of time.
* Replay a trip: Click **Replay** in the **Trip history** panel to view how the asset was moving along its route and add trip widgets to visualize additional trip data, such as speed, mileage, or GSM signal quality.
* Show geo objects: Select geo objects in the **Geo objects** panel to draw their shapes on the map.

The **Assets** and **Geo objects** panels are collapsed when you open the page for the first time. Click a collapsed panel to open it. Navixy remembers in your browser which panels are open.

## Asset list

The **Assets** panel lists your workspace's assets. You can switch between tabs, search, filter, sort, and select assets.

Click the panel title to choose how the list is organized:

* **By type**: One tab per asset type. The **All** tab shows every asset under a heading for its type, and the panel is titled **Assets**.
* **By asset group**: An **All** tab, then one tab per asset group, and the panel is titled **Asset groups**. An asset that belongs to several groups appears in each of them, and selecting it in one group selects it in all of them. The search in this view also finds group names and then lists every asset of the group.

Each list entry shows the asset's name, its status, and the custom fields you chose to show. The status is the one that the asset's marker has, with the speed while the asset is moving. By default, the fields are the asset's groups and its tags. To learn how to display another field, see [Track assets](track-assets.md#show-a-custom-field-in-the-asset-list).

Click an entry to open the asset. The map will move to the asset, and a small window will open next to its marker with the asset's name, its status, and 2 buttons. **Show details** opens the details panel, and **Trip history** opens the asset's trip history. An asset without a position has no marker, so its window opens next to the list.

Clicking an entry doesn't select the asset. To select assets, use their checkboxes. Selecting one asset also centers the map on it. While at least one asset is selected, a bar at the bottom of the panel shows their number and the **Trip history** button.

<picture><source srcset="../.gitbook/assets/icons/tune-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/tune.svg" alt="Filter and sort" data-size="line"></picture> in the panel header filters and sorts the list:

* **By status:** Shows only the assets with the statuses you select: **Moving**, **Idling**, **Stopped**, **Parked**, or **Offline**. With no status selected, every asset is shown. The filter applies to the map and the list. Applying it clears the selection and closes the details panel and the trip history.
* **Sort:** Sorts the list by name or by connection status. **By connection status** puts online assets first, followed by offline and then never reported.
* **More filters…:** Filters the assets by their custom fields. It's available on the tab of an asset type.

The icon shows a dot while a status filter or a sort order other than the default one applies. The filters and the sort order last until you close or reload the page.

## Asset statuses

Every asset has 2 statuses, which Navixy works out from the messages of its primary device. The details panel shows both statuses for each device of the asset. The list entry and the marker show one status that combines them.

The **connection status** says whether the device is sending messages:

<table><thead><tr><th width="182">Status</th><th>Meaning</th></tr></thead><tbody><tr><td><strong>Online</strong></td><td>A message from the device arrived in the last 10 minutes.</td></tr><tr><td><strong>Offline</strong></td><td>No message from the device for 10 minutes or more.</td></tr><tr><td><strong>Never reported</strong></td><td>The device has never sent a message.</td></tr></tbody></table>

Any sent message keeps a device online, including messages with readings only and repeats of the last known position. So an asset can show as **Online** while its marker stays at an old position. Typically, it's caused by the device: no satellite fix, for example in a tunnel or an underground car park, sleep or power-saving mode, or sending stored messages after a gap. Online status depends on when a message arrived, but the details panel shows the time that the message carries. A device that sends stored messages can therefore show as online with an outdated last message time.

The 10 minutes are the same for every device and every workspace, and you can't change them. A device in sleep or power-saving mode, or one that reports less often than every 10 minutes, shows as offline between its messages, although it works. It goes back online with its next message.

Offline means only that no message arrived. It doesn't mean that the device is broken. Common causes are due to the device or the SIM card: a weak mobile signal, deep sleep, roaming that isn't enabled on the SIM card, a used-up data allowance or balance, or a device that sends to a different server address.

The **motion status** says whether the device is moving:

<table><thead><tr><th width="187">Status</th><th>Meaning</th></tr></thead><tbody><tr><td><strong>Moving</strong></td><td>The device is moving. The speed shows next to the status.</td></tr><tr><td><strong>Stopped</strong></td><td>The device has been standing still for less than 5 minutes, such as at a traffic light or a delivery.</td></tr><tr><td><strong>Parked</strong></td><td>The device has been standing still for 5 minutes or more.</td></tr><tr><td><strong>Unknown</strong></td><td>The device is offline, has never reported, or hasn't sent a usable GPS or network position yet. A repeated or weak fix shows on the map but doesn't set the status.</td></tr></tbody></table>

A device is considered standing still while it stays within 100 m of where it stopped and its speed is 3 km/h or less. These values are the same for every device and every workspace, and you can't change them. Ignition doesn't affect the motion status or where trips start and end.

When a device loses its satellite fix, for example indoors or with a damaged antenna, it keeps sending messages, so it stays online. A message without coordinates doesn't move the marker. While the device stays online, the motion status keeps the value of the last usable position, so a vehicle that lost its fix while moving still reads as **Moving**. A stopped one still becomes **Parked** after 5 minutes. While a device is offline, its motion status is **Unknown** and its speed isn't shown.

The list entry, the marker on the map, the bar over the map, and the status filter combine the 2 statuses into one:

<table><thead><tr><th width="100">Marker</th><th width="119">Status</th><th>When</th></tr></thead><tbody><tr><td><picture><source srcset="../.gitbook/assets/markers/marker-moving-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/markers/marker-moving.svg" alt="Blue disc with a white arrow that points where the asset is heading" data-size="line"></picture></td><td><strong>Moving</strong></td><td>The device is moving.</td></tr><tr><td><picture><source srcset="../.gitbook/assets/markers/marker-idling-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/markers/marker-idling.svg" alt="Orange disc with two white bars" data-size="line"></picture></td><td><strong>Idling</strong></td><td>The device is stopped or parked, and its ignition is on.</td></tr><tr><td><picture><source srcset="../.gitbook/assets/markers/marker-stopped-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/markers/marker-stopped.svg" alt="Gray disc with a white square" data-size="line"></picture></td><td><strong>Stopped</strong></td><td>The device is stopped, and its ignition is off or the device doesn't report ignition.</td></tr><tr><td><picture><source srcset="../.gitbook/assets/markers/marker-parked-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/markers/marker-parked.svg" alt="Teal disc with a white letter P" data-size="line"></picture></td><td><strong>Parked</strong></td><td>The device is parked, and its ignition is off or the device doesn't report ignition. An online device whose motion status is unknown also shows as parked.</td></tr><tr><td><picture><source srcset="../.gitbook/assets/markers/marker-offline-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/markers/marker-offline.svg" alt="Light gray disc with a white dash" data-size="line"></picture></td><td><strong>Offline</strong></td><td>The device is offline, has never reported, or hasn't sent its state yet.</td></tr></tbody></table>

Navixy doesn't calculate ignition. **Idling** uses the ignition reading that the device reports. The device's configuration determines how the device decides that the ignition is on: a wire to the ignition circuit, the board voltage, movement, etc. A device that never reports ignition shows as **Stopped** or **Parked** when it stands still, never **Idling**.

## Assets by status

The bar over the map counts your assets by status: moving, idling, stopped, parked, and offline. It counts the assets of the open tab that match your search. An asset without a device has no status. The bar doesn't count it, and the list hides it while a status filter applies.

Clicking a status shows only the assets with that status in the list and on the map. It also selects them and moves the map to fit them. Clicking the status again removes the filter and clears the selection. The bar is hidden when the window is too narrow for it.

## Map

The map shows a marker for every asset in the list at the position of its primary device. It shows the assets of the open tab that match your search, so an asset that isn't in the list isn't on the map. Markers move as new positions arrive.

A marker is a disc in the color of the asset's status, with the asset's name next to it. A marker drives to each new position, and it changes its status when it arrives there. So the marker can show a new status a moment later than the list.

The marker shows the newest position that the device sent, including a repeated or weak fix. GPS drift can therefore move the marker of a parked vehicle. Weak fixes and repeats never change the motion status. A usable position changes it to **Moving** when it is more than 100 m from where the stop began or reports more than 3 km/h, and it adds a short trip to **Trip history**. Navixy has no setting that holds the position of a parked vehicle. Many devices have one of their own, set in the device configuration.

The markers of the selected assets and of the asset that you opened are highlighted. While only some of the assets on the map are highlighted, the other markers are paler and have no names. Hover over a pale marker to see its name.

Clicking a marker opens the asset, the same as clicking its entry in the list. The map will center on the asset, and the small window will open next to the marker. If a details panel is open, it will switch to that asset. Clicking a marker doesn't change the selection.

The toolbar at the bottom of the map can zoom in and out and switch the map between 3D and 2D. After you turn the map, the toolbar also has a button that turns it back to north. <picture><source srcset="../.gitbook/assets/icons/layers-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/layers.svg" alt="Open map settings" data-size="line"></picture> offers a choice of maps and has the map settings. Navixy remembers the map's position, zoom, and map type in your browser and shares them with the other Navixy maps in the same browser.

## Map settings

The map settings change what the map shows and how it reacts. To open them, click <picture><source srcset="../.gitbook/assets/icons/layers-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/layers.svg" alt="Open map settings" data-size="line"></picture> on the map toolbar.

<table><thead><tr><th width="149">Setting</th><th width="545">What it does</th><th>Default</th></tr></thead><tbody><tr><td><strong>Show asset names</strong></td><td>Shows each asset's name next to its marker. If viewed <strong>By asset group</strong>, the name takes the color of the asset's group.</td><td>On</td></tr><tr><td><strong>Show only selected assets</strong></td><td>Hides the markers of every unselected asset. With nothing selected, the map shows no markers.</td><td>Off</td></tr><tr><td><strong>Follow the selected asset</strong></td><td>Keeps the map centered on an asset as it moves. The map follows the asset that it last moved to: the asset that you selected, or the one whose entry or marker you clicked. Following stops when you drag the map or move it with the arrow keys, clear the asset's checkbox, select a geo object, or show trips from the trip history on the map. It starts again when you select or click an asset.</td><td>Off</td></tr></tbody></table>

## Asset details

The details panel shows everything an asset's devices report, and the asset's own fields. It opens in the place of the **Geo objects** panel. To open it, click <picture><source srcset="../.gitbook/assets/icons/info-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/info.svg" alt="Show details" data-size="line"></picture> on a list entry, or click **Show details** in the small window next to the asset's marker. It closes when the asset leaves the list, for example, when you switch tabs or search for another name. It also closes when you open the trip history.

The panel's header shows the asset's name and its status. Under it, the panel has one section for each **Device** field of the asset. The **Details** section comes last, with the asset's groups and its other fields. **Trip history** at the bottom of the panel opens the asset's trip history. Each device section shows:

<table><thead><tr><th width="220">Row</th><th>Contains</th></tr></thead><tbody><tr><td><strong>Model</strong></td><td>The device model.</td></tr><tr><td><strong>Device identifier</strong></td><td>The device's identifier, such as its IMEI, with the identifier type under it.</td></tr><tr><td><strong>Device status</strong></td><td>The status of the device record, such as whether it is activated.</td></tr><tr><td><strong>Connection status</strong></td><td>The connection status and when the device recorded its last message.</td></tr><tr><td><strong>Motion status</strong></td><td>The motion status, the speed while the device is moving, and when the status changed.</td></tr><tr><td><strong>Coordinates</strong></td><td>The latitude and longitude of the last position.</td></tr><tr><td><strong>Readings</strong></td><td>The latest values the device reported, such as ignition, fuel level, or battery level. Which readings appear depends on the device.</td></tr></tbody></table>

Each reading is listed under its title. A reading that has no title is listed under its code name, such as **hw_mileage**. When a device has several readings of one kind, the name adds a channel number, such as **din channel 1**. A reading that is on or off shows Yes or No. Some readings need a word of explanation:

* **Odometer:** **hw_mileage** is the count that the device itself reports, in kilometers. Navixy doesn't calculate an odometer and has no setting to correct it or set its start value, so it can differ from the vehicle's dashboard odometer.
* **Inputs and outputs:** A discrete input, such as a door sensor, an ignition wire, or an emergency button, has the code name **din** with its input number as the channel, such as **din channel 1**. An output has the code name **dout**. An analog input has the code name **analog**, such as **analog channel 1**, in volts. The input numbers are the ones in the device manufacturer's documentation.
* **Doors, hood, and trunk:** These states are usually available only when the vehicle sends them over the CAN bus and the device passes them on. Not every vehicle model does. Their code names are **door_open** with the door number as the channel, **hood_open**, and **trunk_open**.

<picture><source srcset="../.gitbook/assets/icons/star-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/star.svg" alt="Show the field for this asset in the list" data-size="line"></picture> will show that field in the asset's list entry. Pinned fields are saved for each asset in this browser only: another person, or you on another computer, will see the default fields. You can pin every row except **Connection status**, **Motion status**, and the readings.

<picture><source srcset="../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/edit.svg" alt="Edit asset" data-size="line"></picture> in the panel header opens the asset editor, the same one as on the **Assets** page. You can't delete the asset from here, and its fields can't create new devices or records. To learn what its fields do, see [Assets](../bdr/assets/#asset-editor).

## Trip history

The **Trip history** panel lists an asset's trips and stops for the period you choose and shows the trips on the map. To open it, click **Trip history** in the **Assets** panel, in an asset's details panel, or in the small window next to a marker. The panel opens in the place of the **Geo objects** panel. It closes when you clear the selection, open a details panel, or apply a status filter.

<figure><img src="../.gitbook/assets/tracking-trip-history.png" alt="The Tracking page with the Trip history panel open in the place of the Geo objects panel. The panel shows the asset&#x27;s name and status, the period buttons Today, Yesterday, and Last 7 days with a calendar icon, the Replay button, and the Color and Display menus. The Trips tab shows the period&#x27;s distance, driving time, and stopped time, then the start, the trips with their distance, duration, and speeds, and the stops between them. Each trip has a checkbox. On the map, the trips&#x27; tracks run between stop markers, and some markers show a number of stops. In the Assets panel, the asset is selected, and a bar at the bottom of the panel shows the number of selected assets and the Trip history button."><figcaption><p>Trip history for one asset, with its trips on the map</p></figcaption></figure>

The panel shows one asset at a time. If you selected several assets, the asset's name at the top of the panel is a menu. Use it to switch to another selected asset.

### Period

The period is **Today** every time the panel opens. **Yesterday** and **Last 7 days** change it with one click. **Last 7 days** covers the last 7 days before the current moment.

<picture><source srcset="../.gitbook/assets/icons/calendar_month-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/calendar_month.svg" alt="Choose a custom period" data-size="line"></picture> opens a calendar for any other period. You can select a ready period, such as **Last week** or **This month**, or click the first and the last day in the calendar. **Start** and **End** also accept a date and a time that you enter. The period changes when you click **Apply**. The longest period is 31 days.

### Trips

The **Trips** tab lists the period from its earliest moment to its latest. A period longer than one day has a heading for each day. The line above the list shows the period's distance, driving time, and stopped time. The list has the following entries:

<table><thead><tr><th width="150">Entry</th><th>What it shows</th></tr></thead><tbody><tr><td><strong>Start</strong>, <strong>End</strong></td><td>When and where the first trip of the period started and the last trip ended, with the coordinates.</td></tr><tr><td>Trip</td><td>The trip's distance and duration. Below are the time when it started and ended, the average speed, and the top speed. If a trip had device events, their icons will be shown, and clicking them will open these events on the <strong>Device events</strong> tab. A trip also states for how long the device had no satellite position, if that lasted 1 minute or more.</td></tr><tr><td><strong>Stopped</strong></td><td>A stop shorter than 10 minutes, with its duration and coordinates.</td></tr><tr><td><strong>Parked</strong></td><td>A stop of 10 minutes or more. In trip history, <strong>Parked</strong> starts at 10 minutes, not at 5 minutes as in the motion status.</td></tr><tr><td><strong>Idling</strong></td><td>The asset stood still for 2 minutes or more in the middle of a trip. Trip history calculates this from the speed, not the ignition.</td></tr><tr><td><strong>No data</strong></td><td>The device sent nothing for 15 minutes or more between 2 trips or stops.</td></tr></tbody></table>

Only stops of 5 minutes or more are listed. Shorter stops, such as at a traffic light, are part of the trips.

Trip history builds trips from the usable positions that the device sent. A trip's distance is the sum of the straight-line distances between them. A trip starts when a new position is more than 100 m from where the device stopped or reports a speed above 3 km/h, and a gap in the data doesn't end it unless no position data arrives for more than 25 hours. When a device reports rarely, its route is a straight line between reports, so the distance is shorter than the distance driven, and a short stop between two reports isn't listed. How often a device sends positions is set on the device. Reports build trips by their own rules, so Tracking and Reports can show different trips for such a device.

An asset's trips come from its primary device, so an asset with 2 GPS devices lists every trip once. If the asset made no trips in the period, the panel says so.

### Trips on the map

When the panel opens, the map shows the tracks of all the period's trips. You choose in the list what the map shows:

* Clicking a trip selects it and moves the map to fit its track. Clicking the selected trip again goes back to the whole period.
* The checkbox of a trip removes its track from the map or adds it again. The checkbox above the list does this for all trips.
* Clicking a stop, or another entry with coordinates, moves the map to that place.

The map marks where the period or the selected trip starts and ends. A dashed part of a track means that the device sent no position there for 2 minutes or more. The map clears when you change the period or the asset and when the panel closes.

The **Color** menu sets the colors of the tracks, and the line under it explains the current colors:

<table><thead><tr><th width="149">Option</th><th>What it does</th></tr></thead><tbody><tr><td><strong>Trips</strong></td><td>Gives each trip its own color, in the order of the list. After 8 trips, the colors repeat. This is the default.</td></tr><tr><td><strong>Status</strong></td><td>Colors each part of a track by what the asset was doing there: moving, idling, stopped, or parked.</td></tr><tr><td><strong>Speed</strong></td><td>Colors each part of a track by the speed there, on a scale from the slowest to the fastest speed of the trips on the map.</td></tr><tr><td><strong>One color</strong></td><td>Draws the whole period in one color.</td></tr></tbody></table>

The **Display** menu sets what else the map shows and how the period is divided into trips:

<table><thead><tr><th width="220">Setting</th><th>What it does</th></tr></thead><tbody><tr><td><strong>Stops</strong></td><td>Marks the stops on the map. A stop's marker shows its duration when you zoom in.</td></tr><tr><td><strong>Device events</strong></td><td>Marks the places of the device events on the map.</td></tr><tr><td><strong>Cell and Wi-Fi positions</strong></td><td>Shows as dots the positions that the device worked out from cell towers or Wi-Fi networks. The tracks use satellite positions only. This setting is off by default.</td></tr><tr><td><strong>Cluster markers</strong></td><td>Replaces markers that are too close to each other with one marker that shows their number. Clicking it zooms in on them.</td></tr><tr><td><strong>Smart filter</strong></td><td>Leaves out of a track a position that the asset couldn't have reached in the time since the position before it, such as a GPS error far from the route.</td></tr><tr><td><strong>Split into trips</strong></td><td>When it's off, the period is one trip from its first start to its last end, and the list has no checkboxes.</td></tr><tr><td><strong>Merge stops shorter than</strong></td><td>Joins a stop shorter than the selected time with the trips before and after it into one trip. The default is 5 minutes. The stopped time above the list still counts these stops.</td></tr><tr><td><strong>Track width</strong></td><td><strong>Auto</strong> keeps the same width on screen at every zoom. <strong>Fixed</strong> sets a width on the ground, from 10 to 300 m, so the track gets wider when you zoom in.</td></tr></tbody></table>

Navixy remembers the **Color** and **Display** settings in your browser.

### Device events

The **Device events** tab lists the events that the asset's device reported in the period, such as harsh braking or an SOS signal. The latest event is first. You can filter the list by event type. Clicking an event moves the map to the place where it happened, and a replay that is open moves to that moment. If the device reported no events in the period, the tab says so.

## Trip replay

The trip replay visualizes the asset's movement on the map during the trip and shows trip widgets for the same time. Use it to see what happened at one moment of a trip: where the asset was, how fast it went, and what its devices reported. **Replay** in the **Trip history** panel opens the player over the map.

<figure><img src="../.gitbook/assets/tracking-trip-replay.png" alt="The trip replay. A player over the map shows a play button, the replayed time and date, the Add a chart button, the playback speed, a follow icon, and a close icon. Under them are three charts on one time scale: the speed as a line, a voltage reading as a line, and an ignition reading as a bar. Each chart shows its name and its value at the replayed moment, and a vertical line through all the charts marks that moment. On the map, a marker on the trip&#x27;s track shows where the asset was at that moment. In the Trip history panel, the replayed trip is highlighted in the list."><figcaption><p>The trip replay with 3 charts</p></figcaption></figure>

The replay plays one trip at a time. It starts with the trip that you clicked in the list or with the first trip on the map. Clicking another trip in the list replays that trip. At **1×**, the replay takes as long as the trip did, and you can select a higher speed. Click **Replay** in the panel or press the Space bar to pause or unpause it. The replay closes when you select another period or asset.

### Trip widgets

A trip widget shows one thing that happened during the replayed trip, such as how the asset's speed or its fuel level changed. Trip widgets appear in the replay player. At the moment, every trip widget is a chart. You choose which charts the player shows and how each chart looks.

The player opens with the **Speed** chart. **Add a chart** opens the **Charts** list, where you select what else to see. The list has the speed and every reading of the asset's devices that is a number or is either on or off. Next to each reading, the list shows its value at the replayed moment.

Every chart covers the time of the replayed trip:

* A line across the charts marks the replayed moment, and each chart shows its value at that moment under its name.
* Clicking a moment on a chart, or dragging across the charts, moves the replay to that moment.
* The player shows 4 charts at a time. Scroll the charts to see the others.
* A reading that is either on or off is drawn as a bar over the time when it was on.
* If a reading has no values during the trip, its chart says so.

A chart has the same name as its reading in the details panel. That is the reading's title, or its code name, such as **battery_level**, when it has no title. If the asset has several devices, the chart of a device other than the primary device adds that device's name.

Clicking a chart's name opens its options. You can move the chart up or down, remove it, and select its **Chart type**:

* **Line:** Connects the values with a line.
* **Area:** Draws the same line and fills the space under it.
* **Steps:** Draws each value as a flat step. Use it for a reading that changes in jumps, such as a signal level.

A reading that is either on or off has no chart type.

Navixy remembers your charts, their order, and their types in your browser, for each asset type in the workspace. Every asset of that type opens with the same charts. Another person, or you on another computer, will start with the **Speed** chart only. If an asset's devices don't send a reading that you selected for its asset type, the player leaves that chart out. The **Charts** list shows such readings in a separate group at the end, where you can clear them. **Reset to defaults** in the **Charts** list removes every chart except **Speed** and restores the default chart types.

To learn how to add a chart, see [View an asset's readings during a trip](track-assets.md#view-an-assets-readings-during-a-trip).

## Geo objects

The **Geo objects** panel lists the geo objects of your workspace, with one tab per geo object type and the **All** tab. You can search the list. <picture><source srcset="../.gitbook/assets/icons/tune-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/tune.svg" alt="Filter and sort" data-size="line"></picture> in the panel header sorts it by name. On the tab of a geo object type, it also filters the list by the type's custom fields.

Selecting a geo object draws its shape and its name on the map and moves the map to show every selected geo object. While a geo object is selected, a bar at the bottom of the panel shows the number of selected geo objects. To learn how to create geo objects and their types, see [Geo objects](../bdr/geo-objects/).

The details panel and the **Trip history** panel open in the place of this panel. The panel comes back with its tab, search, and selection when you close them.

## Access to Tracking

Tracking is in the menu when your workspace includes it. If your workspace doesn't include Tracking, or you don't have access to it, you will receive a warning. What you see in Tracking follows your access to assets, devices, and geo objects in [Business Data Repository](../bdr/README.md#access-to-business-data-repository):

* The **Assets** panel and the map show only the assets that your role lets you see.
* The details panel of an asset shows only the devices that your role lets you see.
* The **Geo objects** panel lists only the geo objects that your role lets you see.
* If you don't have access to asset types or geo object types, you will receive a warning in the **Assets** or **Geo objects** panel.
* <picture><source srcset="../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/edit.svg" alt="Edit asset" data-size="line"></picture> in the details panel is shown to everyone. Saving changes to the asset needs a role that lets you edit assets.

To learn more, see [Access and roles](../access-and-roles.md).
