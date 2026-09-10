---
description: >-
  Reference for live tracking state: the last known position, connectivity,
  motion, readings and events of a device, the per-device states of an asset,
  and the queries that read them.
---

# Live state

{% include "../../../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

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
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Newest `updatedAt` across the asset's devices. Null when no device has ever reported. |
| `devices` | [[TrackingDeviceState](#trackingdevicestate)!]! | State of every device linked to the asset. |
| `primaryDevice` | [TrackingDeviceState](#trackingdevicestate) | State of `Asset.primaryDevice`, for the common case of one tracker per asset. The same object also appears in `devices`; this is a pointer, not a second state. |

</details>

---

### assetStates

Tracking state of the workspace's assets.

```graphql
assetStates(
    workspaceId: ID!
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
| `workspaceId` | `ID!` |  |
| `filter` | `TrackingAssetStateFilter` |  |
| `orderBy` | `TrackingAssetStateOrder` |  |
| `first` | `Int` |  |
| `after` | `String` |  |
| `last` | `Int` |  |
| `before` | `String` |  |

**Input types:**

<details>

<summary>TrackingAssetStateFilter</summary>

Filters over asset states. Since an asset's state is a list, every device-level condition here
matches when **any** of the asset's devices satisfies it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetIds` | `[ID!]` |  |
| `assetGroupIds` | `[ID!]` |  |
| `connectivityStatus` | [[TrackingConnectivityStatus](#trackingconnectivitystatus)!] |  |
| `motionStatus` | [[TrackingMotionStatus](#trackingmotionstatus)!] |  |
| `updatedSince` | [DateTime](../../../core-api-reference/common.md#datetime) | Only assets where some device reported at or after this moment. |
| `within` | [GeoJSON](../../../core-api-reference/common.md#geojson) | Only assets where some device's `position` falls inside this area — the same position the map shows, selected by the rule on `TrackingDeviceState.position`, so a `LAST_KNOWN` repeat inside the area matches. The filter answers where the device says it is, not where a track-grade fix last put it; for the latter compare `connectivity.lastPositionAt`. |

</details>

<details>

<summary>TrackingAssetStateOrder</summary>

Order of an asset-state page. Only asset-side keys are offered as stable orders; `UPDATED_AT`
moves with every message, so a page boundary read under it may not hold on the next request — use
it for a one-shot "most recently active" list, not for walking a large set page by page.

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
| `carrier` | [Device](../../../business-data-repository/api-reference/devices/README.md#device) | For a device that does not report on its own — a BLE tag seen by a tracker — the device whose message carried the latest observation. Null for a device that reports on its own. A tag is its own `Device` in BDR, linked to the tracker it rides with through `DeviceRelation` (`Device.relationsFrom` / `relationsTo`), and it has a state of its own: NTF keeps a tag's readings in the carrier message's `ble_tags`, apart from the carrier's own fields (SPEC §6.6), and this schema keeps them apart the same way — a tag's temperature is `readings` on the tag's state, never on the tracker's. Its `position` is a carrier's position with `source: BLE`, chosen by the ordinary rule on `position`: the most recent carrying message that had coordinates. That is not always the latest observation — a stationary gateway reports tags in messages without a position (SPEC §6.4.3) — so `position.at` can lag `updatedAt`, and the position can come from an earlier message through a different tracker than `carrier` names. Its `connectivity` describes the latest carrier's link, and its `identifier` is the tag's own. This field says which carrier the latest observation came through, which the static relation cannot: a tag that rides between two trackers has two relations and one current carrier. Where it comes from: every observation carries it. On the wire it is the `device` of the message whose `ble_tags` held the tag; in the archive it is `telemetry.ble_tags.device_id`, the device that saw the tag. The tag's own `Device` is in the same observation: the receiver resolves it against BDR at identification, by the tag's registered identifiers and the carrier's `DeviceRelation`s, into `BleTag.device_id` (NTF 1.2), stored as `ble_tags.tag_device_id`. Both are values as of the observation, so a tag registered later leaves its earlier observations unattributed. |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Event time of the most recent message — the newest `updatedAt` across this state. Null for a device that has never reported (`TrackingConnectivityStatus.NEVER_REPORTED`), which is why it is nullable: a freshly registered tracker inside a page of asset states would otherwise null out the whole response through the non-null chain above it. |
| `connectivity` | [TrackingConnectivity](#trackingconnectivity)! |  |
| `position` | [TrackingPosition](#trackingposition) | The most recent position of any fitness except `NO_POSITION` and `NO_FIX`, the two that carry no coordinates — so a `LAST_KNOWN` repeat or a `WEAK_GNSS` fix is shown rather than nothing (STORAGE.md §"Как отобрать пригодные точки", the "where is the device now" query; equal event times are broken by fitness rank, then by message id). Null for a device that has never sent one. Its age is `position.at`, not `updatedAt`: a heartbeat after it moves the latter and leaves the former. |
| `motion` | [TrackingMotion](#trackingmotion) |  |
| `readings` | [[TrackingReading](../readings.md#trackingreading)!]! | Readings of this device, in two tiers. Called without `codes`, this returns the **hot set** — the fields the receiver keeps in memory, listed by `TrackingQuery.hotFieldCodes`. That covers position-adjacent and frequently reported values and costs nothing extra. Naming a code that is not hot — `obd_vin`, `driver_card`, `fw_version` — makes the service look the last value up in the archive, per code. That is the only way to get a field a device last sent months ago, and it is why the lookup is opt-in rather than implicit: an unbounded "give me everything ever known" would scan back over every field name for every device on the page. **A cold lookup is bounded, and the bound is not optional.** Readings live in map columns, and the shipped schema carries no `mapKeys` index (STORAGE.md §Показания), so a search for a code reads every granule it walks; the archive is partitioned by day with a three-year TTL, and a device that never sent `obd_vin` would be scanned to the end of it. So `maxAge` is **required** whenever `codes` names anything outside `hotFieldCodes`, and the number of cold codes per request is capped — over either limit the field fails with `VALIDATION_ERROR` rather than quietly costing a table scan per device per page. `maxAge` drops readings older than that; it applies to both tiers and is optional for hot ones. |
| `lastEvents` | [[TrackingEvent](#trackingevent)!]! | The one-shot events of the most recent message that carried one, plus every level alarm still holding — those arrive collapsed, with `TrackingEvent.since` giving the moment the condition started rather than one object per repeat. Two retention rules, one per kind. A one-shot event stays until a message with another one-shot event replaces it; messages without events, and the repeats of a held alarm, leave it in place — otherwise a held overspeed alarm would evict an SOS within one report interval. A held alarm stays until a message arrives without it or the device goes `OFFLINE` (`TrackingEvent.since`). |

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
| `carrier` | [Device](../../../business-data-repository/api-reference/devices/README.md#device) | For a device that does not report on its own — a BLE tag seen by a tracker — the device whose message carried the latest observation. Null for a device that reports on its own. A tag is its own `Device` in BDR, linked to the tracker it rides with through `DeviceRelation` (`Device.relationsFrom` / `relationsTo`), and it has a state of its own: NTF keeps a tag's readings in the carrier message's `ble_tags`, apart from the carrier's own fields (SPEC §6.6), and this schema keeps them apart the same way — a tag's temperature is `readings` on the tag's state, never on the tracker's. Its `position` is a carrier's position with `source: BLE`, chosen by the ordinary rule on `position`: the most recent carrying message that had coordinates. That is not always the latest observation — a stationary gateway reports tags in messages without a position (SPEC §6.4.3) — so `position.at` can lag `updatedAt`, and the position can come from an earlier message through a different tracker than `carrier` names. Its `connectivity` describes the latest carrier's link, and its `identifier` is the tag's own. This field says which carrier the latest observation came through, which the static relation cannot: a tag that rides between two trackers has two relations and one current carrier. Where it comes from: every observation carries it. On the wire it is the `device` of the message whose `ble_tags` held the tag; in the archive it is `telemetry.ble_tags.device_id`, the device that saw the tag. The tag's own `Device` is in the same observation: the receiver resolves it against BDR at identification, by the tag's registered identifiers and the carrier's `DeviceRelation`s, into `BleTag.device_id` (NTF 1.2), stored as `ble_tags.tag_device_id`. Both are values as of the observation, so a tag registered later leaves its earlier observations unattributed. |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Event time of the most recent message — the newest `updatedAt` across this state. Null for a device that has never reported (`TrackingConnectivityStatus.NEVER_REPORTED`), which is why it is nullable: a freshly registered tracker inside a page of asset states would otherwise null out the whole response through the non-null chain above it. |
| `connectivity` | [TrackingConnectivity](#trackingconnectivity)! |  |
| `position` | [TrackingPosition](#trackingposition) | The most recent position of any fitness except `NO_POSITION` and `NO_FIX`, the two that carry no coordinates — so a `LAST_KNOWN` repeat or a `WEAK_GNSS` fix is shown rather than nothing (STORAGE.md §"Как отобрать пригодные точки", the "where is the device now" query; equal event times are broken by fitness rank, then by message id). Null for a device that has never sent one. Its age is `position.at`, not `updatedAt`: a heartbeat after it moves the latter and leaves the former. |
| `motion` | [TrackingMotion](#trackingmotion) |  |
| `readings` | [[TrackingReading](../readings.md#trackingreading)!]! | Readings of this device, in two tiers. Called without `codes`, this returns the **hot set** — the fields the receiver keeps in memory, listed by `TrackingQuery.hotFieldCodes`. That covers position-adjacent and frequently reported values and costs nothing extra. Naming a code that is not hot — `obd_vin`, `driver_card`, `fw_version` — makes the service look the last value up in the archive, per code. That is the only way to get a field a device last sent months ago, and it is why the lookup is opt-in rather than implicit: an unbounded "give me everything ever known" would scan back over every field name for every device on the page. **A cold lookup is bounded, and the bound is not optional.** Readings live in map columns, and the shipped schema carries no `mapKeys` index (STORAGE.md §Показания), so a search for a code reads every granule it walks; the archive is partitioned by day with a three-year TTL, and a device that never sent `obd_vin` would be scanned to the end of it. So `maxAge` is **required** whenever `codes` names anything outside `hotFieldCodes`, and the number of cold codes per request is capped — over either limit the field fails with `VALIDATION_ERROR` rather than quietly costing a table scan per device per page. `maxAge` drops readings older than that; it applies to both tiers and is optional for hot ones. |
| `lastEvents` | [[TrackingEvent](#trackingevent)!]! | The one-shot events of the most recent message that carried one, plus every level alarm still holding — those arrive collapsed, with `TrackingEvent.since` giving the moment the condition started rather than one object per repeat. Two retention rules, one per kind. A one-shot event stays until a message with another one-shot event replaces it; messages without events, and the repeats of a held alarm, leave it in place — otherwise a held overspeed alarm would evict an SOS within one report interval. A held alarm stays until a message arrives without it or the device goes `OFFLINE` (`TrackingEvent.since`). |

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
| `fitness` | [TrackingFitness](#trackingfitness)! | Whether the point may be used for a track, and how (telemetry.records.track_fitness). |
| `timeSource` | [TrackingTimeSource](#trackingtimesource) | Where `at` came from (`telemetry.records.event_time_source`). `fitness` grades the coordinates, this grades the time: a point can be a clean satellite fix whose time the receiver replaced (`DEVICE_CORRECTED`) or a buffered record that had no time of its own (`SERVER`), and both then sit on the axis at the moment of upload rather than of observation (NTF SPEC §6.3). Null where the archive holds `UNSPECIFIED`. |
| `at` | [DateTime](../../../core-api-reference/common.md#datetime)! | Event time of the message this position came from — the moment the position is claimed for (NTF SPEC §6.3), and the time to age it by on a map. Always present: a message with a position has an event time by definition. In a state it equals `updatedAt` when the newest message carried a position, and lags it when heartbeats or readings-only reports came after. |
| `fixedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | When the fix was taken, as the device reported it — a raw observation, not a decoder's inference (NTF SPEC §6.3). For a fresh fix it simply equals `at`; for a repeated last-known point it is the time of that old fix, and for dead reckoning the time of the anchor. Null means one thing only: the protocol sent no separate fix time. So `fixedAt != null` says nothing about staleness — compare it with `at` for that. |

---

<a id="trackingconnectivity"></a>

### TrackingConnectivity

Link state of a device. Named `Connectivity`, not `Connection`, so it is not mistaken for a
Relay connection type.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `status` | [TrackingConnectivityStatus](#trackingconnectivitystatus)! |  |
| `lastMessageAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Event time of the most recent message of any kind. |
| `lastPositionAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Event time of the most recent message that carried a position fit for a track — fitness `GNSS` or `NETWORK`, the archive's track filter (STORAGE.md §"Как отобрать пригодные точки"). A `LAST_KNOWN` repeat or a `WEAK_GNSS` fix moves `position.at` but not this. |
| `lastReceivedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Arrival time of the most recent message (`UUIDv7ToDateTime(message_id)`). |
| `protocol` | [Code](../../../core-api-reference/common.md#code) | Protocol code with generation, as stored: `teltonika_codec8e`, not the model family. Left as `Code` rather than an enum on purpose — there are over a hundred and the list grows with every new device model (NTF SPEC §5.3), which is exactly why the format keeps it a string too. |
| `transport` | [TrackingTransport](#trackingtransport) | Channel the message arrived over. |
| `identifier` | `String` | The identifier value as the device presented it, with its case preserved. The archive stores value and uniqueness namespace packed into one column with escaping (`schema.sql` §identifier); this field is the unpacked value, and `identifierNamespace` the unpacked namespace. Returning the packed form would hand callers a string that does not match the IMEI on the sticker for the minority of devices that have a namespace. |
| `identifierNamespace` | `String` | Namespace the identifier is unique within. Null when the identifier is globally unique. `String`, not `Code`: NTF permits `$` anywhere in either half of the packed identifier (SPEC §8.2, receiver obligations) and `Code` allows only letters, digits, `_`, `.` and `-`. A single device with an exotic namespace would otherwise fail coercion — and through the non-null chain `connectivity` → `devices` → `nodes`, take the whole page of states down with it. |
| `identifierType` | [DeviceIdType](../../../business-data-repository/api-reference/devices/README.md#deviceidtype) | Kind of identifier. Null where the archive holds `UNSPECIFIED`. |

---

<a id="trackingmotion"></a>

### TrackingMotion

| Field | Type | Description |
| ----- | ---- | ----------- |
| `status` | [TrackingMotionStatus](#trackingmotionstatus)! |  |
| `since` | [DateTime](../../../core-api-reference/common.md#datetime) | When the current status began. |

---

<a id="trackingevent"></a>

### TrackingEvent

An event carried by a message (NTF SPEC §6.5).

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../../../core-api-reference/common.md#code) | Canonical platform code, e.g. `sos`, `harsh_braking`. Null when the vendor code is unmapped. |
| `vendorCode` | `String` | The code the device sent, verbatim. Null means the platform itself raised the event (NTF SPEC §6.5) — there is no separate origin flag. `String` for the same reason as `identifierNamespace`: SPEC §6.5 requires storing exactly what the device sent, and that alphabet is open, while `Code`'s is not. |
| `occurredAt` | [DateTime](../../../core-api-reference/common.md#datetime)! | When the event occurred. Equal to the message's event time unless the protocol reported a separate moment for the event — a fault code carries the time the fault was recorded. |
| `point` | [GeoPoint](../../../business-data-repository/api-reference/geo-objects.md#geopoint) | Where the event happened: the position of the message that carried it, since an event has no place of its own (NTF SPEC §6.5). Null when that message carried no coordinates. On `TrackingTrackPoint.events` it equals the point; on `lastEvents`, `TrackingTrack.events` and `TrackingStop.events` it is what lets an SOS stay on the map after the device has moved on. |
| `params` | [[TrackingReading](../readings.md#trackingreading)!]! | Event parameters — triggering input, threshold, duration — as readings, not as opaque JSON. The archive already stores them as a canonical `StructValue` with each parameter's name, type and unit, and NTF SPEC §6.5 calls them "ordinary fields, as everywhere else". Returning `JSON` here would force a client that already renders `TrackingReading` to write a second renderer, which is the split this schema exists to avoid. |
| `text` | `String` | Free-form text, including a device's reply to a command. |
| `commandId` | [UUID](../../../core-api-reference/common.md#uuid) | The command this event answers, when the receiver matched one. `UUID`, not `ID`, and deliberately: commands are a separate surface this contract does not create (§8). Every `ID` in this schema is a platform UUIDv8 carrying an entity-type discriminator, so declaring this as `ID` would oblige a command surface that has not been designed to mint its identifiers that way, and would promise `node(id:)` resolution this schema cannot deliver. `UUID` is the kernel's scalar for exactly this — an identifier owned by a system outside the API. |
| `since` | [DateTime](../../../core-api-reference/common.md#datetime) | For a level alarm — an event a device re-sends in every message while the condition holds — the moment it started. `occurredAt` then carries the latest repeat, and the repeats in between are collapsed into this one object rather than returned individually (SPEC §6.5: collapsing "is the job of a consumer that holds state", and this service is that consumer). Null for a one-shot event. A held alarm stops being reported once a message arrives without it — protocols of this family send no paired `*_END` code, so absence is the only signal there is. It also stops when the device's `connectivity.status` reaches `OFFLINE`: otherwise a truck that raised an alarm and then lost its link would show that alarm burning for a week while nothing at all was heard from it. Which of the two ended it is visible in `occurredAt` against `connectivity.lastMessageAt`. |

---

<a id="trackingdevicestate"></a>

### TrackingDeviceState

Last known state of one device: position, link, motion and every reading, each as of the
message it last arrived in.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `device` | [Device](../../../business-data-repository/api-reference/devices/README.md#device)! |  |
| `carrier` | [Device](../../../business-data-repository/api-reference/devices/README.md#device) | For a device that does not report on its own — a BLE tag seen by a tracker — the device whose message carried the latest observation. Null for a device that reports on its own. A tag is its own `Device` in BDR, linked to the tracker it rides with through `DeviceRelation` (`Device.relationsFrom` / `relationsTo`), and it has a state of its own: NTF keeps a tag's readings in the carrier message's `ble_tags`, apart from the carrier's own fields (SPEC §6.6), and this schema keeps them apart the same way — a tag's temperature is `readings` on the tag's state, never on the tracker's. Its `position` is a carrier's position with `source: BLE`, chosen by the ordinary rule on `position`: the most recent carrying message that had coordinates. That is not always the latest observation — a stationary gateway reports tags in messages without a position (SPEC §6.4.3) — so `position.at` can lag `updatedAt`, and the position can come from an earlier message through a different tracker than `carrier` names. Its `connectivity` describes the latest carrier's link, and its `identifier` is the tag's own. This field says which carrier the latest observation came through, which the static relation cannot: a tag that rides between two trackers has two relations and one current carrier. Where it comes from: every observation carries it. On the wire it is the `device` of the message whose `ble_tags` held the tag; in the archive it is `telemetry.ble_tags.device_id`, the device that saw the tag. The tag's own `Device` is in the same observation: the receiver resolves it against BDR at identification, by the tag's registered identifiers and the carrier's `DeviceRelation`s, into `BleTag.device_id` (NTF 1.2), stored as `ble_tags.tag_device_id`. Both are values as of the observation, so a tag registered later leaves its earlier observations unattributed. |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Event time of the most recent message — the newest `updatedAt` across this state. Null for a device that has never reported (`TrackingConnectivityStatus.NEVER_REPORTED`), which is why it is nullable: a freshly registered tracker inside a page of asset states would otherwise null out the whole response through the non-null chain above it. |
| `connectivity` | [TrackingConnectivity](#trackingconnectivity)! |  |
| `position` | [TrackingPosition](#trackingposition) | The most recent position of any fitness except `NO_POSITION` and `NO_FIX`, the two that carry no coordinates — so a `LAST_KNOWN` repeat or a `WEAK_GNSS` fix is shown rather than nothing (STORAGE.md §"Как отобрать пригодные точки", the "where is the device now" query; equal event times are broken by fitness rank, then by message id). Null for a device that has never sent one. Its age is `position.at`, not `updatedAt`: a heartbeat after it moves the latter and leaves the former. |
| `motion` | [TrackingMotion](#trackingmotion) |  |
| `readings` | [[TrackingReading](../readings.md#trackingreading)!]! | Readings of this device, in two tiers. Called without `codes`, this returns the **hot set** — the fields the receiver keeps in memory, listed by `TrackingQuery.hotFieldCodes`. That covers position-adjacent and frequently reported values and costs nothing extra. Naming a code that is not hot — `obd_vin`, `driver_card`, `fw_version` — makes the service look the last value up in the archive, per code. That is the only way to get a field a device last sent months ago, and it is why the lookup is opt-in rather than implicit: an unbounded "give me everything ever known" would scan back over every field name for every device on the page. **A cold lookup is bounded, and the bound is not optional.** Readings live in map columns, and the shipped schema carries no `mapKeys` index (STORAGE.md §Показания), so a search for a code reads every granule it walks; the archive is partitioned by day with a three-year TTL, and a device that never sent `obd_vin` would be scanned to the end of it. So `maxAge` is **required** whenever `codes` names anything outside `hotFieldCodes`, and the number of cold codes per request is capped — over either limit the field fails with `VALIDATION_ERROR` rather than quietly costing a table scan per device per page. `maxAge` drops readings older than that; it applies to both tiers and is optional for hot ones. |
| `lastEvents` | [[TrackingEvent](#trackingevent)!]! | The one-shot events of the most recent message that carried one, plus every level alarm still holding — those arrive collapsed, with `TrackingEvent.since` giving the moment the condition started rather than one object per repeat. Two retention rules, one per kind. A one-shot event stays until a message with another one-shot event replaces it; messages without events, and the repeats of a held alarm, leave it in place — otherwise a held overspeed alarm would evict an SOS within one report interval. A held alarm stays until a message arrives without it or the device goes `OFFLINE` (`TrackingEvent.since`). |

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
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Newest `updatedAt` across the asset's devices. Null when no device has ever reported. |
| `devices` | [[TrackingDeviceState](#trackingdevicestate)!]! | State of every device linked to the asset. |
| `primaryDevice` | [TrackingDeviceState](#trackingdevicestate) | State of `Asset.primaryDevice`, for the common case of one tracker per asset. The same object also appears in `devices`; this is a pointer, not a second state. |

---

## Inputs

<a id="trackingassetstatefilter"></a>

### TrackingAssetStateFilter

Filters over asset states. Since an asset's state is a list, every device-level condition here
matches when **any** of the asset's devices satisfies it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetIds` | `[ID!]` |  |
| `assetGroupIds` | `[ID!]` |  |
| `connectivityStatus` | [[TrackingConnectivityStatus](#trackingconnectivitystatus)!] |  |
| `motionStatus` | [[TrackingMotionStatus](#trackingmotionstatus)!] |  |
| `updatedSince` | [DateTime](../../../core-api-reference/common.md#datetime) | Only assets where some device reported at or after this moment. |
| `within` | [GeoJSON](../../../core-api-reference/common.md#geojson) | Only assets where some device's `position` falls inside this area — the same position the map shows, selected by the rule on `TrackingDeviceState.position`, so a `LAST_KNOWN` repeat inside the area matches. The filter answers where the device says it is, not where a track-grade fix last put it; for the latter compare `connectivity.lastPositionAt`. |

---

<a id="trackingassetstateorder"></a>

### TrackingAssetStateOrder

Order of an asset-state page. Only asset-side keys are offered as stable orders; `UPDATED_AT`
moves with every message, so a page boundary read under it may not hold on the next request — use
it for a one-shot "most recently active" list, not for walking a large set page by page.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [TrackingAssetStateOrderField](#trackingassetstateorderfield)! |  |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! |  |

---

## Enums

<a id="trackingfixtype"></a>

### TrackingFixType

Kind of fix, as `navixy.telemetry.v1.FixType`. The correction axis (DGPS, RTK) is not in it.

Names differ from the archive's on two members and only because GraphQL forbids a leading digit:
the column holds `2D` and `3D`, this enum has `FIX_2D` and `FIX_3D`. The mapping is a named
artifact of the implementation, not something a resolver improvises — `valueOf(row)` on a 3D fix
would throw. `UNSPECIFIED` maps to null here and in `TrackingPositionSource`.

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

Where a message's event time came from, as `navixy.telemetry.v1.TimeSource`. Decides how far the
order of points can be trusted: `GNSS` depends on neither the device clock nor the network, while
`SERVER` and `DEVICE_CORRECTED` mean the time on the axis is the receiver's, not the observation's.
`UNSPECIFIED` maps to null.

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

Where the position came from, as `navixy.telemetry.v1.PositionSource`.

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

Suitability of a point, decided once by the writer rather than by each reader
(contracts/clickhouse/telemetry/v1/STORAGE.md §"Как отобрать пригодные точки").

| Value | Description |
| ----- | ----------- |
| `NO_POSITION` | No position in the message at all — heartbeat, readings-only report. |
| `NO_FIX` | The device tried and failed to get a fix. |
| `LAST_KNOWN` | A repeat of the last known point — legitimate on a map, must not enter distance maths. |
| `WEAK_GNSS` | A GNSS fix that failed the quality filter. |
| `GNSS` | Usable GNSS fix. |
| `NETWORK` | Network-derived position (cell towers, Wi-Fi). |
| `UNKNOWN_SRC` | Position from an unrecognised source. |

---

<a id="trackingtransport"></a>

### TrackingTransport

Channel a message arrived over, as `telemetry.records.transport`. A closed list — the archive's
daily "values outside the enumeration" check is written against exactly these — so it is an enum
rather than a `Code`. `UNSPECIFIED` maps to null.

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

Whether a device is currently reporting. Derived from the time of its last message and never
stored.

Not to be confused with `Device.status` (`DeviceStatus`), which is the administrative lifecycle of
the device — registered, active, retired — held as a catalog item in BDR, changing rarely and
deliberately. This one changes whenever a device goes quiet, which is why it is computed rather
than written: making "online" a lifecycle value would have every device write to BDR each time its
link flapped.

| Value | Description |
| ----- | ----------- |
| `ONLINE` | Reporting within the expected interval. |
| `IDLE` | Connected but silent longer than expected. |
| `OFFLINE` | Not reporting. |
| `NEVER_REPORTED` | The device has never sent a message. |

---

<a id="trackingmotionstatus"></a>

### TrackingMotionStatus

**Values:** `MOVING`, `STOPPED`, `PARKED`, `UNKNOWN`

---

<a id="trackingreportreason"></a>

### TrackingReportReason

Why the device sent this point, as `telemetry.records.report_reason`. A closed list, same as
`TrackingTransport`; `UNSPECIFIED` maps to null.

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
| `UPDATED_AT` | By `TrackingAssetState.updatedAt`; assets that never reported sort as null (`OrderDirection`). |

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
