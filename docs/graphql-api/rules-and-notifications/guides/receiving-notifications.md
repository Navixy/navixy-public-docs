---
description: >-
  Receive rule notifications live with the notificationsEvents subscription,
  subscribe again after the stream ends, and list, count, and mark the rest.
tags:
  - guide
  - beta
---

# Receiving notifications

When a rule activates for an asset, Navixy sends one notification to every user who can read that asset. The `notificationsEvents` subscription delivers each notification live, to every open session. A session is one open subscription, such as the subscription of one browser tab. The `notifications` query lists the stored notifications, so a client finds there what it missed while it wasn't connected.

This guide shows how to open the stream, what a client receives, how the stream ends and how to subscribe again, and how to list, count, and mark notifications as read. It's for developers who show notifications in their own app, as Location Intelligence does with a pop-up message on the map. When a rule activates is explained in [How rules work](../how-rules-work.md#when-a-rule-activates-and-deactivates).

## Prerequisites

You need an access token of a user, in a workspace that has the Notifications product, and a client for the `graphql-transport-ws` protocol. This guide uses the [graphql-ws](https://github.com/enisdenjo/graphql-ws) library for JavaScript. The protocol and the connection rules that every subscription shares are in [Subscriptions](../../graphql-basics/#subscriptions).

The stream is served over a WebSocket at `wss://nvx4.<REGION>.navixy.com/api/graphql/ws`. The same subscription is also served as server-sent events: a POST request to `https://nvx4.<REGION>.navixy.com/api/graphql` with the `Accept: text/event-stream` header and the access token in the `Authorization` header. This guide describes the WebSocket. Replace `<REGION>` in both addresses with the region of your organization, `eu` or `us`. See [Endpoint and region](../../graphql-basics/#endpoint-and-region).

## Who receives a notification

A notification goes to the users of the workspace who can read its asset. These conditions decide who receives it:

* The token sets the workspace. The stream receives the notifications of the workspace named by the access token. A token for another workspace opens another stream.
* The workspace has the Notifications product. An administrator of your organization turns it on in Navixy Console. Without it, the subscription and the list return `PRODUCT_DISABLED`.
* The token belongs to a user. An API key receives nothing: its subscription and its list return `PERMISSION_DENIED`. Navixy checks the product first, so in a workspace without Notifications, every caller gets `PRODUCT_DISABLED`.
* The user can read the asset. A user whose roles allow reading only some assets receives the notifications of those assets only. A user without `read` on `bdr.asset` gets `PERMISSION_DENIED`. While the stream is open, Navixy checks the user's access again from time to time. A user who loses access to an asset stops receiving its notifications without reconnecting.
* Every open session gets its own copy. A user with three open browser tabs receives three copies. Navixy doesn't know whether another session already showed the notification.

The stream doesn't replay anything. A notification sent while a session wasn't connected never arrives on that session's stream. It stays in the stored list. See [List the stored notifications](#list-the-stored-notifications).

## What a notification contains

The stream and the list return the same type, `NotificationsEvent`:

| Field | What it contains |
| --- | --- |
| `id` | The notification's ID. Every session receives the same ID for the same notification. |
| `publishedAt` | When Navixy accepted the notification. The read mark and the list's retention count from this time. |
| `occurredAt` | When the event happened, from the device message. Show this time to the user. |
| `text` | The text to show, up to 1,000 characters: the rule's `notificationText`, or its `title` when the rule has no text |
| `severity` | How urgent the notification is. Rules send `INFO` only. The schema also has `WARNING` and `CRITICAL`, which rules don't send. |
| `title` | A short heading: the rule's `title` |
| `assetId` | The asset that the notification is about |
| `deviceId` | The device whose message activated the rule. Can be `null`. |
| `point` | Where the event happened, as `lat` and `lng`. `null` when the message had no position. |

The notification has IDs, not objects. Your app reads the asset and the device from Business Data Repository with `bdr { asset(id:) }` and `bdr { device(id:) }`, or from the data it already has.

`occurredAt` and `publishedAt` can be far apart. A device can store messages while it has no connection and send them later, and Navixy can publish a notification some time after the rule activated. So in the list, which is ordered by `occurredAt`, a notification published later can appear below notifications published earlier.

## Open the notification stream

To receive notifications over the WebSocket, follow these steps:

{% stepper %}
{% step %}
### Connect and send the access token

Open a WebSocket to `wss://nvx4.<REGION>.navixy.com/api/graphql/ws` with the subprotocol `graphql-transport-ws`. Within 10 seconds, send `connection_init` with the access token in the payload, under the key `Authorization`:

```json
{
  "type": "connection_init",
  "payload": { "Authorization": "Bearer <ACCESS_TOKEN>" }
}
```

The server replies with `connection_ack`:

```json
{ "type": "connection_ack" }
```

The token must have an expiry time. The server reads the token only from `connection_init`. An open connection keeps using that token. To use a renewed token, open a new connection.

If the server refuses `connection_init`, it closes the connection. The close code is 4401 for a missing, invalid, or expired token. The code is 4403 when you already have the maximum number of connections. Reply to every `ping` with `pong`. The `graphql-ws` library does this automatically.
{% endstep %}

{% step %}
### Subscribe

Send `subscribe` with an ID of your choice and the subscription. Select the fields that your app shows:

```json
{
  "id": "1",
  "type": "subscribe",
  "payload": {
    "query": "subscription Notifications { notificationsEvents { id publishedAt occurredAt text severity title assetId deviceId point { lat lng } } }"
  }
}
```

The server doesn't confirm the subscription. An open stream sends nothing until the first notification.
{% endstep %}

{% step %}
### Receive notifications

Each notification arrives as one `next` message, with the notification under `data.notificationsEvents`:

```json
{
  "id": "1",
  "type": "next",
  "payload": {
    "data": {
      "notificationsEvents": {
        "id": "01a14d05-9b44-8000-801f-4e4f5449a2b8",
        "publishedAt": "2026-10-28T10:12:08.214Z",
        "occurredAt": "2026-10-28T10:12:07.000Z",
        "text": "Speed over 25 km/h in the depot. Check the driver.",
        "severity": "INFO",
        "title": "Speeding in the depot",
        "assetId": "01a1305f-50ec-8000-801f-41535354a5e1",
        "deviceId": "019dbec6-d380-8000-801f-44455643e216",
        "point": { "lat": 52.5207, "lng": 13.4049 }
      }
    }
  }
}
```

Show `title` and `text`, with the asset's name from your own data and the time from `occurredAt`. When `point` isn't `null`, you can center your map there.
{% endstep %}

{% step %}
### End the stream

When the view that shows notifications closes, send `complete` with the subscription's ID:

```json
{ "id": "1", "type": "complete" }
```

The server ends the stream at once. The stream no longer counts toward your limit of 12 open streams. A stream that you leave open counts toward the limit until the connection closes.
{% endstep %}
{% endstepper %}

## A notification client in JavaScript

The following client uses `graphql-ws`, which sends `connection_init`, replies to `ping`, and reconnects after most closes:

```js
import { createClient } from 'graphql-ws';

const client = createClient({
  url: 'wss://nvx4.<REGION>.navixy.com/api/graphql/ws',
  // Runs on every connection, so a renewed token reaches the next connection.
  connectionParams: async () => ({ Authorization: `Bearer ${await getAccessToken()}` }),
});

const unsubscribe = client.subscribe(
  {
    query: `subscription Notifications {
      notificationsEvents { id publishedAt occurredAt text severity title assetId deviceId point { lat lng } }
    }`,
  },
  {
    next: ({ data, errors }) => {
      if (errors) return handleRefusal(errors[0].extensions.code);
      showNotification(data.notificationsEvents);
    },
    // An `error` message passes the list of GraphQL errors. A closed connection passes the close event.
    error: (err) => handleEnd(err),
    complete: () => {},
  },
);
```

`getAccessToken`, `showNotification`, `handleRefusal`, and `handleEnd` are your app's functions. `handleRefusal` and `handleEnd` act on the table in [Refusals and endings of the stream](#refusals-and-endings-of-the-stream). Call `unsubscribe()` when the view closes.

## Refusals and endings of the stream

A refused or ended stream arrives in one of two shapes, depending on whether the stream was open:

* **Before the stream opens:** a `next` message with `errors`, then `complete`. The code is at `payload.errors[0].extensions.code`. Don't rely on `payload.data`: it can be `null` or absent.
* **When the stream starts or while it's open:** an `error` message without `complete`. The payload is the list of errors, so the code is at `payload[0].extensions.code`.

The server sends the following messages when an API key subscribes:

```json
{
  "id": "1",
  "type": "next",
  "payload": {
    "data": null,
    "errors": [{
      "message": "Notifications are delivered to users; the caller is not one",
      "path": ["notificationsEvents"],
      "extensions": {
        "code": "PERMISSION_DENIED",
        "status": 403,
        "title": "Permission Denied",
        "detail": "Notifications are delivered to users; the caller is not one",
        "type": "https://api.navixy.com/errors/forbidden",
        "instance": "/graphql",
        "classification": "FORBIDDEN"
      }
    }]
  }
}
{ "id": "1", "type": "complete", "payload": {} }
```

The following table lists every refusal and ending, with the shape of its messages:

| What happened | Code | Shape |
| --- | --- | --- |
| The token expired after the connection opened | `UNAUTHORIZED` | `next` with `errors`, then `complete` |
| The request isn't one subscription, or the connection already has 10 subscriptions | `VALIDATION_ERROR` | `next` with `errors`, then `complete` |
| You're over your request rate | `RATE_LIMITED` | `next` with `errors`, then `complete` |
| The workspace doesn't have the Notifications product | `PRODUCT_DISABLED` | `next` with `errors`, then `complete` |
| The token belongs to an API key, or the user can't read any asset | `PERMISSION_DENIED` | `next` with `errors`, then `complete` |
| You already have 12 open notification streams | `RATE_LIMITED` | `error`, no `complete` |
| The token expired while the stream was open | `UNAUTHORIZED` | `error`, no `complete` |
| Navixy couldn't read your workspace or access while the stream was open | `SERVICE_UNAVAILABLE` | `error`, no `complete` |
| The WebSocket closed: a refused `connection_init`, no reply to `ping`, or a server restart | A close code | The connection closes, and every stream on it ends |

### How to handle a refused or ended notification stream

What to do depends on the code:

* `PRODUCT_DISABLED`: Don't retry. When an administrator turns on the product, get a new token, open a new connection, and subscribe again. The open connection keeps its old token, so a new subscription on it is refused again.
* `PERMISSION_DENIED`: Don't retry. The same request returns the same error until the user's roles change.
* `UNAUTHORIZED`: Get a new token, open a new connection, and subscribe again.
* `RATE_LIMITED`: Wait before you retry, and wait longer after each refusal. You can open a new stream after one of the user's open streams ends, in any tab.
* `VALIDATION_ERROR`: Fix the subscription or open another connection. Sending the same request again returns the same error.
* `SERVICE_UNAVAILABLE`: Subscribe again after a delay, on the same connection or a new one.
* A closed connection: Connect again after a delay, then subscribe again. `graphql-ws` reconnects by itself after 4403 and after ordinary closes. After 4401 it stops, so connect again with a new token.

## Find the notifications missed while the stream was closed

A new stream delivers only the notifications sent after it opens. The notifications sent between the end of the old stream and the start of the new stream are only in the stored list.

To show what the user missed, read the list after you subscribe again. Keep the largest `publishedAt` that your app showed before the stream ended. The notifications with a later `publishedAt` are the ones the user hasn't seen.

The list is ordered by `occurredAt`, not `publishedAt`, so a missed notification can appear below notifications that the user already saw. The list has no filter or order by `publishedAt`. To find every missed notification, page through the whole list and keep the notifications whose `publishedAt` is later than the value that you kept. To only tell the user that they have new notifications, read `unreadCount` instead. See [Unread notifications and the read mark](#unread-notifications-and-the-read-mark).

## List the stored notifications

The `notifications` query lists the notifications of your workspace that you can read, with the read mark and the unread count. It's a normal HTTP request to `https://nvx4.<REGION>.navixy.com/api/graphql`. One query reads all three:

```graphql
query Inbox($after: String) {
  notifications {
    readUpTo
    unreadCount { count precision }
    events(first: 50, after: $after) {
      total { count precision }
      pageInfo { hasNextPage endCursor }
      nodes { id publishedAt occurredAt text severity title assetId deviceId point { lat lng } }
    }
  }
}
```

Response:

```json
{
  "data": {
    "notifications": {
      "readUpTo": "2026-10-28T09:00:00.000Z",
      "unreadCount": { "count": 1, "precision": "EXACT" },
      "events": {
        "total": { "count": 2, "precision": "EXACT" },
        "pageInfo": { "hasNextPage": false, "endCursor": "eyJvIjoiMjAyNi0xMC0yOFQwODo0NDoxMFoifQ" },
        "nodes": [
          {
            "id": "01a14d05-9b44-8000-801f-4e4f5449a2b8",
            "publishedAt": "2026-10-28T10:12:08.214Z",
            "occurredAt": "2026-10-28T10:12:07.000Z",
            "text": "Speed over 25 km/h in the depot. Check the driver.",
            "severity": "INFO",
            "title": "Speeding in the depot",
            "assetId": "01a1305f-50ec-8000-801f-41535354a5e1",
            "deviceId": "019dbec6-d380-8000-801f-44455643e216",
            "point": { "lat": 52.5207, "lng": 13.4049 }
          },
          {
            "id": "01a14c91-0e7a-8000-801f-4e4f5449c3d0",
            "publishedAt": "2026-10-28T08:44:11.902Z",
            "occurredAt": "2026-10-28T08:44:10.000Z",
            "text": "Ignition is on outside working hours.",
            "severity": "INFO",
            "title": "Ignition at night",
            "assetId": "01a1305f-50ec-8001-801f-41535354b7c2",
            "deviceId": "00000000-0000-8000-801f-444556430003",
            "point": null
          }
        ]
      }
    }
  }
}
```

The list works as follows:

* Order: Newest first by `occurredAt`. Notifications with the same time are ordered by ID. `orderBy: { field: OCCURRED_AT, direction: ASC }` gives the oldest first. No other field orders the list.
* Filter: `filter` accepts `assetIds`, `severities`, `occurredFrom`, and `occurredTo`, combined with AND. `occurredFrom` and `occurredTo` are inclusive.
* Retention: The list has the notifications published in the last 30 days. Older notifications are gone from the list, the counts, and `node(id:)`, whatever `occurredFrom` says.
* Counts: `total` and `unreadCount` count up to 1,000. Up to that number, `precision` is `EXACT`. Above it, `count` is `1000` and `precision` is `APPROXIMATE`, so show "1000+". Paging through the list still returns every notification.

Navixy checks access when you read the list, not when the notification was sent. A user who joins the workspace later sees the earlier notifications of the assets that they can read. A user who loses access to an asset no longer sees its notifications. The `node(id:)` query returns one notification, or `null` for one that you can't read or that is older than the retention.

## Unread notifications and the read mark

Navixy stores one read mark per user and workspace, `readUpTo`, and no read state per notification. A notification is unread when its `publishedAt` is later than `readUpTo`. Before the user's first mark, `readUpTo` is `null`, and every notification is unread. `unreadCount` counts the unread notifications with the same retention, access, and limit of 1,000 as `total`.

Navixy compares the mark with `publishedAt`, not `occurredAt`. A notification about an old event that arrives today is new to the user, although it sits far down a list ordered by `occurredAt`. Read and unread notifications can therefore alternate in the list. Show a notification as unread when `publishedAt > readUpTo`, whatever its position in the list.

To mark every notification as read, send `eventsMarkRead`:

```graphql
mutation MarkRead($upTo: DateTime!) {
  notifications {
    eventsMarkRead(input: { upTo: $upTo }) {
      readUpTo
    }
  }
}
```

Variables:

```json
{
  "upTo": "2026-10-28T10:12:08.214Z"
}
```

Response:

```json
{
  "data": {
    "notifications": {
      "eventsMarkRead": {
        "readUpTo": "2026-10-28T10:12:08.214Z"
      }
    }
  }
}
```

Follow these requirements when you set the mark:

* Send the largest `publishedAt` that your app showed, not the current time of the user's device. A mark from the clock would also mark the notifications published after your app read the list, which the user hasn't seen. A time later than the server's clock is reduced to the server's clock.
* The mark never moves back. The server keeps the later of the stored mark and the new one. A tab with an older list can't undo a newer mark from another tab or device. Take the stored mark from the response's `readUpTo`, not from what you sent.
* There is one mark per user and workspace. Every session and device of the user shares it. Other users have their own marks.

Rarely, a notification accepted while your app was reading the list can be saved a moment later with a `publishedAt` below the mark that you then send. It counts as read without being shown. The gap is usually tens of milliseconds.

## Stored list errors

The list and `eventsMarkRead` return errors in the usual format, at `errors[0].extensions.code`. Besides `UNAUTHORIZED` and `RATE_LIMITED`, which any request can return, they return these errors:

| Code | When | What to do |
| --- | --- | --- |
| `PRODUCT_DISABLED` | The workspace doesn't have the Notifications product. | Retry after an administrator turns on the product and you get a new token. |
| `PERMISSION_DENIED` | The token belongs to an API key, or the user can't read any asset. | Don't retry. The answer changes only when the user's roles change. |

`notifications` can't be `null`, so either error sets the whole `data` to `null`, including other fields of the same request. Send the notifications query in a request of its own. A notification that you can't read isn't an error: it's absent from the list and the counts.

## Limits

These limits apply to notifications on top of the [subscription limits](../../limits.md#subscription-limits) that every subscription shares:

| Limit | Default value | What happens when it's exceeded |
| --- | --- | --- |
| Open notification streams per user, on each API server | 12 | The new subscription ends with `RATE_LIMITED` |
| Notifications that wait for one session that doesn't read its messages | 256 | A new notification replaces the oldest waiting one |
| Stored notifications | 30 days from `publishedAt` | Older notifications are deleted |
| `total` and `unreadCount` | 1,000 | `count` is `1000`, with `precision` `APPROXIMATE` |

Each subscription also counts as one request toward your [request rate](../../limits.md#request-rate).

## See also

* [How rules work](../how-rules-work.md): Learn when a rule activates and sends a notification.
* [Reading rule history](reading-rule-history.md): Check whether a rule activated when a notification is missing.
* [Subscriptions](../../graphql-basics/#subscriptions): Learn the protocol and the connection rules of every subscription.
* [Notification stream](../api-reference/notifications/stream.md) and [Stored notifications](../api-reference/notifications/history.md): Look up every field of a notification, the list, and the read mark.
