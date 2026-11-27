---
description: >-
  Read the track of a trip with the API of Reports: the positions and speeds that
  the GPS device sent from the start to the end of the trip.
tags:
  - fundamentals
  - beta
---

# Trip tracks

{% hint style="info" %}
**API, with a session token**

The operations on this page are API operations. They accept the session token of a person signed in to Navixy Reports in a browser. The role must let you run `driver_performance`, and see the asset and the GPS device of the trip. See [API access](../authentication.md#api-access).
{% endhint %}

A trip track is the list of positions that a GPS device sent during one trip, with the time and the speed of each position. Use it to draw a trip on a map or a speed chart. The operation is `GET /api/reports/{id}/trips/{tripId}/track`. See [Read trip track](api-reference.md) in the API reference.

## How to request a trip track

Name `driver_performance` in the path, and take the trip ID from the `trips` of a `driver_performance` run. See [Report runs](report-runs.md). A trip of a `trips_and_stops` run is the same trip, so its ID works too. `trips_and_stops` and `sensor_readings` in the path return HTTP 501 with `not_implemented`.

The following request reads the track of one trip:

```bash
curl --request GET \
  --url 'https://reports.<REGION>.navixy.com/api/reports/driver_performance/trips/5f1c2a9e7b3d4e6f8a0b1c2d3e4f5a6b/track' \
  --header "Authorization: Bearer $SESSION_TOKEN"
```

Response, shortened to the first 3 points and the last point:

```json
{
  "points": [
    { "at": "2026-11-26T01:25:35.397Z", "lat": 44.822908, "lon": 20.45738, "speedKmh": 40 },
    { "at": "2026-11-26T01:25:45.413Z", "lat": 44.823484, "lon": 20.456292, "speedKmh": 37 },
    { "at": "2026-11-26T01:25:54.434Z", "lat": 44.823923, "lon": 20.455534, "speedKmh": 26 },
    { "at": "2026-11-26T02:09:29.386Z", "lat": 44.799256, "lon": 20.476289, "speedKmh": 20 }
  ]
}
```

The times are in UTC. `speedKmh` is `null` when the device sent no speed.

## Which positions a track includes

A track has the positions from the start to the end of the trip, both included, in time order. It leaves out these positions:

* A position that can't be drawn on a map: no coordinates, coordinates out of range, or 0,0.
* A position without a valid fix, and a position with a speed over 200 km/h. Trip detection leaves out the same positions.
* A repeated position. When the device sent the same position several times, the track has it once.

Navixy keeps the positions of a trip for 3 months, so the track of an older trip has an empty `points` list. Positions that the device sent more than 10 minutes late aren't kept either. So a track can have gaps, and a trip that the device sent wholly late has an empty `points` list. The track is never shortened, however many points it has.

Some trip IDs return HTTP 404 with `trip_not_found`. They are IDs that the workspace doesn't have, stop IDs, and trips whose asset or GPS device you can't see. See [Reports errors](errors.md#api-errors).
