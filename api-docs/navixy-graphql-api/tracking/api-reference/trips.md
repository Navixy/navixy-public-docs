---
description: >-
  Reference for trips and stops: timelines derived from the message archive, the
  segmentation policy that cuts them, and the options that shape returned
  points.
---

# Trips and stops

{% include "../../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

Trips and stops derived from the message archive on read, with the segmentation policy and point options that shape them.

## Queries

### deviceTimeline

Trips and stops of one device over a window.

  `segmentation` applies only to devices that carry no policy of their own; the result reports
  which policy actually applied.

```graphql
deviceTimeline(
    deviceId: ID!
    from: DateTime!
    to: DateTime!
    segmentation: TrackingSegmentationInput
    options: TrackingTrackOptions
  ): TrackingTimeline
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `deviceId` | `ID!` |  |
| `from` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `to` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `segmentation` | `TrackingSegmentationInput` |  |
| `options` | `TrackingTrackOptions` |  |

**Input types:**

<details>

<summary>TrackingSegmentationInput</summary>

How a device's message stream is cut into trips and stops.

Resolution order per device: the device's own policy, else the policy supplied with the request,
else the platform default. `TrackingSegmentationApplied.source` reports which one was used, so a
caller never has to guess whether its request-level policy took effect.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `minStopDuration` | [Duration](../../core-api-reference/common.md#duration) | How long a device must stay put before the pause counts as a stop and cuts the trip. A shorter pause is neither: it appears in no `stops` entry and the trip runs through it. Longer than the window, no pause qualifies: `stops` is empty and whatever movement the window holds is one trip, the shape nvx3's `split: false` returned. A window without movement yields no trip either way, and a server-side cut (`TrackingTimeline.continueFrom`) still splits the answer. |
| `stopRadius` | `Float` | Radius, metres, within which points count as the same place rather than movement. |
| `movingSpeedThreshold` | `Float` | Speed at or below which the device counts as not moving, km/h. |

</details>

<details>

<summary>TrackingTrackOptions</summary>

| Field | Type | Description |
| ----- | ---- | ----------- |
| `fitness` | [[TrackingFitness](state/README.md#trackingfitness)!] | Which points to include. Defaults to the track filter from STORAGE.md — `[GNSS, NETWORK]`. Adding `LAST_KNOWN` puts the repeated last position on the map, which is where it belongs; it never enters `distance`, whatever this argument says, because a stop in a tunnel would otherwise read as a trip (STORAGE.md §Как отобрать пригодные точки). |
| `includePoints` | `Boolean` | Return the points of each trip, not just its summary. Off by default — points dominate the payload. |
| `maxPoints` | `Int` | Thin each trip's polyline to at most this many points (Douglas–Peucker). Null disables thinning. |
| `readingCodes` | [[Code](../../core-api-reference/common.md#code)!] | Include these readings on every point. |
| `eventCodes` | [[Code](../../core-api-reference/common.md#code)!] | Include only these event codes on segments and points. Empty list — the default — includes none; null includes all, which is rarely what a caller wants: a level alarm re-fires in every message while it holds (SPEC §6.5), so a day of a stuck input is thousands of identical objects. The service collapses a repeated level alarm into one event with `since` set; this argument is the second lever. |

</details>

**Output types:**

<details>

<summary>TrackingTimeline</summary>

Trips and stops of one device over a window, plus the policy that produced them.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `device` | [Device](../../business-data-repository/api-reference/devices/README.md#device)! |  |
| `from` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `to` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `segmentation` | [TrackingSegmentationApplied](#trackingsegmentationapplied)! |  |
| `tracks` | [[TrackingTrack](#trackingtrack)!]! |  |
| `stops` | [[TrackingStop](#trackingstop)!]! |  |
| `totalDistance` | `Float!` | Sum of `TrackingTrack.distance`, km. |
| `totalDrivingTime` | [Duration](../../core-api-reference/common.md#duration)! | Sum of trip durations. |
| `totalStopTime` | [Duration](../../core-api-reference/common.md#duration)! | Sum of stop durations, an unfinished stop counted per `TrackingStop.duration` — which bounds it by what this answer covers, not by `to`, when `continueFrom` is set. Stable for a `to` in the past; grows between calls for a `to` in the future, for the same reason. |
| `continueFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Where the answer stops when a server-side budget cut the window short — pass it back as the next call's `from`. Null means the whole window was covered. A cursor rather than a boolean flag: truncation can land inside a stop, or leave `tracks` empty with only `stops` cut, and then "continue from the last segment's end" names nothing. The budget itself exists because segmentation re-reads the window's raw points on every call. |

</details>

---

### assetTimeline

Trips and stops of an asset over a window, per device.
  An asset with several devices yields one timeline per device — the same no-merge rule as state.

```graphql
assetTimeline(
    assetId: ID!
    from: DateTime!
    to: DateTime!
    segmentation: TrackingSegmentationInput
    options: TrackingTrackOptions
  ): [TrackingTimeline!]!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `assetId` | `ID!` |  |
| `from` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `to` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `segmentation` | `TrackingSegmentationInput` |  |
| `options` | `TrackingTrackOptions` |  |

**Input types:**

<details>

<summary>TrackingSegmentationInput</summary>

How a device's message stream is cut into trips and stops.

Resolution order per device: the device's own policy, else the policy supplied with the request,
else the platform default. `TrackingSegmentationApplied.source` reports which one was used, so a
caller never has to guess whether its request-level policy took effect.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `minStopDuration` | [Duration](../../core-api-reference/common.md#duration) | How long a device must stay put before the pause counts as a stop and cuts the trip. A shorter pause is neither: it appears in no `stops` entry and the trip runs through it. Longer than the window, no pause qualifies: `stops` is empty and whatever movement the window holds is one trip, the shape nvx3's `split: false` returned. A window without movement yields no trip either way, and a server-side cut (`TrackingTimeline.continueFrom`) still splits the answer. |
| `stopRadius` | `Float` | Radius, metres, within which points count as the same place rather than movement. |
| `movingSpeedThreshold` | `Float` | Speed at or below which the device counts as not moving, km/h. |

</details>

<details>

<summary>TrackingTrackOptions</summary>

| Field | Type | Description |
| ----- | ---- | ----------- |
| `fitness` | [[TrackingFitness](state/README.md#trackingfitness)!] | Which points to include. Defaults to the track filter from STORAGE.md — `[GNSS, NETWORK]`. Adding `LAST_KNOWN` puts the repeated last position on the map, which is where it belongs; it never enters `distance`, whatever this argument says, because a stop in a tunnel would otherwise read as a trip (STORAGE.md §Как отобрать пригодные точки). |
| `includePoints` | `Boolean` | Return the points of each trip, not just its summary. Off by default — points dominate the payload. |
| `maxPoints` | `Int` | Thin each trip's polyline to at most this many points (Douglas–Peucker). Null disables thinning. |
| `readingCodes` | [[Code](../../core-api-reference/common.md#code)!] | Include these readings on every point. |
| `eventCodes` | [[Code](../../core-api-reference/common.md#code)!] | Include only these event codes on segments and points. Empty list — the default — includes none; null includes all, which is rarely what a caller wants: a level alarm re-fires in every message while it holds (SPEC §6.5), so a day of a stuck input is thousands of identical objects. The service collapses a repeated level alarm into one event with `since` set; this argument is the second lever. |

</details>

**Output types:**

<details>

<summary>TrackingTimeline</summary>

Trips and stops of one device over a window, plus the policy that produced them.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `device` | [Device](../../business-data-repository/api-reference/devices/README.md#device)! |  |
| `from` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `to` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `segmentation` | [TrackingSegmentationApplied](#trackingsegmentationapplied)! |  |
| `tracks` | [[TrackingTrack](#trackingtrack)!]! |  |
| `stops` | [[TrackingStop](#trackingstop)!]! |  |
| `totalDistance` | `Float!` | Sum of `TrackingTrack.distance`, km. |
| `totalDrivingTime` | [Duration](../../core-api-reference/common.md#duration)! | Sum of trip durations. |
| `totalStopTime` | [Duration](../../core-api-reference/common.md#duration)! | Sum of stop durations, an unfinished stop counted per `TrackingStop.duration` — which bounds it by what this answer covers, not by `to`, when `continueFrom` is set. Stable for a `to` in the past; grows between calls for a `to` in the future, for the same reason. |
| `continueFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Where the answer stops when a server-side budget cut the window short — pass it back as the next call's `from`. Null means the whole window was covered. A cursor rather than a boolean flag: truncation can land inside a stop, or leave `tracks` empty with only `stops` cut, and then "continue from the last segment's end" names nothing. The budget itself exists because segmentation re-reads the window's raw points on every call. |

</details>

---

## Objects

<a id="trackingsegmentationapplied"></a>

### TrackingSegmentationApplied

The segmentation policy that actually produced the result, and where it came from.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `minStopDuration` | [Duration](../../core-api-reference/common.md#duration)! |  |
| `stopRadius` | `Float!` |  |
| `movingSpeedThreshold` | `Float!` |  |
| `source` | [TrackingSegmentationSource](#trackingsegmentationsource)! |  |

---

<a id="trackingtrack"></a>

### TrackingTrack

A trip — a movement segment between two stops.

`id` identifies the trip **within one response**, nothing more. It is a hash over the device, the
request's parameters and the segment's bounds, so an identical request reproduces it, and any
change to the window or the segmentation policy does not. The device is part of the hash because
`assetTimeline` returns one timeline per device from a single request: two trackers on the same
vehicle, parked together, produce trips with identical bounds, and without the device in the hash
a UI holding a selection by id would highlight both.

Nothing stores it, `Query.node` does not resolve it, and no query takes it as an argument — use it
to correlate a trip with a client-side selection inside a single answer, not to link to a trip
later.

A trip that a server-side cut (`TrackingTimeline.continueFrom`) lands inside ends at the last
point the answer covers: `endedAt` is that point's time, and the next page's first trip starts
where that page starts. The parts are adjacent, not overlapping, so `distance`, `duration` and
`pointCount` summed across pages give the whole trip's figures; `id` differs per part because
the bounds differ. The same rule, for stops, is on `TrackingStop.duration`.

Typed `String!`, not `ID!`, and that is not cosmetic: the gateway's DGS codegen maps **every**
`ID` in the schema to `com.navixy.uuid.UuidV8` (`api-gateway/build.gradle.kts` `typeMapping`),
a value class with a fixed layout — 48 time bits, version, counter, a four-character entity
discriminator. A content hash is not expressible in it, and squeezing one into the deterministic
constructor's 16-bit suffix would collide inside a single response, destroying the very
distinction the device-in-the-hash rule above exists to make.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `String!` |  |
| `startedAt` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `endedAt` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `distance` | `Float!` | Trip length, km. LAST_KNOWN repeats are excluded from the sum (STORAGE.md §Как отобрать). |
| `duration` | [Duration](../../core-api-reference/common.md#duration)! | Wall-clock duration. |
| `maxSpeed` | `Float` |  |
| `avgSpeed` | `Float` |  |
| `startPoint` | [GeoPoint](../../business-data-repository/api-reference/geo-objects.md#geopoint)! |  |
| `endPoint` | [GeoPoint](../../business-data-repository/api-reference/geo-objects.md#geopoint)! |  |
| `pointCount` | `Int!` |  |
| `points` | [[TrackingTrackPoint](#trackingtrackpoint)!]! | Points of the trip. Empty unless the query asked for them. |
| `simplified` | `Boolean!` | True when `points` was thinned to fit `TrackingTrackOptions.maxPoints`. |
| `events` | [[TrackingEvent](state/README.md#trackingevent)!]! | Events that occurred within the trip. |

---

<a id="trackingstop"></a>

### TrackingStop

A stop — the device stayed within `stopRadius` for at least `minStopDuration`.
`id` is response-scoped, and `String!` rather than `ID!`, on the same terms as `TrackingTrack.id`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `String!` |  |
| `startedAt` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `endedAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the stop ended. Null for a stop still in progress at the end of the requested window — including the current one, which is the usual case for `to = now`. |
| `duration` | [Duration](../../core-api-reference/common.md#duration)! | How long the stop lasted. For a finished stop, `endedAt - startedAt`. For one still in progress — `endedAt: null` — it is measured to the end of what this answer actually covers: `min(to, now)`, or `continueFrom` when a server-side budget cut the window short. That is not the same bound as `to`, because truncation can land inside a stop. Never past the end of the requested window and never into the future, so the same `to` in the past yields the same duration on every call. A stop the truncation cut continues in the next page as a stop of its own, starting where that page starts: the parts are adjacent rather than overlapping, and stop time summed across pages is the total. The one case that does not settle is a `to` in the future — the window has not closed, `min(to, now)` is `now`, and the duration grows between calls. That is the honest answer for an open window rather than a defect. |
| `point` | [GeoPoint](../../business-data-repository/api-reference/geo-objects.md#geopoint)! | Representative point of the stop (centroid of its points). |
| `messageCount` | `Int!` | Number of messages received during the stop. |
| `events` | [[TrackingEvent](state/README.md#trackingevent)!]! |  |

---

<a id="trackingtrackpoint"></a>

### TrackingTrackPoint

| Field | Type | Description |
| ----- | ---- | ----------- |
| `at` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `point` | [GeoPoint](../../business-data-repository/api-reference/geo-objects.md#geopoint)! |  |
| `altitude` | `Float` |  |
| `speed` | `Float` |  |
| `heading` | `Float` |  |
| `satellites` | `Int` |  |
| `fitness` | [TrackingFitness](state/README.md#trackingfitness)! |  |
| `timeSource` | [TrackingTimeSource](state/README.md#trackingtimesource) | Where `at` came from; see `TrackingPosition.timeSource`. A `SERVER` or `DEVICE_CORRECTED` point stands at its upload moment. |
| `reportReason` | [TrackingReportReason](state/README.md#trackingreportreason) | Why the device sent this point. |
| `readings` | [[TrackingReading](readings.md#trackingreading)!]! | Readings from the message this point came from, when the query asked for them. |
| `events` | [[TrackingEvent](state/README.md#trackingevent)!]! | Events carried by that message. |

---

<a id="trackingtimeline"></a>

### TrackingTimeline

Trips and stops of one device over a window, plus the policy that produced them.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `device` | [Device](../../business-data-repository/api-reference/devices/README.md#device)! |  |
| `from` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `to` | [DateTime](../../core-api-reference/common.md#datetime)! |  |
| `segmentation` | [TrackingSegmentationApplied](#trackingsegmentationapplied)! |  |
| `tracks` | [[TrackingTrack](#trackingtrack)!]! |  |
| `stops` | [[TrackingStop](#trackingstop)!]! |  |
| `totalDistance` | `Float!` | Sum of `TrackingTrack.distance`, km. |
| `totalDrivingTime` | [Duration](../../core-api-reference/common.md#duration)! | Sum of trip durations. |
| `totalStopTime` | [Duration](../../core-api-reference/common.md#duration)! | Sum of stop durations, an unfinished stop counted per `TrackingStop.duration` — which bounds it by what this answer covers, not by `to`, when `continueFrom` is set. Stable for a `to` in the past; grows between calls for a `to` in the future, for the same reason. |
| `continueFrom` | [DateTime](../../core-api-reference/common.md#datetime) | Where the answer stops when a server-side budget cut the window short — pass it back as the next call's `from`. Null means the whole window was covered. A cursor rather than a boolean flag: truncation can land inside a stop, or leave `tracks` empty with only `stops` cut, and then "continue from the last segment's end" names nothing. The budget itself exists because segmentation re-reads the window's raw points on every call. |

---

## Inputs

<a id="trackingsegmentationinput"></a>

### TrackingSegmentationInput

How a device's message stream is cut into trips and stops.

Resolution order per device: the device's own policy, else the policy supplied with the request,
else the platform default. `TrackingSegmentationApplied.source` reports which one was used, so a
caller never has to guess whether its request-level policy took effect.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `minStopDuration` | [Duration](../../core-api-reference/common.md#duration) | How long a device must stay put before the pause counts as a stop and cuts the trip. A shorter pause is neither: it appears in no `stops` entry and the trip runs through it. Longer than the window, no pause qualifies: `stops` is empty and whatever movement the window holds is one trip, the shape nvx3's `split: false` returned. A window without movement yields no trip either way, and a server-side cut (`TrackingTimeline.continueFrom`) still splits the answer. |
| `stopRadius` | `Float` | Radius, metres, within which points count as the same place rather than movement. |
| `movingSpeedThreshold` | `Float` | Speed at or below which the device counts as not moving, km/h. |

---

<a id="trackingtrackoptions"></a>

### TrackingTrackOptions

| Field | Type | Description |
| ----- | ---- | ----------- |
| `fitness` | [[TrackingFitness](state/README.md#trackingfitness)!] | Which points to include. Defaults to the track filter from STORAGE.md — `[GNSS, NETWORK]`. Adding `LAST_KNOWN` puts the repeated last position on the map, which is where it belongs; it never enters `distance`, whatever this argument says, because a stop in a tunnel would otherwise read as a trip (STORAGE.md §Как отобрать пригодные точки). |
| `includePoints` | `Boolean` | Return the points of each trip, not just its summary. Off by default — points dominate the payload. |
| `maxPoints` | `Int` | Thin each trip's polyline to at most this many points (Douglas–Peucker). Null disables thinning. |
| `readingCodes` | [[Code](../../core-api-reference/common.md#code)!] | Include these readings on every point. |
| `eventCodes` | [[Code](../../core-api-reference/common.md#code)!] | Include only these event codes on segments and points. Empty list — the default — includes none; null includes all, which is rarely what a caller wants: a level alarm re-fires in every message while it holds (SPEC §6.5), so a day of a stuck input is thousands of identical objects. The service collapses a repeated level alarm into one event with `since` set; this argument is the second lever. |

---

## Enums

<a id="trackingsegmentationsource"></a>

### TrackingSegmentationSource

| Value | Description |
| ----- | ----------- |
| `DEVICE` | The device carries its own segmentation policy, which overrides the request. |
| `REQUEST` | The policy supplied with the request applied — the device has none of its own. |
| `PLATFORM_DEFAULT` | Neither device nor request supplied one; the platform default applied. |

---

## See also

* [Pagination](../../pagination.md)
* [Filtering and sorting](../../filtering-and-sorting/README.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
