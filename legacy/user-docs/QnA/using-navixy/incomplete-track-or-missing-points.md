---
title: Incomplete track or missing points
description: "Sparse tracks usually come from missing GPS points, filtered invalid coordinates, or parking and trip settings."
---

# Incomplete track or missing points

## Question

Why is the trip track incomplete or missing points even though the device was reporting?

## Answer

The map draws from stored valid GPS points. Gaps appear when the device didn't send coordinates, lost GPS, or sent invalid coordinates, even if other telemetry arrived.

The platform also discards points with invalid coordinates, for example when the device sees too few satellites. Other data from the same moment, such as input states, still arrives. That is why **Raw data** can show records for a time when the track has a gap.

Also check the **Parking detection** and trip settings, and compare the same interval in **Raw data**. For the full checklist, see [Troubleshooting trip history issues](troubleshooting-trip-history-issues.md).

## Links

* [Troubleshooting trip history issues](troubleshooting-trip-history-issues.md)
* [Raw data](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/object-management/raw-data)
* [Parking detection](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/location-and-movement/parking-detection)
