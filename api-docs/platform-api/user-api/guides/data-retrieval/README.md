---
title: Data retrieval
description: Worked examples of getting trackers, tracks, sensor readings, and reports out of the Navixy platform.
---

# Data retrieval

Reading data out of the platform almost always takes more than one call: you find the tracker, then ask what it did, then ask for the detail. These guides walk those sequences end to end, with the calls in order and the reasoning for each parameter.

* [Getting list of GPS trackers](get-tracker-list.md): the first call most integrations make, and the one every other guide starts from.
* [Getting track points](get-track-points.md): from a tracker to its trips, and from a trip to the raw points behind it.
* [Retrieving sensor and counter data](sensor-data.md): reading what a device measured alongside its location.
* [Obtaining report information](obtain-reports.md): generating a report and collecting the result, which is asynchronous.
