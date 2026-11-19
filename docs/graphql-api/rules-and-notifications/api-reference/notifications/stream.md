---
tags:
  - reference
  - beta
---

# Notification stream

The notifications that rules send, and the notificationsEvents subscription that delivers each one live to every open session of the users who can read its asset.

## Subscriptions

### notificationsEvents

Every notification published for the caller's workspace that the caller may receive, one message
  each, from the moment the stream opens: nothing earlier is replayed; what a session missed is in
  `Query.notifications { events }`, which keeps every notification for the deployment's retention.

  Acts on the workspace the caller's token names, captured when the WebSocket connection is
  initialised or with the request over SSE. A notification is delivered when the caller is a user
  and the caller's `bdr.asset` read access admits the notification's asset; the access is re-read
  on a period while the stream is open.

  The workspace needs Notifications enabled in Console; otherwise the subscription is refused with
  `PRODUCT_DISABLED` before the stream opens.

  Refused at open with `PERMISSION_DENIED` for an API key, a member of staff or the system actor;
  with `UNAUTHORIZED` for a token without an expiry or already expired. A caller that already
  holds as many notification sessions as its limit allows is refused with `RATE_LIMITED`; a slot
  frees when one of its streams ends. Each subscription spends one request from the caller's rate
  limit, as `trackingAssetStates` does.

  Ended by the server with `UNAUTHORIZED` when the token expires and with `SERVICE_UNAVAILABLE`
  when the caller's workspace or access can no longer be read. Resubscribe with a backoff.

```graphql
notificationsEvents: NotificationsEvent!
```

**Output types:**

<details>

<summary>NotificationsEvent</summary>

One notification, as the stream delivers it and the list returns it: the fields a toast or a row
shows, the ids a click needs and the two instants. `node(id:)` resolves one the caller may read, as
long as the retention keeps it.

**Implements:** [Node](../../../core-api-reference/common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The notification's id, minted once per accepted command and the same on every replica and session. |
| `publishedAt` | [DateTime](../../../core-api-reference/common.md#datetime)! | When the platform accepted the command; the retention and the list's window count from here. |
| `occurredAt` | [DateTime](../../../core-api-reference/common.md#datetime)! | When the event happened. A buffered upload delivers events hours or months old, so this may be long before `publishedAt`; it is the instant to show, and the list's order. |
| `text` | `String!` | The text to show, rendered by the producer. At most 1000 characters. |
| `severity` | [NotificationsSeverity](#notificationsseverity)! |  |
| `title` | `String` | A short heading, the rule's name for the Rules service. Absent when the producer sent none. |
| `assetId` | `ID!` | The asset the notification is about, in the caller's workspace. |
| `deviceId` | `ID` | The device whose event triggered the notification, when there is one. |
| `point` | [GeoPoint](../../../business-data-repository/api-reference/geo-objects.md#geopoint) | Where the event happened; a click moves the map there. Absent for an event with no place. |

</details>

---

## Objects

<a id="notificationsevent"></a>

### NotificationsEvent

One notification, as the stream delivers it and the list returns it: the fields a toast or a row
shows, the ids a click needs and the two instants. `node(id:)` resolves one the caller may read, as
long as the retention keeps it.

**Implements:** [Node](../../../core-api-reference/common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The notification's id, minted once per accepted command and the same on every replica and session. |
| `publishedAt` | [DateTime](../../../core-api-reference/common.md#datetime)! | When the platform accepted the command; the retention and the list's window count from here. |
| `occurredAt` | [DateTime](../../../core-api-reference/common.md#datetime)! | When the event happened. A buffered upload delivers events hours or months old, so this may be long before `publishedAt`; it is the instant to show, and the list's order. |
| `text` | `String!` | The text to show, rendered by the producer. At most 1000 characters. |
| `severity` | [NotificationsSeverity](#notificationsseverity)! |  |
| `title` | `String` | A short heading, the rule's name for the Rules service. Absent when the producer sent none. |
| `assetId` | `ID!` | The asset the notification is about, in the caller's workspace. |
| `deviceId` | `ID` | The device whose event triggered the notification, when there is one. |
| `point` | [GeoPoint](../../../business-data-repository/api-reference/geo-objects.md#geopoint) | Where the event happened; a click moves the map there. Absent for an event with no place. |

---

## Enums

<a id="notificationsseverity"></a>

### NotificationsSeverity

How urgent a notification is. The platform publishes `INFO` only for now; `WARNING` and `CRITICAL`
are named so that using them later is no contract change.

**Values:** `INFO`, `WARNING`, `CRITICAL`

---

## See also

* [Pagination](../../../pagination.md)
* [Filtering and sorting](../../../filtering-and-sorting/README.md)
* [Error handling](../../../error-handling.md)
* [Optimistic locking](../../../optimistic-locking.md)
* [Limits](../../../limits.md)
