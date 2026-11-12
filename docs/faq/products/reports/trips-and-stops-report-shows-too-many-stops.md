---
title: Trips and stops report shows too many stops
description: "Each standstill of 5 minutes or longer is a stop, and a parked vehicle's moving position, data gaps, or a second GPS device add more."
tags:
  - troubleshooting
---

# Trips and stops report shows too many stops

## Question

Why does my history show too many stops?

## Answer

Navixy records a stop every time an asset stands still for 5 minutes or longer. The asset stands still when the speed reported by its GPS device is 3 km/h or less. Each traffic jam, queue at a gate, or wait at a loading dock adds a stop and splits the drive into two trips. These limits are the same for every workspace, and you can't change them.

Some stops in the report aren't separate standstills. Check the following:

* **Several stops while the vehicle was parked.** A stop ends when the GPS device reports a speed above 3 km/h. While a vehicle is parked, the position reported by its GPS device can move a little, and the device then reports such a speed. This movement is too short to count as a trip, so the report shows one parked period as two stops. To prevent the extra stops, configure the GPS device to report a speed of 0 while the vehicle is parked. On Teltonika devices, turn on [static navigation](../../devices/teltonika/static-navigation-on-teltonika-devices.md).
* **The GPS device sent no data for more than 15 minutes.** A stop ends at such a gap. When the vehicle is still parked, the next data from the GPS device starts a new stop.
* **The asset has two GPS devices.** The report shows each stop once for each GPS device. The **Device** column in the **Trip and stop log** shows which GPS device recorded each line.

See [Trips and stops](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/reports/trips-and-stops#how-a-stop-is-detected) in Reports to learn how Navixy detects trips and stops.
