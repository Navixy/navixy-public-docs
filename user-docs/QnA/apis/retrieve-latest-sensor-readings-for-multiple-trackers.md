---
description: Call batch_list with a list of tracker IDs to retrieve latest sensor readings. Get all trackers first, then filter for those with data
---

# Retrieve latest sensor readings for multiple trackers

## Question

Is it possible to get a list of tracker IDs that have sensor data?

## Answer

You can retrieve latest readings using:

`tracker/sensor/readings/batch_list`

Note: this method works with a **list of tracker IDs that you provide** — it doesn't automatically return "all trackers that have sensor data".

To build a list of trackers that have readings:

1. Get the list of all trackers in the account.
2. Call `batch_list` for that list.
3. Filter out trackers with no readings.

## Links

* [`tracker/sensor/readings/batch_list`](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/tracking/tracker/readings#batch_list)
* [Sensor data guide](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/guides/data-retrieval/sensor-data)
