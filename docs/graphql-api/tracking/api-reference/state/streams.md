---
description: >-
  Reference for the tracking subscription: the full state of an asset, with its
  linked devices, pushed on every report, delivered in batches with
  opening-snapshot markers and removal signals.
tags:
  - reference
  - beta
---

# State streams

The subscription that pushes the full state of followed assets, with their linked devices, on every report, with the batch, change and removal types it emits.

## Subscriptions

### trackingAssetStates

Full state of each followed asset, re-emitted whenever any of its devices reports.

  Every message carries the whole state rather than a delta, so a client holds no state machine
  and a reconnect needs no reconciliation. The emission is a batch — see
  `TrackingAssetStateBatch` — which is what carries the opening-snapshot markers and lets an
  asset report that it has left the subscription.

  Acts on the workspace the caller's token names, captured when the stream opens.

  The workspace needs Tracking enabled in Console; otherwise the subscription is refused with
  `PRODUCT_DISABLED`. Every refusal comes before the stream opens.

  A caller that already has as many streams open as its concurrent stream limit allows is refused
  with `RATE_LIMITED`. A slot frees when the server ends one of the caller's streams; for a
  connection that dropped without closing, that happens only once the server notices the silence.
  Retry with a backoff.

  Each subscription spends one request from the caller's rate limit, the same budget its HTTP
  requests to `/graphql` spend. Over it the subscription is refused with `RATE_LIMITED`; retry
  with a backoff.

