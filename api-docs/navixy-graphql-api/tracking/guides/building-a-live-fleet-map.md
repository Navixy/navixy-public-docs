---
description: >-
  Show where your assets are, whether they are moving and reporting, and what
  their sensors say, then keep the picture current with a state stream.
---

# Building a live fleet map

{% include "../../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

A dispatcher opens a map and expects four answers for every vehicle: where it is, whether it is moving, whether its GPS device is still reporting, and what its sensors say. This guide builds that map from the Tracking API. To achieve that, you read the state of every asset in a workspace once and place each asset on the map. Then you render its readings with the help of the field dictionary and follow a stream that replaces each state as the device reports.

The Tracking API stores what devices report. [Business Data Repository](../../business-data-repository/overview.md) stores records: which assets exist, which GPS devices are linked to them, and which device is the primary one. This guide uses both APIs, because an asset on a map is a record from Business Data Repository placed at a position from Tracking.

## Prerequisites

You need your workspace ID for every operation on this page. It comes with your access credentials and is in the `workspace_id` claim of your access token. See [Authentication](../../authentication.md) for how tokens work and where the workspace ID comes from.

You also need assets with a linked GPS device. Tracking places an asset on the map based on the position of its primary device, so an asset without a primary device has no marker. To check which of your assets have one, run this query:

```graphql
query AssetsWithPrimaryDevice {
  bdr {
    assets(workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7", first: 50) {
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

The examples on this page use the workspace ID `7c9e6679-7425-40de-944b-e07fc1f90ae7` and the two assets above. Replace them with yours.

## How tracking state works

[Tracking overview](../overview.md#key-concepts) defines device state, asset state, readings, and the field dictionary. [Live state](../api-reference/state/README.md) and [Readings and the field dictionary](../api-reference/readings.md) document every field. This section names the fields that a map reads.

Tracking returns a state per device, not per asset, and doesn't merge them. `primaryDevice` returns the state of the primary device, and `devices` lists the state of every linked device.

| Question on the map                           | Field                                                                                                                | Notes                                                                                                                                         |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Is the device registered, active, or retired? | `Device.status` in [Business Data Repository](../../business-data-repository/api-reference/devices/README.md#device) | The lifecycle state. An administrator changes it, rarely and on purpose.                                                                      |
| Is the device reporting?                      | [`connectivity.status`](../api-reference/state/README.md#trackingconnectivitystatus)                                 | `ONLINE`, `IDLE`, `OFFLINE`, or `NEVER_REPORTED`. Calculated from the time of the last message, not stored.                                   |
| Is it moving?                                 | [`motion.status`](../api-reference/state/README.md#trackingmotionstatus), `motion.since`                             | `MOVING`, `STOPPED`, `PARKED`, or `UNKNOWN`, and the time when the current value began.                                                       |
| Where is it, and how old is that position?    | `position.point`, `position.at`, `position.fitness`                                                                  | `fitness: LAST_KNOWN` is a repeat of an old point. The age of a position is `position.at`, not `updatedAt`, which any message updates.        |
| What do its sensors say?                      | `readings`: `code`, `value`, `channel`, `unit`, `updatedAt`                                                          | Hot readings come with every state. Any other code is a cold lookup that must be bounded with `maxAge`. `hotFieldCodes` lists the hot fields. |
| What does a reading mean?                     | `fieldDefinitions`: `title`, `unit`, `nature`                                                                        | The dictionary. Titles are localized by the `Accept-Language` or `X-Locale` header, with English as the fallback.                             |
| What just happened?                           | `lastEvents`                                                                                                         | The one-shot events of the most recent message that had one, plus every alarm that is still active.                                           |

{% hint style="warning" %}
Don't build alerting on `lastEvents`. A state contains one one-shot event, and a later message with another one replaces it. On a stream, `batchWindowMs` combines several messages into one emission, and only the later event remains. Tracking has no event query and no event stream, so an app that must see every event can't get them from this API yet. Use `lastEvents` to show what is happening now, not as the source for notifications.
{% endhint %}

## Example scenario: A dispatcher's map for a delivery fleet

TransLog GmbH runs delivery trucks out of Hamburg. Their dispatcher needs a map that shows every truck with its connectivity and motion state, and opens a card with the latest sensor readings. The map must stay current without the app polling for changes.

{% stepper %}
{% step %}

### Load the field dictionary

Before you read any state, fetch two lists once. `hotFieldCodes` returns the hot fields: the readings that Tracking keeps in memory and returns with every state at no extra cost. Every other code is a cold field, which Tracking looks up in the message archive, so a request for it must be limited by `maxAge`. `fieldDefinitions` returns the field dictionary: every reading code the platform knows, with its title, unit, and nature. A reading arrives as a bare code such as `lls_level` and a value, and only the dictionary says what that is.

Run this query:

```graphql
query FieldDictionary {
  tracking {
    hotFieldCodes
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
      "hotFieldCodes": ["ignition", "board_voltage", "battery_level", "lls_level", "hw_mileage"],
      "fieldDefinitions": [
        {
          "code": "ignition",
          "title": "Ignition",
          "unit": null,
          "nature": "STATE",
          "valueType": "BOOL",
          "channelled": false,
          "deprecated": false,
          "replacedBy": null
        },
        {
          "code": "lls_level",
          "title": "Fuel level",
          "unit": "L",
          "nature": "GAUGE",
          "valueType": "FLOAT",
          "channelled": true,
          "deprecated": false,
          "replacedBy": null
        },
        {
          "code": "hw_mileage",
          "title": "Odometer",
          "unit": "km",
          "nature": "COUNTER",
          "valueType": "FLOAT",
          "channelled": false,
          "deprecated": false,
          "replacedBy": null
        }
      ]
    }
  }
}
```

Both results can be cached. Titles are localized by the request headers, and an entry is never renamed or retyped, only marked deprecated. [Readings and the field dictionary](../api-reference/readings.md#fielddefinitions) states the rules.
{% endstep %}

{% step %}

### Read the state of every asset in the workspace

Read the first page of asset states. Ask for the asset record, the state of the primary device, and the states of all devices. The `value` of a reading is a union, so select each type that you render with an inline fragment, the `... on TypeName { }` blocks below.

Run this query:

```graphql
query FleetState {
  tracking {
    assetStates(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
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
                clearedAt
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
                  "code": "lls_level",
                  "channel": 1,
                  "unit": "L",
                  "updatedAt": "2026-09-14T08:42:10Z",
                  "value": { "floatValue": 212.5 }
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

The forklift has no linked device, so its `devices` list is empty and `primaryDevice` is null. The truck reports, moves, and has three hot readings. Called without a `codes` argument, `readings` returns the hot fields only.

Request the next page with `after: endCursor` while `hasNextPage` is true. Order by `ASSET_TITLE` when you page through a large fleet. The `UPDATED_AT` order changes with every message, so a page boundary can shift between requests. See [Pagination](../../pagination.md) for the cursor rules.
{% endstep %}

{% step %}

### Place each asset on the map

The marker position is `primaryDevice.position.point`. Four properties of the state decide what a marker can show:

- An asset with `primaryDevice: null` has no position. Tracking positions an asset by its primary device only.
- An asset whose primary device has `position: null` has no position either. The device has never sent one, and its connectivity is usually `NEVER_REPORTED`.
- The age of a position is `position.at`, not `updatedAt`.
- `fitness: LAST_KNOWN` means that the device repeated an old point rather than observing a new one.

`heading` is meaningful only while `motion.status` is `MOVING`. On a stopped or parked device it is the direction of the last movement.
{% endstep %}

{% step %}

### Read the readings of an asset

Each reading's `code` maps to a `title` in the dictionary that you cached. A reading with a non-null `channel` is one of several values of the same field: `lls_level` on channel 1 and channel 2 are two tanks, not one value.

The unit of a reading is `TrackingReading.unit`, the unit that the message declared. It is null when the value is dimensionless or when the unit is unknown. The dictionary `unit` tells the two apart, because a dimensionless field has no unit there either. A value that arrived without a unit isn't in liters just because the dictionary shows liters for that field.

A value of type `TrackingClearedValue` means that the value no longer applies, for example a driver key that was removed. It is neither the previous value nor an empty one.

To read a cold field, name it in `codes` and bound the lookup with `maxAge`. This query reads the vehicle identification number that the device sent at most 30 days ago:

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

`maxAge` is required whenever `codes` names a field outside `hotFieldCodes`, and the number of cold codes per request is limited. A request over either limit fails with `VALIDATION_ERROR`. This limit exists because a cold lookup reads the message archive. Without it, a device that never sent the field would be searched to the end of the retention period. Read cold fields for one device at a time, not for every asset on a page.
{% endstep %}

{% step %}

### Follow the fleet with a state stream

Open a subscription on the asset states of the workspace. Ask for the opening snapshot in the same stream, so that your app doesn't query and subscribe separately and miss updates between the two. Narrow the readings to the codes that the map renders, because every emission is a full state.

Run this subscription:

```graphql
subscription FleetStream {
  trackingAssetStates(
    input: {
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      includeInitialState: true
      batchWindowMs: 1000
      readingCodes: ["ignition", "lls_level"]
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
                { "code": "lls_level", "channel": 1, "unit": "L", "value": { "floatValue": 212.5 } }
              ]
            }
          },
          "removal": null
        },
        {
          "assetId": "019d48ea-0752-8000-801f-415353540a12",
          "state": {
            "asset": { "id": "019d48ea-0752-8000-801f-415353540a12", "title": "Forklift F-07" },
            "primaryDevice": null
          },
          "removal": null
        }
      ]
    }
  }
}
```

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
                { "code": "lls_level", "channel": 1, "unit": "L", "value": { "floatValue": 212.1 } }
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
- When `removal` is set, `state` is null. `removal.reason` is `DELETED`, `UNLINKED`, `ACCESS_REVOKED`, or `FILTERED_OUT`, and the list will grow, so treat an unknown value as a removal.
- An asset that becomes visible after the subscription opened arrives as an ordinary change with its full state. A new truck with a device linked is one example. There is no separate "added" signal.
- `readingCodes` must be a subset of `hotFieldCodes`. A stream can't look cold fields up in the archive per device per message, so a cold code in the input is refused with `VALIDATION_ERROR`.

Leave `assetIds` out to follow every asset that the token can see. The number of assets that one subscription may follow has a server-side limit. Above it, the subscription is refused with `VALIDATION_ERROR` rather than truncated, and a fleet that grows past the limit while the stream is open closes the stream with the same error. For a very large workspace, open one subscription per asset group and pass the group's asset IDs in `assetIds`.

A reconnect opens a new subscription, and the new one delivers its own opening snapshot. `batchWindowMs: 1000` combines updates for the same asset within one second into one message and delivers messages as a batch. Set it to 0 to receive every report as it arrives.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You now have the position, connectivity, motion, and readings of every asset with a primary device, the dictionary to render the readings, and a stream that replaces each state as the device reports. The map needs no polling.
{% endhint %}

## Filtering fleet by connectivity, group, or area

`assetStates` takes a filter. Every device-level condition matches an asset when any of its devices satisfies it, because an asset's state is a list. This query lists the assets in one asset group whose devices are silent:

```graphql
query SilentTrucksInGroup {
  tracking {
    assetStates(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: {
        assetGroupIds: ["019d48ea-0752-8000-801f-414752500c31"]
        connectivityStatus: [IDLE, OFFLINE]
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

The filter has these fields:

| Field                | What it selects                                                                                                                                                                           |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `assetIds`           | Only these assets.                                                                                                                                                                        |
| `assetGroupIds`      | Assets in any of these asset groups.                                                                                                                                                      |
| `connectivityStatus` | Assets where some device has one of these connectivity values.                                                                                                                            |
| `motionStatus`       | Assets where some device has one of these motion values.                                                                                                                                  |
| `updatedSince`       | Assets where some device reported at or after this moment.                                                                                                                                |
| `within`             | Assets where some device's `position` is inside this GeoJSON area. A `LAST_KNOWN` repeat inside the area matches, because the filter answers where the device last reported itself to be. |

Use `within` with a polygon to list the vehicles in a depot or a city. For the rules of the `GeoJSON` scalar, see [Common resources](../../core-api-reference/common.md#geojson).

## Checking which assets are inside a geofence

A geofence is a geo object in Business Data Repository whose `geojsonData` contains a `Polygon` or a `MultiPolygon`. Read that geometry with `geoObject(id:)`, as [Creating geofences, POIs, and routes](../../business-data-repository/guides/creating-geofences-pois-and-routes.md) shows, then send it as the `within` filter. Inside a GraphQL document the keys have no quotes:

```graphql
query AssetsInDepot {
  tracking {
    assetStates(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: {
        within: {
          type: "Polygon"
          coordinates: [
            [
              [10.0400, 53.5170],
              [10.0500, 53.5170],
              [10.0500, 53.5240],
              [10.0400, 53.5240],
              [10.0400, 53.5170]
            ]
          ]
        }
      }
      first: 50
    ) {
      nodes {
        asset {
          title
        }
        primaryDevice {
          position {
            point {
              lat
              lng
            }
            at
            fitness
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
            "asset": { "title": "HH-TL 2041" },
            "primaryDevice": {
              "position": {
                "point": { "lat": 53.5201, "lng": 10.0448 },
                "at": "2026-09-14T08:03:47Z",
                "fitness": "GNSS"
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

The filter matches on the same position that the map shows, so a `LAST_KNOWN` repeat inside the area counts as inside. That is deliberate: it answers where the device last reported itself to be.

To answer the opposite question, whether one known position falls inside a geofence, use `containsPoints` on the geo object instead. See [Check test point containment](../../business-data-repository/guides/creating-geofences-pois-and-routes.md#check-test-point-containment) in the guide on geo objects.

{% hint style="warning" %}
Both checks answer only at the moment you run them, because Tracking raises no geofence event. An app that needs entry and exit alerts runs one of these checks on a schedule and compares each result with the previous one.
{% endhint %}

## Reading one asset or one device

`assetState(assetId:)` returns the state of one asset, and `deviceState(deviceId:)` the state of one device. Both return null when the entity doesn't exist or isn't visible to the token. A null isn't an error, so check it before you read fields. `deviceStates(deviceIds:)` returns several device states in the order requested, with null in place of each device that isn't visible.

## Error handling

The Tracking queries and subscriptions return errors in the shared format. See [Error handling](../../error-handling.md) for the response structure and the general codes.

| Code                | When it occurs                                                                                 | Resolution                                                                                  |
| ------------------- | ---------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| `VALIDATION_ERROR`  | `readings` names a cold code without `maxAge`, or names more cold codes than the limit allows. | Add `maxAge`, and read cold fields one card at a time instead of for every asset on a page. |
| `VALIDATION_ERROR`  | A subscription follows more assets than the limit allows, at open or after the fleet grew.     | Follow a subset with `assetIds`, one subscription per asset group.                          |
| `VALIDATION_ERROR`  | `readingCodes` on a subscription names a code that isn't in `hotFieldCodes`.                   | Remove the code from the subscription and read it on demand with `deviceState`.             |
| `PERMISSION_DENIED` | The token has no access to the workspace.                                                      | Check the workspace ID against the `workspace_id` claim of the token.                       |

A cold lookup without a bound returns this error:

```json
{
  "errors": [{
    "message": "Validation failed",
    "path": ["tracking", "deviceState", "readings"],
    "extensions": {
      "type": "https://api.navixy.com/errors/validation",
      "title": "Validation Error",
      "status": 400,
      "detail": "maxAge is required when codes names a field outside hotFieldCodes",
      "instance": "/graphql",
      "code": "VALIDATION_ERROR",
      "field": "maxAge",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-14T08:45:00.000Z"
    }
  }]
}
```

### How to handle cold reading limits

Read the `field` in `extensions` to see which argument failed. Add `maxAge` as an ISO 8601 duration such as `P30D`, and reduce the number of cold codes in one request. The hot fields from `hotFieldCodes` need no bound.

## See also

- [Live state](../api-reference/state/README.md): the state of a device and of an asset, with the queries, filter, and order
- [State streams](../api-reference/state/streams.md): the subscriptions, their batches, and the removal reasons
- [Readings and the field dictionary](../api-reference/readings.md): how a reading is built and what the dictionary says about it
- [Reading a vehicle trip history](reading-a-vehicle-trip-history.md): where a vehicle drove and where it stopped over a period
- [Creating assets and assigning devices](../../business-data-repository/guides/creating-assets-and-assigning-devices.md): registering assets and linking a primary device
- [Creating geofences, POIs, and routes](../../business-data-repository/guides/creating-geofences-pois-and-routes.md): drawing the geofences that the `within` filter and `containsPoints` test against
