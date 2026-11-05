---
description: >-
  Reference for live tracking state and its queries: the last known position,
  connectivity, motion, readings, and events of a device or of an asset's
  devices.
tags:
  - reference
  - beta
---

# Live state

Last known state of devices and of the assets they are linked to: position, connectivity, motion, readings and events, with the queries that read it.

## Queries

### assetState

Tracking state of one asset. Null when the asset does not exist or is not visible.

```graphql
assetState(assetId: ID!): TrackingAssetState
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `assetId` | `ID!` | The asset. |

**Output types:**

<details>

<summary>TrackingAssetState</summary>

Tracking state of an asset: the states of the devices linked to it, side by side.

Deliberately NOT merged into one state. An asset can carry several devices reporting the same
field — two temperature sensors, two trackers — and any merge policy (primary wins, newest wins)
either hides a value or makes it jump between sources. The asset exposes the list and the client
decides.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `asset` | [Asset](../../../business-data-repository/api-reference/assets/README.md#asset)! |  |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Newest `updatedAt` across the asset's devices; under `TrackingAssetStateFilter.primaryDeviceOnly`, the primary device's. Null when none of those devices has ever reported, or, under that flag, when the asset has no primary device the caller can see. |
| `devices` | [[TrackingDeviceState](#trackingdevicestate)!]! | State of every device linked to the asset. |
| `primaryDevice` | [TrackingDeviceState](#trackingdevicestate) | State of `Asset.primaryDevice`, for the common case of one tracker per asset. The same object also appears in `devices`; this is a pointer, not a second state. |

</details>

---

### assetStates

Tracking state of the caller's assets.

  Counts for several filters come in one request: alias `assetStates` once per filter, each with `first: 1` and
  `total { count }`. A request counts once against the rate limit however many aliases it holds; they all count
  toward its complexity limit. Each alias is answered on its own, as a separate `assetStates` call would be, but an
  error in one of them, such as an id of the wrong type, leaves the whole response without data.

```graphql
assetStates(
    filter: TrackingAssetStateFilter
    orderBy: TrackingAssetStateOrder = { field: ASSET_TITLE, direction: ASC }
    first: Int
    after: String
    last: Int
    before: String
  ): TrackingAssetStateConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `TrackingAssetStateFilter` |  |
| `orderBy` | `TrackingAssetStateOrder` |  |
| `first` | `Int` |  |
| `after` | `String` |  |
| `last` | `Int` |  |
| `before` | `String` |  |

**Input types:**

<details>

<summary>TrackingAssetStateFilter</summary>

Filters over asset states; the conditions combine with AND, except that `assetGroupIds` and `ungrouped` form one
condition by OR. Since an asset's state is a list, every device-level
condition (`connectivityStatus`, `motionStatus`, `updatedSince`) matches when **any** of the asset's devices satisfies
it, unless `primaryDeviceOnly` is true.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetIds` | `[ID!]` | Only these assets. An empty list sets no condition and answers every asset the other conditions match, so a client that builds the list from a search that found nothing should skip the query. In `TrackingAssetStateSubscriptionInput.assetIds` an empty list follows no asset. |
| `assetGroupIds` | `[ID!]` | Assets in any of these groups. A group counts only while the caller can see it and it is not deleted, so the id of a hidden or deleted group matches nothing. With `ungrouped: true`, assets in no group match as well. An empty list sets no condition. |
| `ungrouped` | `Boolean` | Assets in no group the caller can see; a group hidden from the caller or deleted does not count. Combined with `assetGroupIds` by OR, so "no group" can be one more option in a choice of groups. False or absent sets no condition. |
| `typeIds` | `[ID!]` | Assets of any of these types. An empty list sets no condition. |
| `titleContains` | `String` | Partial match on the asset's title, case-insensitive, as `AssetFilter.titleContains`. |
| `connectivityStatus` | [[TrackingConnectivityStatus](#trackingconnectivitystatus)!] |  |
| `motionStatus` | [[TrackingMotionStatus](#trackingmotionstatus)!] |  |
| `updatedSince` | [DateTime](../../../core-api-reference/common.md#datetime) | Only assets where some device reported at or after this moment. |
| `primaryDeviceOnly` | `Boolean` | Check `connectivityStatus`, `motionStatus` and `updatedSince` on `Asset.primaryDevice` only: an asset's status is its primary device's status. An asset without a primary device, or whose primary device the caller cannot see, matches none of them. Also takes `TrackingAssetState.updatedAt`, and with it the `UPDATED_AT` order, from the primary device. False or absent: any device, as this type describes. |

</details>

<details>

<summary>TrackingAssetStateOrder</summary>

Order of an asset-state page. Only asset-side keys are offered as stable orders; `UPDATED_AT`
moves with every message, so a page boundary read under it may not hold on the next request — use
it for a one-shot "most recently active" list, not for walking a large set page by page.

A cursor under `UPDATED_AT` carries the anchor asset's `(updatedAt, assetId)`, and the next page
starts after that pair in the requested order. It never fails because the anchor asset has moved or
left the set since the last request; an asset that moved between requests may be skipped or
returned twice. A cursor issued under another field or direction, an `UPDATED_AT` cursor issued under the other value
of `TrackingAssetStateFilter.primaryDeviceOnly`, or one that cannot be parsed, still fails with `VALIDATION_ERROR`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [TrackingAssetStateOrderField](#trackingassetstateorderfield)! |  |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! |  |

</details>

**Output types:**

<details>

<summary>TrackingAssetStateConnection</summary>

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[TrackingAssetStateEdge](#trackingassetstateedge)!]! |  |
| `nodes` | [[TrackingAssetState](#trackingassetstate)!]! |  |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### deviceState

Last known state of one device.

```graphql
deviceState(deviceId: ID!): TrackingDeviceState
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `deviceId` | `ID!` | The device. |

