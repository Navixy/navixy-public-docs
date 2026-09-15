---
description: >-
  Read a vehicle's trip history with Navixy GraphQL API: request a period,
  receive trips and stops with totals, and load the route of each trip.
---

# Reading a vehicle trip history

{% include "../../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

Read the trip history of a vehicle for a period: its trips and stops, their distances and durations, the route of each trip, and the rules that identified the stops. This is the data behind a trip history view, and the guide follows the requests that a fleet manager's history view makes.

This guide uses Tracking in Navixy GraphQL API for GPS trip history and Business Data Repository for the vehicle and its assigned device. It also states what the API returns for incomplete results and missing data, so that an application doesn't present them as complete.

## Prerequisites

You need an access token and the ID of an asset in your workspace. See [Authentication](../../authentication.md) for obtaining credentials. The examples use a fictional truck, HH-TL 2041.

Read the asset and its primary device:

```graphql
query TruckPrimaryDevice {
  bdr {
    asset(id: "019d48ea-0752-8000-801f-415353540a11") {
      id
      title
      primaryDevice {
        id
        title
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
      "asset": {
        "id": "019d48ea-0752-8000-801f-415353540a11",
        "title": "HH-TL 2041",
        "primaryDevice": {
          "id": "019d48ea-0752-8000-801f-444556430b21",
          "title": "Teltonika FMC130 #8621"
        }
      }
    }
  }
}
```

Use the primary device as the default source. This is an application choice: the Tracking API returns history per device and doesn't merge the histories of several devices.

An asset with no primary device has no default source, but it can still have history on its other assigned devices, and a device can be assigned to it. See [Assigning assets to devices](../../business-data-repository/guides/creating-assets-and-assigning-devices.md#assigning-assets-to-devices).

For this scenario, assume that the selected device was assigned to the vehicle throughout the requested period. The current assignment alone doesn't establish which device represented the vehicle in the past.

## How a trip history is returned

[Tracking overview](../overview.md#key-concepts) defines trips and stops, which the Tracking API calculates on each request rather than store them. A timeline contains separate `tracks` and `stops` lists, totals, and the segmentation policy that produced them. Segmentation is the set of rules that divides messages into trips and stops.

| Part of a history view      | API data                                          | What the API provides                                                            |
| --------------------------- | ------------------------------------------------- | -------------------------------------------------------------------------------- |
| Vehicle and source selector | Business Data Repository asset and device records | The assets, their devices, and which device is primary.                          |
| Period selector             | `from` and `to`                                   | The period as two UTC timestamps.                                                |
| Stop settings               | `segmentation`                                    | The requested policy, and in the response the policy that actually applied.      |
| Trip and stop list          | `tracks` and `stops`                              | Two separate lists, each segment with its kind, times, and duration.             |
| Route map                   | `tracks.points`                                   | The points of each trip, returned with the trip.                                 |
| Totals and completeness     | Totals and `continueFrom`                         | Totals for the returned part, and a marker when the part isn't the whole period. |

Trip and stop IDs identify segments within a response. No query accepts a trip ID, and changing the period or policy changes IDs. An ID is valid only within the result that returned it.

## Example scenario: Reviewing a trip

A fleet manager reviews truck HH-TL 2041 for September 13, 2026, in Hamburg. The manager needs to locate a short stop, inspect the route around it, and compare trip and stop durations.

{% stepper %}
{% step %}

### Select the period and stop policy

`from` and `to` are UTC timestamps. Convert the calendar date in the user's time zone into two separate timestamps, because a local day isn't always 24 hours long when a time-zone offset changes. For this example, the zone is `Europe/Berlin`, and September 13 runs from `2026-09-12T22:00:00Z` to `2026-09-13T22:00:00Z`.

The example requests the following stop policy. These values are choices for the scenario, not platform defaults:

| Parameter              | Example value | Purpose                                                                               |
| ---------------------- | ------------- | ------------------------------------------------------------------------------------- |
| `minStopDuration`      | `PT2M`        | Include pauses of at least two minutes as stops. Shorter pauses remain within a trip. |
| `stopRadius`           | `50`          | Treat points within a radius of 50 meters as the same place.                          |
| `movingSpeedThreshold` | `3`           | Treat speeds at or below 3 km/h as not moving.                                        |

The device policy takes precedence over the request policy. When the device has no policy, the Tracking API uses the request policy, or the platform default if neither supplies one. The response returns the `segmentation` values that applied and their `source`, so read the policy from the response rather than from the request.
{% endstep %}

{% step %}

### Load trips, stops, and route points

Request the timeline with the selected device, period, and policy. Request route points in the same query: there is no query that returns the points of a trip by ID, so a second request would produce a new result with different IDs.

`includePoints` defaults to false. Here it is true, with `maxPoints: 500` to limit the points returned for each trip. The default point fitness selection is `[GNSS, NETWORK]`.

```graphql
query VehicleTripHistory {
  tracking {
    deviceTimeline(
      deviceId: "019d48ea-0752-8000-801f-444556430b21"
      from: "2026-09-12T22:00:00Z"
      to: "2026-09-13T22:00:00Z"
      segmentation: {
        minStopDuration: "PT2M"
        stopRadius: 50
        movingSpeedThreshold: 3
      }
      options: { includePoints: true, maxPoints: 500 }
    ) {
      from
      to
      segmentation { minStopDuration stopRadius movingSpeedThreshold source }
      tracks {
        id
        startedAt
        endedAt
        duration
        distance
        pointCount
        simplified
        points { at point { lat lng } fitness }
      }
      stops {
        id
        startedAt
        endedAt
        duration
        point { lat lng }
      }
      totalDistance
      totalDrivingTime
      totalStopTime
      continueFrom
    }
  }
}
```

Response, with each trip's `points` shortened to its first and last point. The actual response can contain up to 500 points per trip:

```json
{
  "data": {
    "tracking": {
      "deviceTimeline": {
        "segmentation": {
          "minStopDuration": "PT2M",
          "stopRadius": 50.0,
          "movingSpeedThreshold": 3.0,
          "source": "REQUEST"
        },
        "tracks": [
          {
            "id": "0b6d4e8f1a29c375",
            "startedAt": "2026-09-13T04:58:12Z",
            "endedAt": "2026-09-13T05:47:20Z",
            "distance": 46.1,
            "duration": "PT49M8S",
            "pointCount": 590,
            "simplified": true,
            "points": [
              {
                "at": "2026-09-13T04:58:12Z",
                "point": {
                  "lat": 53.5201,
                  "lng": 10.0448
                },
                "fitness": "GNSS"
              },
              {
                "at": "2026-09-13T05:47:20Z",
                "point": {
                  "lat": 53.7012,
                  "lng": 10.3344
                },
                "fitness": "GNSS"
              }
            ]
          },
          {
            "id": "9e2f7a1c4d58b063",
            "startedAt": "2026-09-13T05:50:02Z",
            "endedAt": "2026-09-13T06:41:30Z",
            "distance": 38.5,
            "duration": "PT51M28S",
            "pointCount": 646,
            "simplified": true,
            "points": [
              {
                "at": "2026-09-13T05:50:02Z",
                "point": {
                  "lat": 53.7012,
                  "lng": 10.3344
                },
                "fitness": "GNSS"
              },
              {
                "at": "2026-09-13T06:41:30Z",
                "point": {
                  "lat": 53.8655,
                  "lng": 10.6866
                },
                "fitness": "GNSS"
              }
            ]
          },
          {
            "id": "3c8a5f0e7b12d946",
            "startedAt": "2026-09-13T07:12:05Z",
            "endedAt": "2026-09-13T08:03:47Z",
            "distance": 41.9,
            "duration": "PT51M42S",
            "pointCount": 620,
            "simplified": true,
            "points": [
              {
                "at": "2026-09-13T07:12:05Z",
                "point": {
                  "lat": 53.8655,
                  "lng": 10.6866
                },
                "fitness": "GNSS"
              },
              {
                "at": "2026-09-13T08:03:47Z",
                "point": {
                  "lat": 53.5201,
                  "lng": 10.0448
                },
                "fitness": "GNSS"
              }
            ]
          }
        ],
        "stops": [
          {
            "startedAt": "2026-09-12T22:00:00Z",
            "endedAt": "2026-09-13T04:58:12Z",
            "duration": "PT6H58M12S",
            "point": {
              "lat": 53.5201,
              "lng": 10.0448
            },
            "id": "2d90fe7a51c3b864"
          },
          {
            "startedAt": "2026-09-13T05:47:20Z",
            "endedAt": "2026-09-13T05:50:02Z",
            "duration": "PT2M42S",
            "point": {
              "lat": 53.7012,
              "lng": 10.3344
            },
            "id": "7ab3c5d1e90f2468"
          },
          {
            "startedAt": "2026-09-13T06:41:30Z",
            "endedAt": "2026-09-13T07:12:05Z",
            "duration": "PT30M35S",
            "point": {
              "lat": 53.8655,
              "lng": 10.6866
            },
            "id": "e5f60a2b3c7d9148"
          },
          {
            "startedAt": "2026-09-13T08:03:47Z",
            "endedAt": null,
            "duration": "PT13H56M13S",
            "point": {
              "lat": 53.5201,
              "lng": 10.0448
            },
            "id": "5b1e9c38a0f6d274"
          }
        ],
        "from": "2026-09-12T22:00:00Z",
        "to": "2026-09-13T22:00:00Z",
        "totalDistance": 126.5,
        "totalDrivingTime": "PT2H32M18S",
        "totalStopTime": "PT21H27M42S",
        "continueFrom": null
      }
    }
  }
}
```

`source: REQUEST` confirms that the requested two-minute policy applied. If the result reports `DEVICE`, the returned values are the ones that applied, not the requested settings.

The distance total is 126.5 km. Trip durations total 2 hours, 32 minutes, and 18 seconds. Stop durations total 21 hours, 27 minutes, and 42 seconds.

`maxPoints` limits each trip, not the entire response. For a long period, `includePoints: false` returns the trips and stops without their points. A narrower request produces a new result with its own segment IDs.
{% endstep %}

{% step %}

### Finish loading the selected period

Check `continueFrom` before treating the result as complete. The example returns null, so no further requests are needed. A non-null value means a server-side limit stopped the answer early, even if the original `to` remains in the response.

Repeat the timeline query with `from` set to `continueFrom`. Keep the original `to`, device, policy, and point options. Each part returns its own segments and totals. Repeat until `continueFrom` is null.

For example, if the first part returns `continueFrom: "2026-09-13T07:00:00Z"`, the next request uses these arguments:

```graphql
query ContinueVehicleTripHistory {
  tracking {
    deviceTimeline(
      deviceId: "019d48ea-0752-8000-801f-444556430b21"
      from: "2026-09-13T07:00:00Z"
      to: "2026-09-13T22:00:00Z"
      segmentation: {
        minStopDuration: "PT2M"
        stopRadius: 50
        movingSpeedThreshold: 3
      }
      options: { includePoints: true, maxPoints: 500 }
    ) {
      from
      to
      segmentation { minStopDuration stopRadius movingSpeedThreshold source }
      tracks {
        id startedAt endedAt duration distance pointCount simplified
        points { at point { lat lng } fitness }
      }
      stops { id startedAt endedAt duration point { lat lng } }
      totalDistance
      totalDrivingTime
      totalStopTime
      continueFrom
    }
  }
}
```

The response has the same shape as the first query, with segments and totals for the remaining period or another partial answer. Check its `continueFrom` again.

A part covers a slice of the period, and the next part starts where the previous one ended. The parts don't overlap, so you can add their distances and durations to get the totals for the whole period.

The cut between two parts is not a real event. A trip or a stop that was in progress at the cut appears twice: its first piece at the end of one part and its second piece at the start of the next, with two different IDs. The vehicle didn't stop or depart there.

If a request for a part fails, send the same request again. Add its totals only after it succeeds, so that a retry doesn't count them twice.

Always take the next `from` value from `continueFrom`. Don't compute it from the end of the last trip: a part can contain only stops, so the last trip may not be where the part ended.

{% endstep %}

{% step %}

### Read the trips and stops as one timeline

`tracks` and `stops` are two lists over the same period. Sorted together by `startedAt`, the example response reads the following way in Hamburg local time:

| Kind | Start    | End                   | Duration         | Distance |
| ---- | -------- | --------------------- | ---------------- | -------- |
| Stop | 00:00:00 | 06:58:12              | 6 h 58 min 12 s  |          |
| Trip | 06:58:12 | 07:47:20              | 49 min 8 s       | 46.1 km  |
| Stop | 07:47:20 | 07:50:02              | 2 min 42 s       |          |
| Trip | 07:50:02 | 08:41:30              | 51 min 28 s      | 38.5 km  |
| Stop | 08:41:30 | 09:12:05              | 30 min 35 s      |          |
| Trip | 09:12:05 | 10:03:47              | 51 min 42 s      | 41.9 km  |
| Stop | 10:03:47 | Ongoing at period end | 13 h 56 min 13 s |          |

Only trips have a distance. A stop with `endedAt: null` was still going at the end of the covered period, or at a continuation boundary. Its `duration` is capped at that end, so it isn't the time since the stop began, and the stop isn't necessarily still going now.

`totalDrivingTime` is the sum of trip durations, including pauses too short to qualify as stops. It isn't the time spent physically moving.

Trips and stops don't have to cover the whole period. A gap between segments means that the device sent no messages there, not that it was stopped. A complete response doesn't prove that the device reported throughout the period.
{% endstep %}

{% step %}

### Read the route of a trip and the point of a stop

A trip's `points` are in time order. A stop has one representative `point`, the centroid of the points observed during the stop.

`simplified: true` means that the route was thinned to meet `maxPoints`. `distance` and `duration` are calculated from all points, so a distance recalculated from the simplified line is shorter than the API's value.

The example keeps the default `[GNSS, NETWORK]` fitness selection. To receive repeated last-known positions as well, add `LAST_KNOWN` to `options.fitness`. Those points never contribute to the API's distance sum. Changing point options produces a new result with its own segment IDs.

The API returns coordinates, not street addresses.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You now have the vehicle, its device, the period, and the stop policy that applied, every trip and stop of the period with their totals, the route of each trip, and a marker for an incomplete result.
{% endhint %}

## Reading the timelines of an asset

`assetTimeline(assetId:, from:, to:)` takes the asset instead of a device and returns a list with one timeline per device linked to the asset. Tracking doesn't merge them: two devices on the same vehicle can describe the same movement, so their distances can't be added as vehicle mileage. Each entry names its `device` and has the same shape as the device timeline.

This query reads the same day for the truck, without points:

```graphql
query TruckTimelines {
  tracking {
    assetTimeline(
      assetId: "019d48ea-0752-8000-801f-415353540a11"
      from: "2026-09-12T22:00:00Z"
      to: "2026-09-13T22:00:00Z"
      segmentation: { minStopDuration: "PT2M", stopRadius: 50, movingSpeedThreshold: 3 }
    ) {
      device { id title }
      segmentation { source }
      totalDistance
      totalDrivingTime
      totalStopTime
      continueFrom
    }
  }
}
```

Response, shortened to the totals. `tracks` and `stops` are available on each entry with the same fields as in the device timeline:

```json
{
  "data": {
    "tracking": {
      "assetTimeline": [
        {
          "device": { "id": "019d48ea-0752-8000-801f-444556430b21", "title": "Teltonika FMC130 #8621" },
          "segmentation": { "source": "REQUEST" },
          "totalDistance": 126.5,
          "totalDrivingTime": "PT2H32M18S",
          "totalStopTime": "PT21H27M42S",
          "continueFrom": null
        }
      ]
    }
  }
}
```

The truck has one device, so the list has one entry, and its totals match the device timeline above. An asset with two devices returns two entries, each with the totals of its own device. The primary device is the default source for the current assignment. Which device represented the vehicle in the past is a separate question, as [Prerequisites](#prerequisites) explains.

Both timeline queries answer for one asset or one device. There is no fleet-level total: a report over many vehicles is one request per device, and every request recalculates its period from the device messages.

### Events and readings on points

`options.eventCodes` requests selected events, and `options.readingCodes` includes readings on route points. Events default to none, while an explicit null requests all event codes. [Trips and stops](../api-reference/trips.md#trackingtrackoptions) documents both options.

An event can have no coordinates, so not every event has a place on the route. A reading on a point comes from the message that produced that point, and a simplified route omits points, so the route isn't a complete sensor history. Tracking has no query for raw messages or positions: points arrive only with the trips of a timeline.

## Error handling

Four different outcomes look alike if you read only the totals:

| Outcome                 | What it means                                                                                                                             |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| No primary device       | There is no default device to query. It doesn't mean that the vehicle has zero mileage.                                                   |
| `deviceTimeline: null`  | No timeline exists for the selected device. Null isn't zero totals.                                                                       |
| Empty `tracks`          | The device didn't move under the policy. Stops can still be present. It doesn't establish that the device reported throughout the period. |
| Empty trips and stops   | No segments were found for the period and policy.                                                                                         |
| Non-null `continueFrom` | The response covers part of the period. Its totals are partial.                                                                           |

See [Error handling](../../error-handling.md) for the shared response format. Invalid duration syntax is an input error: `minStopDuration: "P1M"` uses a calendar month, which the `Duration` scalar doesn't accept.

### How to handle an invalid stop duration

Use a fixed-length duration such as `PT2M` or `PT1H`. Calendar date ranges belong in `from` and `to`, rather than in a stop-duration parameter.

## See also

- [Trips and stops](../api-reference/trips.md): Timeline queries, segmentation, and point options
- [Building a live fleet map](building-a-live-fleet-map.md): Current asset positions and state subscriptions
- [Creating assets and assigning devices](../../business-data-repository/guides/creating-assets-and-assigning-devices.md): Asset records and device assignment
- [Creating geofences, POIs, and routes](../../business-data-repository/guides/creating-geofences-pois-and-routes.md#check-test-point-containment): Test whether a stop's `point` is inside a customer site with `containsPoints`
- [Common resources](../../core-api-reference/common.md#duration): Duration syntax
