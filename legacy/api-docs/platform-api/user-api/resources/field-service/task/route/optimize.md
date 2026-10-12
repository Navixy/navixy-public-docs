---
title: Route optimization
description: Reorder a set of points so the total travel time between them is as small as possible.
---

# Route optimization

This operation reorders a set of points so that the total travel time between them is as small as possible, which cuts transit time and cost on a multi-stop round.

It works on raw points rather than on a stored route, so there is no need to create a route and its checkpoints first. Send the points, and the response says what order to visit them in.

## API actions

API base path: `/task/route/points/optimize`.

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/route/points/optimize" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 7 - Invalid parameters.
* 210 - Path distance exceeds the max distance limit - if the overal route distance is more than 5000 km.
* 264 - Timeout not reached - too high api call rate.

### How the result is read

```json
{
  "success": true,
  "result": [2, 0, 1] 
}
```

The `result` will return the order in which the points should be visited.

If for route points:

```
[
   {route_point_0}, // index in list = 0
   {route_point_1}, // index in list = 1
   {route_point_2}  // index in list = 2
]
```

this action returns: `[2, 0, 1]`

it means "change points order as following":

```
point at index 2 move to index 0,
point at index 0 move to index 1,
point at index 1 move to index 0
```

or with a more tangible example with 5 points. You have the next points to be reordered

```json
[
{"location": {"lat": 38.81673961922754,"lng": -77.15569496154785}, "from": "2024-03-19 00:00:00", "to": "2024-03-19 23:59:00"}, // it has index 0
{"location": {"lat": 38.82767290746902,"lng": -77.1445369720459}, "from": "2024-03-19 00:00:00", "to": "2024-03-19 23:59:00"}, // it has index 1
{"location": {"lat": 38.834760258479704,"lng": -77.14093208312988}, "from": "2024-03-19 00:00:00", "to": "2024-03-19 23:59:00"}, // this one with index 2
{"location": {"lat": 38.81583679562883,"lng": -77.14814186096191}, "from": "2024-03-19 00:00:00", "to": "2024-03-19 23:59:00"}, // this with index 3
{"location": {"lat": 38.81031929163279,"lng":7.15582370758057}, "from": "2024-03-19 00:00:00", "to": "2024-03-19 23:59:00"} // and this one has index 4
]
```

Sending those five points with a start point of `"lat": 38.81476676765485, "lng": -77.1608018875122`, the Navixy platform will reply to you with:

```json
{
  "result": [4,0,3,1,2],
  "success": true
}
```

So the optimized route with start point from "lat": 38.81476676765485, "lng": -77.1608018875122 should be:

```json
[
{"location": {"lat": 38.81031929163279,"lng":7.15582370758057}, "from": "2024-03-19 00:00:00", "to": "2024-03-19 23:59:00"}, // this one had index 4, now it is the first point to visit
{"location": {"lat": 38.81673961922754,"lng": -77.15569496154785}, "from": "2024-03-19 00:00:00", "to": "2024-03-19 23:59:00"}, // it had index 0, now it is the second point to visit
{"location": {"lat": 38.81583679562883,"lng": -77.14814186096191}, "from": "2024-03-19 00:00:00", "to": "2024-03-19 23:59:00"}, // this with index 3 becomes the third point to visit
{"location": {"lat": 38.82767290746902,"lng": -77.1445369720459}, "from": "2024-03-19 00:00:00", "to": "2024-03-19 23:59:00"}, // it had index 1, now it is the fourth point to visit
{"location": {"lat": 38.834760258479704,"lng": -77.14093208312988}, "from": "2024-03-19 00:00:00", "to": "2024-03-19 23:59:00"} // and this one with index 2, now it is the last fifth point to visit
]
```
