---
description: >-
  Show where your assets are, whether they are moving and reporting, and what
  their sensors say, then keep the picture current with a state stream.
tags:
  - guide
  - beta
---

# Building a live fleet map

A dispatcher opens a map and expects four answers for every vehicle: where it is, whether it is moving, whether its GPS device is still reporting, and what its sensors say. This guide builds that map from the Tracking API. To build the map, you read the state of every asset in a workspace once and place each asset on the map. Then you render its readings and follow a stream that replaces each state as the device reports.

The Tracking API stores what devices report. [Business Data Repository](../../business-data-repository/README.md) stores records: which assets exist, which GPS devices are linked to them, and which device is the primary one. This guide uses both APIs, because an asset on a map is a record from Business Data Repository placed at a position from Tracking.

## Prerequisites

You need an access token for the workspace you work in. The API takes the workspace from the token, so no operation on this page asks for a workspace ID. See [Authentication](../../authentication.md) for how to get a token and how the API picks the workspace.

You also need assets with a linked GPS device. Tracking places an asset on the map based on the position of its primary device, so an asset without a primary device has no marker. To check which of your assets have one, run this query:

```graphql
query AssetsWithPrimaryDevice {
  bdr {
    assets(first: 50) {
      nodes {
        id
        title
        primaryDevice {
          id
          title
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assets": {
        "nodes": [
          {
            "id": "019d48ea-0752-8000-801f-415353540a11",
            "title": "HH-TL 2041",
            "primaryDevice": {
              "id": "019d48ea-0752-8000-801f-444556430b21",
              "title": "Teltonika FMC130 #8621"
            }
          },
          {
            "id": "019d48ea-0752-8000-801f-415353540a12",
            "title": "Forklift F-07",
            "primaryDevice": null
          }
        ]
      }
    }
  }
}
```

