---
description: Determine parking locations by calling track/list and computing gaps between trip times or analyzing daily end locations
---

# Identify a usual / longest parking location via API

## Question

Can we identify which place is the longest parking location for each device through API?

## Answer

There is no dedicated endpoint for "usual parking location".

However, you can determine it using trip data from the API.

Approach 1:

* Call `track/list` for a period (for example, a month).
* Compute gaps between one trip's `end_time` and the next trip's `start_time`.
* Use the previous trip end location as the parking point.
* The location with the longest gap is a likely main parking spot.

Approach 2:

* Take the last trip end location each day.
* The most frequent location is likely the usual parking spot.

## Linksss

* [`track/list`](https://www.navixy.com/docs/navixy-api/user-api/backend-api/resources/tracking/track/index#list)