```graphql
trackingAssetStates(
    input: TrackingAssetStateSubscriptionInput!
  ): TrackingAssetStateBatch!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `TrackingAssetStateSubscriptionInput!` | What to follow, and how much of each state to deliver. |

**Input types:**

<details>

<summary>TrackingAssetStateSubscriptionInput</summary>

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetIds` | `[ID!]` | Assets to follow. Null means every asset visible to the caller in its workspace, up to a server-side ceiling on how many one subscription may follow; over it the subscription is refused with `VALIDATION_ERROR` rather than silently truncated. The query side paginates the same data, and a stream has no cursor to paginate with, so the ceiling is where that bound goes. The set is live in both directions. An asset that becomes visible after the subscription opened — created, linked, or access granted — joins it, and its first appearance is an ordinary change carrying its full `state`; there is no separate "added" marker, because a state a client has not seen before is that marker. Growing past the ceiling this way closes the stream with `VALIDATION_ERROR`, the same answer the same size gets at open. An explicit id the caller cannot see at open is not refused: it emits nothing until it becomes visible, symmetric to `ACCESS_REVOKED` on the way out. |
| `readingCodes` | [[Code](../../../core-api-reference/common.md#code)!] | Narrow the delivered readings to these codes, every channel included; any code can be named. Null — the default — delivers every reading the device has sent; an empty list delivers none. |
| `eventCodes` | [[Code](../../../core-api-reference/common.md#code)!] | Deliver only these event codes. Empty list — the default — delivers none; null delivers all, which a stuck level alarm turns into a repeated object in every batch. The emitted snapshot type carries no arguments, so this input is the only place the filter can live. |
| `includeInitialState` | `Boolean` | Emit the current state of everything followed before live updates begin, so a client does not have to query and subscribe separately and race between the two. Delivered in the same batches as live updates, not as one frame — a workspace-wide subscription would otherwise serialise its entire fleet into a single message. |
| `batchWindowMs` | `Int` | Coalescing window in milliseconds. Updates for the same asset within one window collapse into one message, and messages are delivered as a batch — nvx3's `state_batch` broadcaster, made explicit. The server sets a minimum and a maximum, 1 000 and 5 000 ms by default; a value outside them refuses the subscription with `VALIDATION_ERROR` on `batchWindowMs`. |

</details>

**Output types:**

<details>

<summary>TrackingAssetStateBatch</summary>

One emission on an asset-state stream.

An empty `changes` is legitimate and carries meaning: a subscription that matches nothing still
receives one batch with `initialSnapshotComplete: true`, so a client's loading state always ends.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `changes` | [[TrackingAssetStateChange](#trackingassetstatechange)!]! |  |
| `fromInitialSnapshot` | `Boolean!` | True while the opening snapshot is being delivered, false for every batch after it. The opening snapshot may span several batches; the client learns it is done from `initialSnapshotComplete`, not from this turning false. Always false on a subscription opened with `includeInitialState: false` — there is no opening snapshot for a batch to belong to. |
| `initialSnapshotComplete` | `Boolean!` | True on the last batch of the opening snapshot and on that batch only. Clear a loading state here. Never true again for the life of the subscription — a reconnect opens a new one. With `includeInitialState: false` the stream still opens with one empty batch carrying this true, for the same reason a subscription matching nothing does: a client ends its loading state on this flag alone, and should not have to special-case how the subscription was opened. |

</details>

---

## Objects

<a id="trackingdevicestatesnapshot"></a>

### TrackingDeviceStateSnapshot

What the subscription emits for one of an asset's devices, under
`TrackingAssetStateSnapshot.devices`. Field for field the same state as `TrackingDeviceState`, with
two differences: nothing here takes arguments — what a caller would have narrowed per field is
narrowed once, in the subscription input — and `readings` is never null (see that field).

A separate type, not the query one, so that what a stream delivers is fixed when it opens: the
subscription input narrows readings once, in `readingCodes`, and nothing on this type takes
arguments. `TrackingDeviceState.readings(codes:, maxAge:)` is an ordinary field with arguments; if a
stream emitted that type, every selection and alias could ask for its own codes and age on every
message, a choice the input never sees. Two types cost some duplication.

`device: Device!` stays a full BDR object rather than a bare id, deliberately: the stream is what
a map renders from, and re-fetching titles over a second channel for every asset defeats that.
The cost is that `Device`'s own collection fields (`inventoryHistory`, and `groups`/`customFields`
on `Asset`) are reachable from a subscription's selection set, where they execute per emission —
and the gateway's complexity limit cannot see it, being computed from the document alone. The
per-emission budget is therefore part of the subscription implementation, not of the query limits.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `device` | [Device](../../../business-data-repository/api-reference/devices/README.md#device)! |  |
| `carrier` | [Device](../../../business-data-repository/api-reference/devices/README.md#device) | See `TrackingDeviceState.carrier`. |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Event time of the most recent message. Null for a device that has never reported. |
| `connectivity` | [TrackingConnectivity](README.md#trackingconnectivity)! |  |
| `position` | [TrackingPosition](README.md#trackingposition) | Selected on the same terms as `TrackingDeviceState.position`; its age is `position.at`. |
| `motion` | [TrackingMotion](README.md#trackingmotion) |  |
| `readings` | [[TrackingReading](../readings.md#trackingreading)!]! | Every reading of the device, each with its own `updatedAt`, narrowed by the subscription's `readingCodes`. A field the device sent once stays in every later emission, whether or not the message behind that emission carried it. Never null: a stream whose readings cannot be loaded is closed, not served device by device as a query is. |
| `lastEvents` | [[TrackingEvent](README.md#trackingevent)!]! | Same content and same retention as `TrackingDeviceState.lastEvents`, narrowed by the subscription's `eventCodes`. Not "the events of the message that triggered this emission": under `batchWindowMs` several messages collapse into one emission, so that message is not well-defined, and an emission caused by a heartbeat would clear an SOS the client may not have rendered yet. Whether a one-shot event is new is read from `code` and `occurredAt` against what the client last showed. A held alarm is the same object across emissions — identified by `code` and `since`, with `occurredAt` advancing on every repeat — so a changed `occurredAt` on it means a repeat, not a new event. A held alarm leaves the list once nothing has been received for the alarm reset gap, not when `connectivity.status` reaches `OFFLINE` (`TrackingEvent.since`); the stream emits the changed state when that moment passes. The initial snapshot carries the same list, so a client reconnecting after an alarm sees it before the next repeat — "a reconnect needs no reconciliation" is why full state was chosen over deltas. The limit of full state applies here too: two one-shot events in one coalescing window leave only the later message's in the emission. A consumer that must see every event needs an event stream, which this contract does not declare. |

---

<a id="trackingassetstatesnapshot"></a>

### TrackingAssetStateSnapshot

What a subscription emits for one asset: every device's snapshot, unmerged, on the same terms as
`TrackingAssetState`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `asset` | [Asset](../../../business-data-repository/api-reference/assets/README.md#asset)! | Full BDR object, on the same terms and at the same cost as `TrackingDeviceStateSnapshot.device`. |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime) | Newest `updatedAt` across the asset's devices. |
| `devices` | [[TrackingDeviceStateSnapshot](#trackingdevicestatesnapshot)!]! | The asset's devices. On a subscription that selects `readings`, a device linked to the asset while the stream is open appears from the stream's next membership refresh, once its readings are loaded; until then it is left out, not shown with empty readings. |
| `primaryDevice` | [TrackingDeviceStateSnapshot](#trackingdevicestatesnapshot) | Snapshot of `Asset.primaryDevice`; the same object also appears in `devices`. Null when there is none or `devices` leaves it out. |

---

<a id="trackingassetstatebatch"></a>

### TrackingAssetStateBatch

One emission on an asset-state stream.

An empty `changes` is legitimate and carries meaning: a subscription that matches nothing still
receives one batch with `initialSnapshotComplete: true`, so a client's loading state always ends.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `changes` | [[TrackingAssetStateChange](#trackingassetstatechange)!]! |  |
| `fromInitialSnapshot` | `Boolean!` | True while the opening snapshot is being delivered, false for every batch after it. The opening snapshot may span several batches; the client learns it is done from `initialSnapshotComplete`, not from this turning false. Always false on a subscription opened with `includeInitialState: false` — there is no opening snapshot for a batch to belong to. |
| `initialSnapshotComplete` | `Boolean!` | True on the last batch of the opening snapshot and on that batch only. Clear a loading state here. Never true again for the life of the subscription — a reconnect opens a new one. With `includeInitialState: false` the stream still opens with one empty batch carrying this true, for the same reason a subscription matching nothing does: a client ends its loading state on this flag alone, and should not have to special-case how the subscription was opened. |

---

<a id="trackingassetstatechange"></a>

### TrackingAssetStateChange

What happened to one asset in a batch: either its current full state, or the news that it is gone.

Exactly one of `state` and `removal` is non-null. `assetId` is present in both cases, because a
client removing an entity from a view needs its id and no longer has a state to read it from.

A pair of nullable fields rather than a `TrackingAssetStateSnapshot | TrackingStreamRemoval`
union, for handling: the id sits in one place for both outcomes, and a client reads two fields
instead of switching on `__typename` across fragments. Not for extensibility — a third outcome
would leave both fields null on a client built against this version, which the sentence above
forbids, so it is a breaking change of the same kind as a new union member.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetId` | `ID!` |  |
| `state` | [TrackingAssetStateSnapshot](#trackingassetstatesnapshot) | Full state as of this emission. Null when, and only when, `removal` is set. |
| `removal` | [TrackingStreamRemoval](#trackingstreamremoval) | Why the asset stopped being followed. Null while it is still followed. |

---

<a id="trackingstreamremoval"></a>

### TrackingStreamRemoval

Why an entity left a subscription. Without this a client cannot tell "gone" from "quiet": an
entity that stops being emitted looks exactly like one that simply has not reported.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `reason` | [TrackingStreamRemovalReason](#trackingstreamremovalreason)! |  |
| `at` | [DateTime](../../../core-api-reference/common.md#datetime)! | When the server stopped following it. |

---

## Inputs

<a id="trackingassetstatesubscriptioninput"></a>

### TrackingAssetStateSubscriptionInput

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetIds` | `[ID!]` | Assets to follow. Null means every asset visible to the caller in its workspace, up to a server-side ceiling on how many one subscription may follow; over it the subscription is refused with `VALIDATION_ERROR` rather than silently truncated. The query side paginates the same data, and a stream has no cursor to paginate with, so the ceiling is where that bound goes. The set is live in both directions. An asset that becomes visible after the subscription opened — created, linked, or access granted — joins it, and its first appearance is an ordinary change carrying its full `state`; there is no separate "added" marker, because a state a client has not seen before is that marker. Growing past the ceiling this way closes the stream with `VALIDATION_ERROR`, the same answer the same size gets at open. An explicit id the caller cannot see at open is not refused: it emits nothing until it becomes visible, symmetric to `ACCESS_REVOKED` on the way out. |
| `readingCodes` | [[Code](../../../core-api-reference/common.md#code)!] | Narrow the delivered readings to these codes, every channel included; any code can be named. Null — the default — delivers every reading the device has sent; an empty list delivers none. |
| `eventCodes` | [[Code](../../../core-api-reference/common.md#code)!] | Deliver only these event codes. Empty list — the default — delivers none; null delivers all, which a stuck level alarm turns into a repeated object in every batch. The emitted snapshot type carries no arguments, so this input is the only place the filter can live. |
| `includeInitialState` | `Boolean` | Emit the current state of everything followed before live updates begin, so a client does not have to query and subscribe separately and race between the two. Delivered in the same batches as live updates, not as one frame — a workspace-wide subscription would otherwise serialise its entire fleet into a single message. |
| `batchWindowMs` | `Int` | Coalescing window in milliseconds. Updates for the same asset within one window collapse into one message, and messages are delivered as a batch — nvx3's `state_batch` broadcaster, made explicit. The server sets a minimum and a maximum, 1 000 and 5 000 ms by default; a value outside them refuses the subscription with `VALIDATION_ERROR` on `batchWindowMs`. |

---

## Enums

<a id="trackingstreamremovalreason"></a>

### TrackingStreamRemovalReason

A client must treat an unrecognised value as a plain removal and drop the entity — the list will
grow, and the safe default is the one every member already shares. Do not switch exhaustively over
it without that fallback.

| Value | Description |
| ----- | ----------- |
| `ACCESS_REVOKED` | The subscriber lost the right to see it. |
| `DELETED` | The asset was deleted. |
| `UNLINKED` | The asset's last device was unlinked, so nothing can report for it any more. The mirror of `linked` in `TrackingAssetStateSubscriptionInput.assetIds`: a device link is one of the three things that bring an asset into a subscription, and losing the last link is what takes it back out. Re-linking a device returns the asset as an ordinary change carrying its full state, with no "added" marker, exactly as it arrived the first time. An asset with no devices is not an error and not invisible — `TrackingQuery.assetStates` still returns it, with an empty `devices` and a null `updatedAt`. It is the *stream* it leaves: a stream carries reports, and an asset with no device has none to carry. A client that wants such assets on screen reads them from the query side, which is where the no-longer-reporting case has an answer. |

---

## See also

* [Pagination](../../../pagination.md)
* [Filtering and sorting](../../../filtering-and-sorting/README.md)
* [Error handling](../../../error-handling.md)
* [Optimistic locking](../../../optimistic-locking.md)
* [Limits](../../../limits.md)