The truck has a primary device and appears on the map. The forklift has none, so the API has nothing to place. To link a device and mark it as primary, see [Assigning assets to devices](../../business-data-repository/guides/creating-assets-and-assigning-devices.md#assigning-assets-to-devices) in the assets guide.

The examples on this page use the two assets above. Replace their IDs with yours.

## How tracking state works

[Tracking overview](../README.md#key-concepts) defines device state, asset state, readings, and the field dictionary. [Live state](../api-reference/state/README.md) and [Readings and the field dictionary](../api-reference/readings.md) document every field. This section names the fields that a map reads.

Tracking returns a state per device, not per asset, and doesn't merge them. `primaryDevice` returns the state of the primary device, and `devices` lists the state of every linked device.

| Question on the map                           | Field                                                                                                                | Notes                                                                                                                                                  |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Is the device registered, active, or retired? | `Device.status` in [Business Data Repository](../../business-data-repository/api-reference/devices/README.md#device) | The lifecycle state. An administrator changes it, rarely and on purpose.                                                                               |
| Is the device connected?                      | [`connectivity.status`](../api-reference/state/README.md#trackingconnectivitystatus)                                 | `ONLINE`, `OFFLINE`, or `NEVER_REPORTED`. Calculated from the arrival time of the last message, not stored.                                            |
| Is it moving?                                 | [`motion.status`](../api-reference/state/README.md#trackingmotionstatus), `motion.since`                             | `MOVING`, `STOPPED`, `PARKED`, or `UNKNOWN`, and the time when the current value began.                                                                |
| Where is it, and how old is that position?    | `position.point`, `position.at`, `position.fitness`                                                                  | `fitness: LAST_KNOWN` is a repeat of an old point. The age of a position is `position.at`, not `updatedAt`, which any message updates.                 |
| What do its sensors say?                      | `readings`: `code`, `value`, `channel`, `unit`, `updatedAt`                                                          | Without `codes`, every field that the device has reported. Null when Tracking can't read the stored readings of the device.                          |
| What does a reading mean?                     | `fieldDefinitions`: `title`, `unit`, `nature`                                                                        | The field dictionary. It is empty at the moment, see [Read the field dictionary](#read-the-field-dictionary).                                             |
| What just happened?                           | `lastEvents`                                                                                                         | The one-shot events of the most recent message that had one, plus every alarm that is still active. Going `OFFLINE` doesn't remove an active alarm from the list. |

{% hint style="warning" %}
Don't build alerting on `lastEvents`. A state contains one one-shot event, and a later message with another one replaces it. On a stream, `batchWindowMs` combines several messages into one emission, and only the later event remains. Tracking has no event query and no event stream, so an app that must see every event can't get them from this API. Use `lastEvents` to show what is happening now, not as the source for notifications.
{% endhint %}

### Connectivity and motion thresholds

Four thresholds decide `connectivity.status` and `motion.status`. Read the values that apply in your workspace with `workspaceSettings`:

```graphql
query StatusThresholds {
  tracking {
    workspaceSettings {
      statusThresholds {
        offlineAfter
        minStopDuration
        movingSpeedThreshold
        stopRadius
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "tracking": {
      "workspaceSettings": {
        "statusThresholds": {
          "offlineAfter": "PT10M",
          "minStopDuration": "PT5M",
          "movingSpeedThreshold": 3.0,
          "stopRadius": 100.0
        }
      }
    }
  }
}
```

| Threshold              | What it decides                                                                                                                         |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `offlineAfter`         | A device is `OFFLINE` when no message of any kind has arrived for this long, and `ONLINE` otherwise. A device that never sent a message is `NEVER_REPORTED`. |
| `movingSpeedThreshold` | A device is `MOVING` when its speed is above this value, in km/h.                                                                       |
| `minStopDuration`      | A device that stands still is `STOPPED` at first and `PARKED` once it has stood still this long.                                        |
| `stopRadius`           | Points within this radius, in meters, count as the same place. The radius keeps GPS drift from counting as movement.                          |

Every workspace uses the same values. Read them from the API instead of copying them into your app. A timeline uses the same stop values by default. A vehicle that is `PARKED` on the map is therefore at a stop in its [trip history](reading-a-vehicle-trip-history.md).

Connectivity is measured from `connectivity.lastReceivedAt`, the time when the last message arrived. It isn't measured from `lastMessageAt`, the time that the device wrote into the message. A device that sends messages it buffered earlier is `ONLINE`, although its `lastMessageAt` can be old.

## Example scenario: A dispatcher's map for a delivery fleet

TransLog GmbH runs delivery trucks out of Hamburg. Their dispatcher needs a map that shows every truck with its connectivity and motion state, and opens a panel with the latest sensor readings. The map must stay current without the app polling for changes.

To build the map, follow these steps:

{% stepper %}
{% step %}

### Read the field dictionary

Before you read any state, fetch the field dictionary once. `fieldDefinitions` returns the title, unit, and nature of every reading code.

Run this query:

```graphql
query FieldDictionary {
  tracking {
    fieldDefinitions {
      code
      title
      unit
      nature
      valueType
      channelled
      deprecated
      replacedBy
    }
  }
}
```

Response:

```json
{
  "data": {
    "tracking": {
      "fieldDefinitions": []
    }
  }
}
```

The field dictionary is empty. `fieldDefinitions` returns an empty list, and `definition` is null on every reading. Render a reading by its `code` and `unit`, and keep your own titles for the codes that your app shows.

Cache the list. [Readings and the field dictionary](../api-reference/readings.md#fielddefinitions) states when to fetch the dictionary again.
{% endstep %}

{% step %}

### Read the state of every asset in the workspace

Read the first page of asset states. Ask for the asset record, the state of the primary device, and the states of all devices. The `value` of a reading is a union, so select each type that you render with an inline fragment, the `... on TypeName { }` blocks below.

Run this query:

```graphql
query FleetState {
  tracking {
    assetStates(
      orderBy: { field: ASSET_TITLE, direction: ASC }
      first: 50
    ) {
      nodes {
        asset {
          id
          title
        }
        updatedAt
        primaryDevice {
          device {
            id
            title
          }
          connectivity {
            status
            lastMessageAt
            lastPositionAt
          }
          position {
            point {
              lat
              lng
            }
            speed
            heading
            fitness
            at
          }
          motion {
            status
            since
          }
          readings {
            code
            channel
            unit
            updatedAt
            value {
              ... on TrackingBoolValue {
                boolValue
              }
              ... on TrackingFloatValue {
                floatValue
              }
              ... on TrackingIntValue {
                intValue
              }
              ... on TrackingClearedValue {
                cleared
              }
            }
          }
          lastEvents {
            code
            occurredAt
            since
          }
        }
        devices {
          device {
            id
            title
          }
          connectivity {
            status
          }
        }
      }
      pageInfo {
        hasNextPage
        endCursor
      }
      total {
        count
      }
    }
  }
}
```

Response:

{% code expandable="true" %}
```json
{
  "data": {
    "tracking": {
      "assetStates": {
        "nodes": [
          {
            "asset": {
              "id": "019d48ea-0752-8000-801f-415353540a12",
              "title": "Forklift F-07"
            },
            "updatedAt": null,
            "primaryDevice": null,
            "devices": []
          },
          {
            "asset": {
              "id": "019d48ea-0752-8000-801f-415353540a11",
              "title": "HH-TL 2041"
            },
            "updatedAt": "2026-09-14T08:42:10Z",
            "primaryDevice": {
              "device": {
                "id": "019d48ea-0752-8000-801f-444556430b21",
                "title": "Teltonika FMC130 #8621"
              },
              "connectivity": {
                "status": "ONLINE",
                "lastMessageAt": "2026-09-14T08:42:10Z",
                "lastPositionAt": "2026-09-14T08:42:10Z"
              },
              "position": {
                "point": { "lat": 53.5511, "lng": 9.9937 },
                "speed": 47.0,
                "heading": 112.0,
                "fitness": "GNSS",
                "at": "2026-09-14T08:42:10Z"
              },
              "motion": {
                "status": "MOVING",
                "since": "2026-09-14T08:31:05Z"
              },
              "readings": [
                {
                  "code": "ignition",
                  "channel": null,
                  "unit": null,
                  "updatedAt": "2026-09-14T08:42:10Z",
                  "value": { "boolValue": true }
                },
                {
                  "code": "board_voltage",
                  "channel": null,
                  "unit": "V",
                  "updatedAt": "2026-09-14T08:42:10Z",
                  "value": { "floatValue": 27.6 }
                },
                {
                  "code": "hw_mileage",
                  "channel": null,
                  "unit": "km",
                  "updatedAt": "2026-09-14T08:42:10Z",
                  "value": { "floatValue": 184203.7 }
                }
              ],
              "lastEvents": []
            },
            "devices": [
              {
                "device": {
                  "id": "019d48ea-0752-8000-801f-444556430b21",
                  "title": "Teltonika FMC130 #8621"
                },
                "connectivity": { "status": "ONLINE" }
              }
            ]
          }
        ],
        "pageInfo": {
          "hasNextPage": false,
          "endCursor": "YXNzZXQ6MDE5ZDQ4ZWEtMDc1Mi04MDAwLTgwMWYtNDE1MzUzNTQwYTEx"
        },
        "total": { "count": 2 }
      }
    }
  }
}
```
{% endcode %}

The forklift has no linked device, so its `devices` list is empty and `primaryDevice` is null. An empty `devices` list appears also when the token has no access to any device of the asset. The truck reports and moves. Called without a `codes` argument, `readings` returns every field that the device has reported, so a real list is often longer than the three in the example.

Request the next page with `after: endCursor` while `hasNextPage` is true. Order by `ASSET_TITLE` when you page through a large fleet. Under the `UPDATED_AT` order, an asset that reports between two requests moves to another place in the order. The next page can then skip the asset or return it a second time. See [Pagination](../../pagination.md) for the cursor rules.
{% endstep %}

{% step %}

### Place each asset on the map

The marker position is `primaryDevice.position.point`. Four properties of the state decide what a marker can show:

- An asset with `primaryDevice: null` has no position. Tracking positions an asset by its primary device only.
- An asset whose primary device has `position: null` has no position either. The device has never sent one, and its connectivity is usually `NEVER_REPORTED`.
- The age of a position is `position.at`, not `updatedAt`.
- `fitness: LAST_KNOWN` means that the device repeated an old point rather than observing a new one.

`heading` is meaningful only while `motion.status` is `MOVING`. On a stopped or parked device it is whatever the device reported with its last position, and many devices report 0 while stationary.
{% endstep %}

{% step %}

### Read the readings of an asset

Show each reading by its `code`, because the field dictionary is empty. A reading with a non-null `channel` is one of several values of the same field: `lls_level` on channel 1 and channel 2 are two tanks, not one value.

The unit of a reading is `TrackingReading.unit`, the unit that the message declared. It is null when the value is dimensionless or when the unit is unknown.

`hw_mileage` (odometer) and `can_engine_hours` (engine hours) are counters that the device keeps. Tracking stores the value that the device sends, in kilometers and hours. The API has no operation that sets a start value or corrects a counter. To change a counter, change it on the device, if the device supports that.

Depending on the device, readings can include `gsm_signal_level` (signal strength, with the unit `{dBm}`), `gsm_signal_quality` (signal on the device's own scale, for example `{bars}` on Teltonika devices), `gsm_operator_code`, `roaming`, and `battery_level` (with the unit `%`). Read `gsm_signal_quality` together with its `unit`, because the scale depends on the device.

A value of type `TrackingClearedValue` means that the value no longer applies, for example a driver key that was removed. It is neither the previous value nor an empty one.

To read only some fields, name them in `codes`. A code covers every channel of the field. To leave out values that are too old to show, add `maxAge`: it drops every reading whose message is older than that. Both arguments are optional. This query reads the vehicle identification number, if the device sent it within the last 30 days:

```graphql
query TruckVin {
  tracking {
    deviceState(deviceId: "019d48ea-0752-8000-801f-444556430b21") {
      readings(codes: ["obd_vin"], maxAge: "P30D") {
        code
        updatedAt
        value {
          ... on TrackingStringValue {
            stringValue
          }
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "tracking": {
      "deviceState": {
        "readings": [
          {
            "code": "obd_vin",
            "updatedAt": "2026-09-01T06:12:44Z",
            "value": { "stringValue": "WDB9634031L812345" }
          }
        ]
      }
    }
  }
}
```

Three rules decide which readings a device has:

- A reading can lag a little behind `position` and `connectivity`, because Tracking may read it from stored messages rather than from its live state.
- Tracking keeps the newest value of each field as messages arrive and doesn't fill in older history. A field that the device last sent before Tracking began keeping values is missing until the device sends it again. For a field that a device sends only when it changes, such as ignition or a digital input, that is the next change.
- When Tracking can't read the stored readings of a device, `readings` is null for that device, with a `SERVICE_UNAVAILABLE` error at its path. The rest of the state is still returned, so the map can show the position and the statuses. A later request may succeed.
{% endstep %}

{% step %}

### Connect to the subscription endpoint

A subscription doesn't use one POST request and one response. It keeps a connection open, and the API sends a message on it every time the state that you follow changes. The API accepts subscriptions over two transports:

| Transport                                 | Address                                                                                        | Where the access token goes                                    |
| ----------------------------------------- | ---------------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| WebSocket, with the `graphql-transport-ws` protocol | `wss://nvx4.eu.navixy.com/api/graphql/ws`                                                    | The `Authorization` field in the payload of `connection_init` |
| Server-sent events                        | `https://nvx4.eu.navixy.com/api/graphql`, the same URL as for queries, with the `Accept: text/event-stream` header | The `Authorization` header                                     |

Use the WebSocket for a map in a web or mobile app, because one connection can run several subscriptions. Use server-sent events when your client can't open a WebSocket, or to test a subscription with `curl`.

To follow the fleet over the WebSocket, send these messages in order:

1. Open the connection to `wss://nvx4.eu.navixy.com/api/graphql/ws` with the subprotocol `graphql-transport-ws`.
2. Send `connection_init` with your token: `{"type": "connection_init", "payload": {"Authorization": "Bearer <ACCESS_TOKEN>"}}`.
3. Wait for `connection_ack`.
4. Send `subscribe` with an ID that you choose and the subscription document of the next step: `{"id": "fleet-1", "type": "subscribe", "payload": {"query": "subscription FleetStream { ... }"}}`.
5. Read the `next` messages with that ID. Each `next` message contains one batch.

The [graphql-transport-ws protocol](https://github.com/enisdenjo/graphql-ws/blob/master/PROTOCOL.md) defines these messages, and GraphQL client libraries such as `graphql-ws` send them for you. In `graphql-ws`, don't set the `disablePong` option. The server closes a connection after 45 seconds without any message from the client, and a client that only listens sends nothing except its `pong` replies. The subscriptions of a closed connection end with it.

The WebSocket connection follows the rules in [Subscriptions](../../graphql-basics/#subscriptions): the deadline for `connection_init`, the `ping` messages, the close codes, and the number of open connections. [Subscription limits](../../limits.md#subscription-limits) lists their values. Two limits matter most for a fleet map:

- One user or API key can have up to 12 open tracking streams over both transports. A subscription over that limit is refused with `RATE_LIMITED`. The refusal is temporary: a slot frees when one of your streams ends, and a connection that dropped without closing frees its slots once the server closes it for silence. Wait before you retry, and wait longer after each refusal.
- Every subscription that you send counts as one request toward your [request rate](../../limits.md#request-rate), even one that the server then refuses. Open the streams your map needs once, and keep them open instead of subscribing again for every change of view.

To test the stream with server-sent events, send the subscription as a POST request:

```bash
curl -L \
  --no-buffer \
  --request POST \
  --url 'https://nvx4.eu.navixy.com/api/graphql' \
  --header 'Authorization: Bearer <ACCESS_TOKEN>' \
  --header 'Content-Type: application/json' \
  --header 'Accept: text/event-stream' \
  --data '{
    "query": "subscription FleetStreamTest { trackingAssetStates(input: { readingCodes: [\"ignition\"] }) { initialSnapshotComplete changes { assetId } } }"
  }'
```

The response stays open. Every batch arrives as a `next` event, with the batch as JSON in its `data` field:

```
event: next
data: {"data":{"trackingAssetStates":{"initialSnapshotComplete":true,"changes":[{"assetId":"019d48ea-0752-8000-801f-415353540a11"}]}}}
```

The server keeps a server-sent events stream open with no time limit and sends a keep-alive comment every 15 seconds.

A stream ends when the access token expires, with an `UNAUTHORIZED` error. Get a new token and open a new subscription. The new subscription delivers its own opening snapshot, so your app needs no other recovery step.

Errors arrive in two ways over the WebSocket:

- A document that the API can't run gets one `next` message with an `errors` array, then `complete`. Examples are a query or a mutation, a document with a syntax or schema error, and an 11th subscription on one connection.
- Every other error ends the subscription with one `error` message and no `complete`. The `error` message is used for invalid input values, the stream limits, token expiry, and `SERVICE_UNAVAILABLE`.
{% endstep %}

{% step %}

### Follow the fleet with a state stream

Open a subscription on the asset states of the workspace. Ask for the opening snapshot in the same stream, so that your app doesn't query and subscribe separately and miss updates between the two. Narrow the readings to the codes that the map renders, because every emission is a full state.

Run this subscription:

```graphql
subscription FleetStream {
  trackingAssetStates(
    input: {
      includeInitialState: true
      batchWindowMs: 1000
      readingCodes: ["ignition", "board_voltage"]
    }
  ) {
    fromInitialSnapshot
    initialSnapshotComplete
    changes {
      assetId
      state {
        asset {
          id
          title
        }
        primaryDevice {
          connectivity {
            status
          }
          position {
            point {
              lat
              lng
            }
            heading
            fitness
            at
          }
          motion {
            status
            since
          }
          readings {
            code
            channel
            unit
            value {
              ... on TrackingBoolValue {
                boolValue
              }
              ... on TrackingFloatValue {
                floatValue
              }
            }
          }
        }
      }
      removal {
        reason
        at
      }
    }
  }
}
```

The input fields have these defaults:

| Field                 | Default           | Meaning                                                                                                          |
| --------------------- | ----------------- | ---------------------------------------------------------------------------------------------------------------- |
| `assetIds`            | Every asset       | Every asset with a linked device that the token can see.                                                         |
| `includeInitialState` | `true`            | Deliver the current state of every followed asset before the live updates.                                       |
| `batchWindowMs`       | `2000`            | Combine updates for the same asset within this many milliseconds. The allowed range is 1,000 to 5,000.           |
| `readingCodes`        | Every reading     | Deliver only these readings, every channel included. Any code can be named. `[]` delivers no readings.           |
| `eventCodes`          | `[]`, no events   | Deliver only these events in `lastEvents`. Null delivers every event.                                            |

The first emission is the opening snapshot. It may span several batches, and the last one has `initialSnapshotComplete: true`:

```json
{
  "data": {
    "trackingAssetStates": {
      "fromInitialSnapshot": true,
      "initialSnapshotComplete": true,
      "changes": [
        {
          "assetId": "019d48ea-0752-8000-801f-415353540a11",
          "state": {
            "asset": { "id": "019d48ea-0752-8000-801f-415353540a11", "title": "HH-TL 2041" },
            "primaryDevice": {
              "connectivity": { "status": "ONLINE" },
              "position": {
                "point": { "lat": 53.5511, "lng": 9.9937 },
                "heading": 112.0,
                "fitness": "GNSS",
                "at": "2026-09-14T08:42:10Z"
              },
              "motion": { "status": "MOVING", "since": "2026-09-14T08:31:05Z" },
              "readings": [
                { "code": "ignition", "channel": null, "unit": null, "value": { "boolValue": true } },
                { "code": "board_voltage", "channel": null, "unit": "V", "value": { "floatValue": 27.6 } }
              ]
            }
          },
          "removal": null
        }
      ]
    }
  }
}
```

The forklift isn't in the snapshot. A stream follows only assets that have a linked device, because an asset without a device has no reports to deliver. Read such assets with `assetStates`.

Every later emission is a batch of changes. Each change is either the full current state of one asset or a notice that the asset left the stream:

```json
{
  "data": {
    "trackingAssetStates": {
      "fromInitialSnapshot": false,
      "initialSnapshotComplete": false,
      "changes": [
        {
          "assetId": "019d48ea-0752-8000-801f-415353540a11",
          "state": {
            "asset": { "id": "019d48ea-0752-8000-801f-415353540a11", "title": "HH-TL 2041" },
            "primaryDevice": {
              "connectivity": { "status": "ONLINE" },
              "position": {
                "point": { "lat": 53.5534, "lng": 10.0012 },
                "heading": 98.0,
                "fitness": "GNSS",
                "at": "2026-09-14T08:42:40Z"
              },
              "motion": { "status": "MOVING", "since": "2026-09-14T08:31:05Z" },
              "readings": [
                { "code": "ignition", "channel": null, "unit": null, "value": { "boolValue": true } },
                { "code": "board_voltage", "channel": null, "unit": "V", "value": { "floatValue": 27.5 } }
              ]
            }
          },
          "removal": null
        }
      ]
    }
  }
}
```

The stream has these properties:

- Every `state` is the whole current state of the asset, not a difference. Replacing your copy with it is enough, and a reconnect needs no reconciliation.
- `initialSnapshotComplete` is true on exactly one batch. A subscription that matches no asset still receives one empty batch with the flag set.
- About every 15 seconds, the stream also sends an empty batch with both flags false. Treat it as a sign that the stream is open, not as an error.
- When `removal` is set, `state` is null. `removal.reason` is `DELETED`, `UNLINKED`, or `ACCESS_REVOKED`, and the list can grow, so treat an unknown value as a removal.
- An asset that becomes visible after the subscription opened arrives as an ordinary change with its full state. A new truck with a device linked is one example. There is no separate "added" signal.
- `readings` in a state holds every reading of the device that `readingCodes` lets through. A field that the device sent once stays in every later emission, even when the message behind the emission didn't carry it.
- A subscription that selects `readings` loads the stored readings of its devices before the opening snapshot. A device linked to a followed asset while the stream is open appears at a later membership refresh, usually within a minute, once its readings are loaded. When the readings can't be loaded, the subscription is refused or closed with `SERVICE_UNAVAILABLE`. Open a new subscription. A subscription that doesn't select `readings` isn't affected. `readingCodes: []` doesn't avoid the loading: leave `readings` out of the selection.
- Read every message as it arrives. A stream whose client doesn't read a message for 30 seconds closes with `SERVICE_UNAVAILABLE`.

Leave `assetIds` out to follow every asset that the token can see. The number of assets that one subscription may follow has a server-side limit. Above it, the subscription is refused with `VALIDATION_ERROR` rather than truncated, and a fleet that grows past the limit while the stream is open closes the stream with the same error. For a very large workspace, open one subscription per asset group and pass the IDs of the group's assets in `assetIds`.

A reconnect opens a new subscription, and the new subscription delivers its own opening snapshot. `batchWindowMs: 1000` combines updates for the same asset within one second into one message and delivers messages as a batch. Device updates arrive at most once per window, so at least 1,000 ms apart. One window can still arrive as several batches, and changes that Tracking calculates, such as a device going `OFFLINE`, arrive outside the window. A device that reports twice before the next batch still appears once, with its latest state.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You now have the position, connectivity, motion, and readings of every asset with a primary device, and the thresholds that decide its statuses. You also have a stream that replaces each state as the device reports, so the map needs no polling.
{% endhint %}

## Filtering fleet by connectivity, group, or area

`assetStates` takes a filter. Every device-level condition matches an asset when any of its devices satisfies it, because an asset's state is a list. This query lists the assets in one asset group whose devices aren't connected:

```graphql
query SilentTrucksInGroup {
  tracking {
    assetStates(
      filter: {
        assetGroupIds: ["019d48ea-0752-8000-801f-414752500c31"]
        connectivityStatus: [OFFLINE]
      }
      first: 50
    ) {
      nodes {
        asset {
          title
        }
        primaryDevice {
          connectivity {
            status
            lastMessageAt
          }
        }
      }
      total {
        count
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "tracking": {
      "assetStates": {
        "nodes": [
          {
            "asset": { "title": "HH-TL 1877" },
            "primaryDevice": {
              "connectivity": {
                "status": "OFFLINE",
                "lastMessageAt": "2026-09-13T19:04:51Z"
              }
            }
          }
        ],
        "total": { "count": 1 }
      }
    }
  }
}
```

To include devices that never sent a message, add `NEVER_REPORTED` to `connectivityStatus`.

The filter has these fields:

| Field                | What it selects                                                                                                                                                                           |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `assetIds`           | Only these assets. An empty list selects every asset.                                                                                                                                     |
| `assetGroupIds`      | Assets in any of these asset groups.                                                                                                                                                      |
| `connectivityStatus` | Assets where some device has one of these connectivity values.                                                                                                                            |
| `motionStatus`       | Assets where some device has one of these motion values.                                                                                                                                  |
| `updatedSince`       | Assets where some device reported at or after this moment.                                                                                                                                |

The filter has no area condition. To find the assets inside a depot or a city, see [Checking which assets are inside a geofence](building-a-live-fleet-map.md#checking-which-assets-are-inside-a-geofence).

## Checking which assets are inside a geofence

A geofence is a geo object in Business Data Repository whose `geojsonData` is a `Polygon` or a `MultiPolygon`. Tracking has no area filter. To find the assets inside a geofence, read their positions from Tracking, then ask Business Data Repository whether the geofence covers each position.

First, read the positions, as in [Read the state of every asset in the workspace](building-a-live-fleet-map.md#read-the-state-of-every-asset-in-the-workspace). The `point` of each device's `position` is the point to test.

Then send the positions to the geofence's `containsPoint` field. The field takes one point, so give each position its own alias. This query tests two trucks against the depot:

```graphql
query DepotCoversTrucks {
  bdr {
    geoObject(id: "019d48ea-0752-8000-801f-47454f420c41") {
      title
      truck2041: containsPoint(point: { lat: 53.5201, lng: 10.0448 })
      truck2042: containsPoint(point: { lat: 53.5302, lng: 10.0611 })
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "geoObject": {
        "title": "Hamburg depot",
        "truck2041": true,
        "truck2042": false
      }
    }
  }
}
```

The position of `truck2041` is inside the depot, and the position of `truck2042` is outside. One request can test many positions, up to the [query complexity limit](../../limits.md#query-complexity): each alias counts as one field.

To ask the other way round, which geofences one asset is in, filter the geo objects by its position:

```graphql
query GeofencesOfTruck {
  bdr {
    geoObjects(filter: { containsPoint: { lat: 53.5201, lng: 10.0448 } }, first: 20) {
      nodes {
        id
        title
      }
    }
  }
}
```

The response lists every geo object that you can read whose area covers the point, in the same shape as any other `geoObjects` query.

These rules decide what a geofence covers:

* A point on the boundary of the area is inside. A point inside a hole of a polygon is outside, and a point on the boundary of the hole is inside.
* Only a well-formed `Polygon` or `MultiPolygon` has an area. A geo object with any other geometry, such as a `Point`, a `LineString`, or a `GeometryCollection`, covers no point: `containsPoint` returns `false`, and the filter never selects it.
* Edges are straight lines in longitude and latitude. Longitude 180 and -180 are different points, so draw an area that crosses the 180th meridian as two polygons.

The test uses the position that you send. Tracking's `position` is where the device last reported itself to be, including a `LAST_KNOWN` repeat. Check `fitness` and `at` before you count an old position as inside.

{% hint style="warning" %}
The result is true for the moment when you run the queries, because Navixy doesn't send an event when an asset enters or leaves a geo object. An app that needs entry and exit alerts runs the queries on a schedule and compares each result with the previous one.
{% endhint %}

## Reading one asset or one device

`assetState(assetId:)` returns the state of one asset, and `deviceState(deviceId:)` the state of one device. Both return null when the entity doesn't exist or isn't visible to the token. A null isn't an error, so check it before you read fields. `deviceStates(deviceIds:)` returns several device states in the order requested, with null in place of each device that isn't visible.

An ID that isn't a valid platform ID fails with `VALIDATION_ERROR` instead of returning null.

## Error handling

The Tracking queries and subscriptions return errors in the shared format. See [Error handling](../../error-handling.md) for the response structure and the general codes.

| Code                  | When it occurs                                                                                                              | Resolution                                                                                                         |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| `VALIDATION_ERROR`    | A subscription follows more assets than the limit allows, at open or after the fleet grew.                                  | Follow a subset with `assetIds`, one subscription per asset group.                                                 |
| `VALIDATION_ERROR`    | `batchWindowMs` on a subscription is outside 1,000 to 5,000. `0` is refused too.                                           | Leave `batchWindowMs` out for the default of 2,000, or set a value in the range.                                   |
| `RATE_LIMITED`        | The user or API key already has the maximum number of open streams, or the subscription went over your request rate. Each subscription counts as one request, in the same budget as your HTTP requests. The error has no `field`. | Wait before you retry, and wait longer after each refusal: a slot frees when one of your streams ends. Close a stream you no longer need, or follow more assets in one subscription. See [Request rate](../../limits.md#request-rate). |
| `PERMISSION_DENIED`   | The API could not tell which workspace the token acts in, or the workspace changed while a stream was open.                  | Read `bdr { workspace { id title } }` to see the workspace, and request a token for one workspace if it fails too. |
| `PRODUCT_DISABLED`    | Tracking isn't enabled for your workspace in Navixy Console. A subscription is refused before the stream opens. The whole `data` object is `null`. See [What your roles and products allow](../../authentication.md#what-your-roles-and-products-allow). | Enable Tracking for the workspace in Console and request a new token.                                             |
| `UNAUTHORIZED`        | The access token expired while a stream was open.                                                                           | Get a new token and open a new subscription.                                                                       |
| `SERVICE_UNAVAILABLE` | Tracking isn't available, for example while it starts. `extensions.dependency` is `tracking`. A stream also closes with this code when the client doesn't read its messages. `readings` is null with this code when Tracking can't read the stored readings of a device. A stream that selects `readings` is refused or closed with it when they can't be loaded. Errors on a stream carry no `dependency` or `field`. | Retry after a short delay. For a stream, open a new subscription and read every message as it arrives.             |

### How to handle missing readings

A device can lack readings for two reasons, and the response tells them apart:

- `readings` is an empty list, or lacks a field: the device hasn't sent that field since Tracking began keeping values, or not within `maxAge`. Show the field as not reported yet.
- `readings` is null, with a `SERVICE_UNAVAILABLE` error whose `path` ends in `readings`: Tracking couldn't read the stored readings of this device. Show the rest of the state, mark the readings as unavailable, and request them again later.

## See also

- [Live state](../api-reference/state/README.md): Learn about the state of a device and of an asset, with the queries, filter, order, and status thresholds.
- [State streams](../api-reference/state/streams.md): Learn about the subscription, its batches, and the removal reasons.
- [Readings and the field dictionary](../api-reference/readings.md): Learn how a reading is built and what the dictionary says about it.
- [Reading a vehicle trip history](reading-a-vehicle-trip-history.md): Find where a vehicle drove and where it stopped over a period.
- [Creating assets and assigning devices](../../business-data-repository/guides/creating-assets-and-assigning-devices.md): Register assets and link a primary device.
- [Creating geofences, POIs, and routes](../../business-data-repository/guides/creating-geofences-pois-and-routes.md): Draw the geofences that `containsPoint` tests positions against.
