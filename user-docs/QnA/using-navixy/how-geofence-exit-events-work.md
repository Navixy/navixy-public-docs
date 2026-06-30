---
title: How geofence exit events work
description: "Platform checks location updates against geofences in real time; a 60-second timer prevents duplicate entry and exit events"
---

# How geofence exit events work

## Question

How do geofence exit events work?

## Answer

Each geofence has its own set of coordinates defining its boundaries. Devices regularly send their current location to the platform. If a device's coordinates move **outside** a geofence and there is an active rule for geofence entry/exit for that geofence, the platform generates an event.

In other words, the platform continuously checks incoming location updates against all geofences and their corresponding rules in real time.

To avoid duplicate events, there's a timer in place: once an entry or exit event is triggered, the platform doesn't register the same type of event again for the next 60 seconds.

Here's a simple example to show how this works:

* 00:00:00 — The device enters the geofence → entry event is triggered.
* 00:00:15 — The device exits the geofence → exit event is triggered.
* 00:00:30 — The device enters again → no event triggered (less than 60 seconds since the previous entry event).
* 00:01:20 — The device exits again → exit event is triggered (more than 60 seconds since the previous exit event).

This mechanism helps filter out noise from devices moving near geofence borders and prevents flooding the system with repeated events.

## Links

* [Geofence entrance/exit: system details](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/events-and-notifications/movement-monitoring/geofence-entrance-or-exit)