**Output types:**

<details>

<summary>TrackingDeviceState</summary>

Last known state of one device: position, link, motion and every reading, each as of the
message it last arrived in.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `device` | [Device](../../../business-data-repository/api-reference/devices/README.md#device)! |  |
| `carrier` | [Device](../../../business-data-repository/api-reference/devices/README.md#device) | For a device that does not report on its own — a BLE tag seen by a tracker — the device whose message carried the latest observation. Null for a device that reports on its own. A tag is its own `Device` in BDR, linked to the tracker it rides with through `DeviceRelation` (`Device.relationsFrom` / `relationsTo`), and it has a state of its own: a tag's readings arrive apart from the carrier's own fields, and this schema keeps them apart — a tag's temperature is `readings` on the tag's state, never on the tracker's. Its `position` is a carrier's position with `source: BLE`, chosen by the ordinary rule on `position`: the most recent carrying message that had coordinates. That is not always the latest observation — a stationary gateway reports tags in messages without a position — so `position.at` can lag `updatedAt`, and the position can come from an earlier message through a different tracker than `carrier` names. Its `connectivity` describes the latest carrier's link, and its `identifier` is the tag's own. This field says which carrier the latest observation came through, which the static relation cannot: a tag that rides between two trackers has two relations and one current carrier. Where it comes from: every observation records the device that saw the tag. It also records the tag's own `Device`, found when the observation arrives by the tag's registered identifiers and the carrier's `DeviceRelation`s. Both are values as of the observation, so a tag registered later leaves its earlier observations unattributed. |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Event time of the most recent message — the newest `updatedAt` across this state. Null for a device that has never reported (`TrackingConnectivityStatus.NEVER_REPORTED`), which is why it is nullable: a freshly registered tracker inside a page of asset states would otherwise null out the whole response through the non-null chain above it. |
| `connectivity` | [TrackingConnectivity](#trackingconnectivity)! |  |
| `position` | [TrackingPosition](#trackingposition) | The most recent position of any fitness except `NO_POSITION` and `NO_FIX`, the two that carry no coordinates — so a `LAST_KNOWN` repeat or a `WEAK_GNSS` fix is shown rather than nothing (equal event times are broken by fitness rank, then by a fixed order of the messages). Null for a device that has never sent one. Its age is `position.at`, not `updatedAt`: a heartbeat after it moves the latter and leaves the former. |
| `motion` | [TrackingMotion](#trackingmotion) |  |
| `readings` | [[TrackingReading](../readings.md#trackingreading)!] | Readings of this device: the newest value of every `(code, channel)` it has reported within the archive's retention, each with its own `updatedAt`. A client that does not know which fields a device has calls it without `codes` and gets all of them. Nothing is backfilled: a field the device reported only before the service began recording last values is absent until the device sends it again. `codes` narrows the list to those codes, every channel included. `maxAge` drops readings whose message is older than that; it is never required. A reading appears once the archive has stored the message that carried it, so it can trail `position` and `connectivity`, which come from the service's memory. Null, with a `SERVICE_UNAVAILABLE` error at this path, when the stored last readings cannot be read for this device; a retry may succeed. The rest of the state is still served, which is why the field is nullable while `TrackingDeviceStateSnapshot.readings` is not. |
| `lastEvents` | [[TrackingEvent](#trackingevent)!]! | The one-shot events of the most recent message that carried one, plus every level alarm still holding — those arrive collapsed, with `TrackingEvent.since` giving the moment the condition started rather than one object per repeat. Two retention rules, one per kind. A one-shot event stays until a message with another one-shot event replaces it; messages without events, and the repeats of a held alarm, leave it in place — otherwise a held overspeed alarm would evict an SOS within one report interval. A held alarm stays until a message arrives without it or nothing has been received for the alarm reset gap (`TrackingEvent.since`); reaching `OFFLINE` alone does not end it. |

</details>

---

### deviceStates

Last known state of several devices, in the order requested. Null for one not visible.

```graphql
deviceStates(deviceIds: [ID!]!): [TrackingDeviceState]!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `deviceIds` | `[ID!]!` | The devices. |

**Output types:**

<details>

<summary>TrackingDeviceState</summary>

Last known state of one device: position, link, motion and every reading, each as of the
message it last arrived in.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `device` | [Device](../../../business-data-repository/api-reference/devices/README.md#device)! |  |
| `carrier` | [Device](../../../business-data-repository/api-reference/devices/README.md#device) | For a device that does not report on its own — a BLE tag seen by a tracker — the device whose message carried the latest observation. Null for a device that reports on its own. A tag is its own `Device` in BDR, linked to the tracker it rides with through `DeviceRelation` (`Device.relationsFrom` / `relationsTo`), and it has a state of its own: a tag's readings arrive apart from the carrier's own fields, and this schema keeps them apart — a tag's temperature is `readings` on the tag's state, never on the tracker's. Its `position` is a carrier's position with `source: BLE`, chosen by the ordinary rule on `position`: the most recent carrying message that had coordinates. That is not always the latest observation — a stationary gateway reports tags in messages without a position — so `position.at` can lag `updatedAt`, and the position can come from an earlier message through a different tracker than `carrier` names. Its `connectivity` describes the latest carrier's link, and its `identifier` is the tag's own. This field says which carrier the latest observation came through, which the static relation cannot: a tag that rides between two trackers has two relations and one current carrier. Where it comes from: every observation records the device that saw the tag. It also records the tag's own `Device`, found when the observation arrives by the tag's registered identifiers and the carrier's `DeviceRelation`s. Both are values as of the observation, so a tag registered later leaves its earlier observations unattributed. |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Event time of the most recent message — the newest `updatedAt` across this state. Null for a device that has never reported (`TrackingConnectivityStatus.NEVER_REPORTED`), which is why it is nullable: a freshly registered tracker inside a page of asset states would otherwise null out the whole response through the non-null chain above it. |
| `connectivity` | [TrackingConnectivity](#trackingconnectivity)! |  |
| `position` | [TrackingPosition](#trackingposition) | The most recent position of any fitness except `NO_POSITION` and `NO_FIX`, the two that carry no coordinates — so a `LAST_KNOWN` repeat or a `WEAK_GNSS` fix is shown rather than nothing (equal event times are broken by fitness rank, then by a fixed order of the messages). Null for a device that has never sent one. Its age is `position.at`, not `updatedAt`: a heartbeat after it moves the latter and leaves the former. |
| `motion` | [TrackingMotion](#trackingmotion) |  |
| `readings` | [[TrackingReading](../readings.md#trackingreading)!] | Readings of this device: the newest value of every `(code, channel)` it has reported within the archive's retention, each with its own `updatedAt`. A client that does not know which fields a device has calls it without `codes` and gets all of them. Nothing is backfilled: a field the device reported only before the service began recording last values is absent until the device sends it again. `codes` narrows the list to those codes, every channel included. `maxAge` drops readings whose message is older than that; it is never required. A reading appears once the archive has stored the message that carried it, so it can trail `position` and `connectivity`, which come from the service's memory. Null, with a `SERVICE_UNAVAILABLE` error at this path, when the stored last readings cannot be read for this device; a retry may succeed. The rest of the state is still served, which is why the field is nullable while `TrackingDeviceStateSnapshot.readings` is not. |
| `lastEvents` | [[TrackingEvent](#trackingevent)!]! | The one-shot events of the most recent message that carried one, plus every level alarm still holding — those arrive collapsed, with `TrackingEvent.since` giving the moment the condition started rather than one object per repeat. Two retention rules, one per kind. A one-shot event stays until a message with another one-shot event replaces it; messages without events, and the repeats of a held alarm, leave it in place — otherwise a held overspeed alarm would evict an SOS within one report interval. A held alarm stays until a message arrives without it or nothing has been received for the alarm reset gap (`TrackingEvent.since`); reaching `OFFLINE` alone does not end it. |

</details>

---

### workspaceSettings

Tracking settings of the caller's workspace. Read-only.

```graphql
workspaceSettings: TrackingWorkspaceSettings!
```

**Output types:**

<details>

<summary>TrackingWorkspaceSettings</summary>

Tracking settings of one workspace.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `statusThresholds` | [TrackingStatusThresholds](#trackingstatusthresholds)! | The thresholds that derive `TrackingConnectivity.status` and `TrackingMotion.status` in this workspace. The same values apply to every device of the workspace, and today to every workspace. The timeline's `PLATFORM_DEFAULT` segmentation is these values too. |

</details>

---

## Objects

<a id="trackingposition"></a>

### TrackingPosition

| Field | Type | Description |
| ----- | ---- | ----------- |
| `point` | [GeoPoint](../../../business-data-repository/api-reference/geo-objects.md#geopoint)! | WGS-84 point. |
| `altitude` | `Float` | Metres above sea level. |
| `speed` | `Float` | Ground speed, km/h. |
| `heading` | `Float` | Course over ground, degrees clockwise from north. |
| `accuracy` | `Float` | Reported horizontal accuracy, metres. |
| `satellites` | `Int` |  |
| `hdop` | `Float` |  |
| `vdop` | `Float` |  |
| `pdop` | `Float` |  |
| `fixType` | [TrackingFixType](#trackingfixtype) | Kind of fix the device reported. |
| `source` | [TrackingPositionSource](#trackingpositionsource) |  |
| `fitness` | [TrackingFitness](#trackingfitness)! | Whether the point may be used for a track, and how. |
| `timeSource` | [TrackingTimeSource](#trackingtimesource) | Where `at` came from. `fitness` grades the coordinates, this grades the time: a point can be a clean satellite fix whose time the receiver replaced (`DEVICE_CORRECTED`) or a buffered record that had no time of its own (`SERVER`), and both then sit on the axis at the moment of upload rather than of observation. Null when the message did not say where its time came from or named a source `TrackingTimeSource` does not list. |
| `at` | [DateTime](../../../core-api-reference/common.md#datetime)! | Event time of the message this position came from — the moment the position is claimed for, and the time to age it by on a map. Always present: a message with a position has an event time by definition. In a state it equals `updatedAt` when the newest message carried a position, and lags it when heartbeats or readings-only reports came after. |
| `fixedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | When the fix was taken, as the device reported it — a raw observation, not a decoder's inference. For a fresh fix it simply equals `at`; for a repeated last-known point it is the time of that old fix, and for dead reckoning the time of the anchor. Null means one thing only: the protocol sent no separate fix time. So `fixedAt != null` says nothing about staleness — compare it with `at` for that. |

---

<a id="trackingconnectivity"></a>

### TrackingConnectivity

Link state of a device. Named `Connectivity`, not `Connection`, so it is not mistaken for a
Relay connection type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `status` | [TrackingConnectivityStatus](#trackingconnectivitystatus)! |  |
| `lastMessageAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Event time of the most recent message of any kind. |
| `lastPositionAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Event time of the most recent message that carried a position fit for a track — fitness `GNSS` or `NETWORK`, the points `TrackingTrackOptions.fitness` selects by default. A `LAST_KNOWN` repeat or a `WEAK_GNSS` fix moves `position.at` but not this. |
| `lastReceivedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Arrival time of the most recent message. |
| `protocol` | [Code](../../../core-api-reference/common.md#code) | Protocol code with generation, as stored: `teltonika_codec8e`, not the model family. Left as `Code` rather than an enum on purpose — there are over a hundred and the list grows with every new device model. |
| `transport` | [TrackingTransport](#trackingtransport) | Channel the message arrived over. |
| `identifier` | `String` | The identifier value as the device presented it, with its case preserved. The value alone: the namespace, for the minority of devices that have one, is in `identifierNamespace`, so this field matches the IMEI on the sticker for those devices too. |
| `identifierNamespace` | `String` | Namespace the identifier is unique within. Null when the identifier is globally unique. `String`, not `Code`: a namespace may contain `$`, and `Code` allows only letters, digits, `_`, `.` and `-`. A single device with an exotic namespace would otherwise fail coercion — and through the non-null chain `connectivity` → `devices` → `nodes`, take the whole page of states down with it. |
| `identifierType` | [DeviceIdType](../../../business-data-repository/api-reference/devices/README.md#deviceidtype) | Kind of identifier. Null when the message did not say or gave a kind `DeviceIdType` does not list. |

---

<a id="trackingmotion"></a>

### TrackingMotion

| Field | Type | Description |
| ----- | ---- | ----------- |
| `status` | [TrackingMotionStatus](#trackingmotionstatus)! |  |
| `since` | [DateTime](../../../core-api-reference/common.md#datetime) | When the current status began. |

---

<a id="trackingworkspacesettings"></a>

### TrackingWorkspaceSettings

Tracking settings of one workspace.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `statusThresholds` | [TrackingStatusThresholds](#trackingstatusthresholds)! | The thresholds that derive `TrackingConnectivity.status` and `TrackingMotion.status` in this workspace. The same values apply to every device of the workspace, and today to every workspace. The timeline's `PLATFORM_DEFAULT` segmentation is these values too. |

---

<a id="trackingstatusthresholds"></a>

### TrackingStatusThresholds

The values a device's connectivity and motion status are derived with. Read-only.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `offlineAfter` | [Duration](../../../core-api-reference/common.md#duration)! | Silent this long or longer since the last received message → `OFFLINE`. |
| `minStopDuration` | [Duration](../../../core-api-reference/common.md#duration)! | Standing still this long or longer → `PARKED`; shorter → `STOPPED`. |
| `movingSpeedThreshold` | `Float!` | Speed above this, km/h → `MOVING`. |
| `stopRadius` | `Float!` | Radius, metres, within which points count as the same place rather than movement. |

---

<a id="trackingevent"></a>

### TrackingEvent

An event carried by a message.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../../../core-api-reference/common.md#code) | Canonical platform code, e.g. `sos`, `harsh_braking`. Null when the vendor code is unmapped. |
| `vendorCode` | `String` | The code the device sent, verbatim. Null means the platform itself raised the event — there is no separate origin flag. `String` for the same reason as `identifierNamespace`: the code is kept exactly as the device sent it, and that alphabet is open, while `Code`'s is not. |
| `occurredAt` | [DateTime](../../../core-api-reference/common.md#datetime)! | When the event occurred. Equal to the message's event time unless the protocol reported a separate moment for the event — a fault code carries the time the fault was recorded. |
| `point` | [GeoPoint](../../../business-data-repository/api-reference/geo-objects.md#geopoint) | Where the event happened: the position of the message that carried it, since an event has no place of its own. Null when that message carried no coordinates. On `TrackingTrackPoint.events` it equals the point; on `lastEvents`, `TrackingTrack.events` and `TrackingStop.events` it is what lets an SOS stay on the map after the device has moved on. |
| `params` | [[TrackingReading](../readings.md#trackingreading)!]! | Event parameters — triggering input, threshold, duration — as readings, not as opaque JSON. Each parameter carries its name, type and unit, as any other reading does. Returning `JSON` here would force a client that already renders `TrackingReading` to write a second renderer, which is the split this schema exists to avoid. |
| `text` | `String` | Free-form text, including a device's reply to a command. |
| `commandId` | `ID` | The command that this event answers, when the platform matched the event to a command. No type in this API is a command, so `node(id:)` returns `null` for this ID. |
| `since` | [DateTime](../../../core-api-reference/common.md#datetime) | For a level alarm — an event a device re-sends in every message while the condition holds — the moment it started. `occurredAt` then carries the latest repeat, and the repeats in between are collapsed into this one object rather than returned individually. Null for a one-shot event. A held alarm stops being reported once a message arrives without it — protocols of this family send no paired `*_END` code, so absence is the only signal there is. It also stops once nothing has been received from the device for the alarm reset gap, a deployment setting: otherwise a truck that raised an alarm and then lost its link would show that alarm burning for a week while nothing at all was heard from it. The gap is independent of the `OFFLINE` threshold, so a device can read `OFFLINE` with its alarm still held. Which of the two ended it shows in the device's connectivity: a message without the alarm arrived when `connectivity.lastMessageAt` is later than the alarm's last `occurredAt`; nothing was received for the gap when `connectivity.lastReceivedAt` is older than the gap. |

---

<a id="trackingdevicestate"></a>

### TrackingDeviceState

Last known state of one device: position, link, motion and every reading, each as of the
message it last arrived in.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `device` | [Device](../../../business-data-repository/api-reference/devices/README.md#device)! |  |
| `carrier` | [Device](../../../business-data-repository/api-reference/devices/README.md#device) | For a device that does not report on its own — a BLE tag seen by a tracker — the device whose message carried the latest observation. Null for a device that reports on its own. A tag is its own `Device` in BDR, linked to the tracker it rides with through `DeviceRelation` (`Device.relationsFrom` / `relationsTo`), and it has a state of its own: a tag's readings arrive apart from the carrier's own fields, and this schema keeps them apart — a tag's temperature is `readings` on the tag's state, never on the tracker's. Its `position` is a carrier's position with `source: BLE`, chosen by the ordinary rule on `position`: the most recent carrying message that had coordinates. That is not always the latest observation — a stationary gateway reports tags in messages without a position — so `position.at` can lag `updatedAt`, and the position can come from an earlier message through a different tracker than `carrier` names. Its `connectivity` describes the latest carrier's link, and its `identifier` is the tag's own. This field says which carrier the latest observation came through, which the static relation cannot: a tag that rides between two trackers has two relations and one current carrier. Where it comes from: every observation records the device that saw the tag. It also records the tag's own `Device`, found when the observation arrives by the tag's registered identifiers and the carrier's `DeviceRelation`s. Both are values as of the observation, so a tag registered later leaves its earlier observations unattributed. |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Event time of the most recent message — the newest `updatedAt` across this state. Null for a device that has never reported (`TrackingConnectivityStatus.NEVER_REPORTED`), which is why it is nullable: a freshly registered tracker inside a page of asset states would otherwise null out the whole response through the non-null chain above it. |
| `connectivity` | [TrackingConnectivity](#trackingconnectivity)! |  |
| `position` | [TrackingPosition](#trackingposition) | The most recent position of any fitness except `NO_POSITION` and `NO_FIX`, the two that carry no coordinates — so a `LAST_KNOWN` repeat or a `WEAK_GNSS` fix is shown rather than nothing (equal event times are broken by fitness rank, then by a fixed order of the messages). Null for a device that has never sent one. Its age is `position.at`, not `updatedAt`: a heartbeat after it moves the latter and leaves the former. |
| `motion` | [TrackingMotion](#trackingmotion) |  |
| `readings` | [[TrackingReading](../readings.md#trackingreading)!] | Readings of this device: the newest value of every `(code, channel)` it has reported within the archive's retention, each with its own `updatedAt`. A client that does not know which fields a device has calls it without `codes` and gets all of them. Nothing is backfilled: a field the device reported only before the service began recording last values is absent until the device sends it again. `codes` narrows the list to those codes, every channel included. `maxAge` drops readings whose message is older than that; it is never required. A reading appears once the archive has stored the message that carried it, so it can trail `position` and `connectivity`, which come from the service's memory. Null, with a `SERVICE_UNAVAILABLE` error at this path, when the stored last readings cannot be read for this device; a retry may succeed. The rest of the state is still served, which is why the field is nullable while `TrackingDeviceStateSnapshot.readings` is not. |
| `lastEvents` | [[TrackingEvent](#trackingevent)!]! | The one-shot events of the most recent message that carried one, plus every level alarm still holding — those arrive collapsed, with `TrackingEvent.since` giving the moment the condition started rather than one object per repeat. Two retention rules, one per kind. A one-shot event stays until a message with another one-shot event replaces it; messages without events, and the repeats of a held alarm, leave it in place — otherwise a held overspeed alarm would evict an SOS within one report interval. A held alarm stays until a message arrives without it or nothing has been received for the alarm reset gap (`TrackingEvent.since`); reaching `OFFLINE` alone does not end it. |

**Arguments of `readings`**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `codes` | [[Code](../../../core-api-reference/common.md#code)!] | Only the readings of these codes, every channel of each code included. All readings when omitted. |
| `maxAge` | [Duration](../../../core-api-reference/common.md#duration) | Leaves out every reading whose message is older than this. Optional. |

**Arguments of `lastEvents`**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `codes` | [[Code](../../../core-api-reference/common.md#code)!] | Limit to these event codes. All when omitted. |

---

<a id="trackingassetstate"></a>

### TrackingAssetState

Tracking state of an asset: the states of the devices linked to it, side by side.

Deliberately NOT merged into one state. An asset can carry several devices reporting the same
field — two temperature sensors, two trackers — and any merge policy (primary wins, newest wins)
either hides a value or makes it jump between sources. The asset exposes the list and the client
decides.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `asset` | [Asset](../../../business-data-repository/api-reference/assets/README.md#asset)! |  |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Newest `updatedAt` across the asset's devices; under `TrackingAssetStateFilter.primaryDeviceOnly`, the primary device's. Null when none of those devices has ever reported, or, under that flag, when the asset has no primary device the caller can see. |
| `devices` | [[TrackingDeviceState](#trackingdevicestate)!]! | State of every device linked to the asset. |
| `primaryDevice` | [TrackingDeviceState](#trackingdevicestate) | State of `Asset.primaryDevice`, for the common case of one tracker per asset. The same object also appears in `devices`; this is a pointer, not a second state. |

---

## Inputs

<a id="trackingassetstatefilter"></a>

### TrackingAssetStateFilter

Filters over asset states; the conditions combine with AND, except that `assetGroupIds` and `ungrouped` form one
condition by OR. Since an asset's state is a list, every device-level
condition (`connectivityStatus`, `motionStatus`, `updatedSince`) matches when **any** of the asset's devices satisfies
it, unless `primaryDeviceOnly` is true.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetIds` | `[ID!]` | Only these assets. An empty list sets no condition and answers every asset the other conditions match, so a client that builds the list from a search that found nothing should skip the query. In `TrackingAssetStateSubscriptionInput.assetIds` an empty list follows no asset. |
| `assetGroupIds` | `[ID!]` | Assets in any of these groups. A group counts only while the caller can see it and it is not deleted, so the id of a hidden or deleted group matches nothing. With `ungrouped: true`, assets in no group match as well. An empty list sets no condition. |
| `ungrouped` | `Boolean` | Assets in no group the caller can see; a group hidden from the caller or deleted does not count. Combined with `assetGroupIds` by OR, so "no group" can be one more option in a choice of groups. False or absent sets no condition. |
| `typeIds` | `[ID!]` | Assets of any of these types. An empty list sets no condition. |
| `titleContains` | `String` | Partial match on the asset's title, case-insensitive, as `AssetFilter.titleContains`. |
| `connectivityStatus` | [[TrackingConnectivityStatus](#trackingconnectivitystatus)!] |  |
| `motionStatus` | [[TrackingMotionStatus](#trackingmotionstatus)!] |  |
| `updatedSince` | [DateTime](../../../core-api-reference/common.md#datetime) | Only assets where some device reported at or after this moment. |
| `primaryDeviceOnly` | `Boolean` | Check `connectivityStatus`, `motionStatus` and `updatedSince` on `Asset.primaryDevice` only: an asset's status is its primary device's status. An asset without a primary device, or whose primary device the caller cannot see, matches none of them. Also takes `TrackingAssetState.updatedAt`, and with it the `UPDATED_AT` order, from the primary device. False or absent: any device, as this type describes. |

---

<a id="trackingassetstateorder"></a>

### TrackingAssetStateOrder

Order of an asset-state page. Only asset-side keys are offered as stable orders; `UPDATED_AT`
moves with every message, so a page boundary read under it may not hold on the next request — use
it for a one-shot "most recently active" list, not for walking a large set page by page.

A cursor under `UPDATED_AT` carries the anchor asset's `(updatedAt, assetId)`, and the next page
starts after that pair in the requested order. It never fails because the anchor asset has moved or
left the set since the last request; an asset that moved between requests may be skipped or
returned twice. A cursor issued under another field or direction, an `UPDATED_AT` cursor issued under the other value
of `TrackingAssetStateFilter.primaryDeviceOnly`, or one that cannot be parsed, still fails with `VALIDATION_ERROR`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [TrackingAssetStateOrderField](#trackingassetstateorderfield)! |  |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! |  |

---

## Enums

<a id="trackingfixtype"></a>

### TrackingFixType

Kind of fix. The correction axis (DGPS, RTK) is not in it.

A field of this type is null when the message did not say or gave a value this enum does not list,
as is one of `TrackingPositionSource`.

| Value | Description |
| ----- | ----------- |
| `NO_FIX` | The device tried and failed. |
| `FIX` | A fix whose dimensionality the protocol does not say. |
| `FIX_2D` |  |
| `FIX_3D` |  |
| `DEAD_RECKONING` | Position carried forward by dead reckoning. |
| `LAST_KNOWN` | A repeat of the last known point. |

---

<a id="trackingtimesource"></a>

### TrackingTimeSource

Where a message's event time came from. Decides how far the order of points can be trusted:
`GNSS` depends on neither the device clock nor the network, while `SERVER` and `DEVICE_CORRECTED`
mean the time on the axis is the receiver's, not the observation's. A field of this type is null
when the message did not say or gave a value this enum does not list.

| Value | Description |
| ----- | ----------- |
| `DEVICE` | The device's own clock. |
| `GNSS` | Time from the satellite solution. |
| `NETWORK` | Operator network or NTP on the device. |
| `SERVER` | Set by the receiver because the device sent no time. The point sits at its moment of upload. |
| `TACHOGRAPH` | Tachograph clock, a subsystem with its own drift and offset. |
| `DEVICE_CORRECTED` | The device sent a time and the receiver replaced it — a clock out of the plausibility window, or a rolled-over protocol counter. The point sits at its moment of upload, whatever the fix quality. |

---

<a id="trackingpositionsource"></a>

### TrackingPositionSource

Where the position came from.

| Value | Description |
| ----- | ----------- |
| `GNSS` |  |
| `DEVICE_LBS` | The device resolved its own position from radio surroundings. |
| `LBS` | The platform resolved it, method unstated. |
| `LBS_CELL` |  |
| `LBS_WIFI` |  |
| `IRIDIUM` |  |
| `LORAWAN` |  |
| `NETWORK` | Reported by the network the device is attached to. |
| `BLE` | Position of a BLE gateway that saw the tag. |
| `MANUAL` |  |
| `FUSED` | Fused from several sources by the device. |

---

<a id="trackingfitness"></a>

### TrackingFitness

Suitability of a point, decided once when the point is stored rather than by each reader.

| Value | Description |
| ----- | ----------- |
| `NO_POSITION` | No position in the message at all — heartbeat, readings-only report. |
| `NO_FIX` | The device tried and failed to get a fix. |
| `LAST_KNOWN` | A repeat of the last known point — legitimate on a map, must not enter distance maths. |
| `WEAK_GNSS` | A satellite-receiver solution below track grade: fewer than three satellites, no fix, or dead reckoning. |
| `GNSS` | Usable GNSS fix. |
| `NETWORK` | A usable non-GNSS radio solution: cells, Wi-Fi, operator network, LoRaWAN, BLE, satellite Doppler such as Iridium. |
| `UNKNOWN_SRC` | A source the platform does not grade: unnamed, or manual. |

---

<a id="trackingtransport"></a>

### TrackingTransport

Channel a message arrived over. A closed list, so it is an enum rather than a `Code`. A field of
this type is null when the message did not say or gave a value this enum does not list.

| Value | Description |
| ----- | ----------- |
| `TCP` |  |
| `UDP` |  |
| `HTTP` |  |
| `GRPC` |  |
| `MQTT` |  |
| `WEBSOCKET` |  |
| `SMS` |  |
| `SATELLITE` |  |
| `INTERNAL` | Raised inside the platform rather than received. |
| `HTTP_POLLING` | Channels where the platform goes after the data rather than receiving it. |
| `KAFKA_CLIENT` |  |
| `RABBITMQ_CLIENT` |  |
| `WEBSOCKET_CLIENT` |  |

---

<a id="trackingconnectivitystatus"></a>

### TrackingConnectivityStatus

Whether a device is currently connected. Derived from the time of its last message and never
stored. Two values are connected and not connected: `ONLINE` is the first, `OFFLINE` and
`NEVER_REPORTED` are the second.

Not to be confused with `Device.status` (`DeviceStatus`), which is the administrative lifecycle of
the device — registered, active, retired — held as a catalog item in BDR, changing rarely and
deliberately. This one changes whenever a device goes quiet, which is why it is computed rather
than written: making "online" a lifecycle value would have every device write to BDR each time its
link flapped.

| Value | Description |
| ----- | ----------- |
| `ONLINE` | Connected: a message arrived within `TrackingStatusThresholds.offlineAfter`. Measured from the arrival of the last message of any kind (`TrackingConnectivity.lastReceivedAt`), not from the last one that carried coordinates. |
| `OFFLINE` | Not connected: nothing has arrived for `TrackingStatusThresholds.offlineAfter` or longer, measured from the same moment as `ONLINE`. It labels connectivity only; a held alarm ends on a separate setting (`TrackingEvent.since`). |
| `NEVER_REPORTED` | Not connected: the device has never sent a message, so there is no last message to measure from. A client shows it as not connected, the same as `OFFLINE`. |

---

<a id="trackingmotionstatus"></a>

### TrackingMotionStatus

**Values:** `MOVING`, `STOPPED`, `PARKED`, `UNKNOWN`

---

<a id="trackingreportreason"></a>

### TrackingReportReason

Why the device sent this point. A closed list, same as `TrackingTransport`; a field of this type is
null when the message did not say or gave a value this enum does not list.

| Value | Description |
| ----- | ----------- |
| `TIME` | Reporting timer elapsed. |
| `DISTANCE` | Distance since the last point exceeded the threshold. |
| `ANGLE` | Course change exceeded the threshold. |
| `IGNITION` |  |
| `HEARTBEAT` |  |
| `ALARM` |  |
| `STATIC_REPEAT` | Repeat of the last known position while stationary. |
| `POWER_ON` |  |
| `MANUAL` |  |
| `NETWORK_RECOVERED` |  |

---

<a id="trackingassetstateorderfield"></a>

### TrackingAssetStateOrderField

| Value | Description |
| ----- | ----------- |
| `ASSET_TITLE` | By `Asset.title`, then by asset id as the tie-breaker. |
| `UPDATED_AT` | By `TrackingAssetState.updatedAt`; an asset whose `updatedAt` is null sorts as null (`OrderDirection`). |

---

## Pagination types

<a id="trackingassetstateconnection"></a>

### TrackingAssetStateConnection

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[TrackingAssetStateEdge](#trackingassetstateedge)!]! |  |
| `nodes` | [[TrackingAssetState](#trackingassetstate)!]! |  |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="trackingassetstateedge"></a>

### TrackingAssetStateEdge

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge, used for pagination. |
| `node` | [TrackingAssetState](#trackingassetstate)! |  |

---

## See also

* [Pagination](../../../pagination.md)
* [Filtering and sorting](../../../filtering-and-sorting/README.md)
* [Error handling](../../../error-handling.md)
* [Optimistic locking](../../../optimistic-locking.md)
* [Limits](../../../limits.md)
