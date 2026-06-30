---
title: LBS Signals and false geofence events
description: Reduce LBS-triggered geofence false positives by increasing the detection radius in device settings; LBS cell tower accuracy is typically 1–2 km
---

# LBS Signals and false geofence events

## Question

Are LBS signals taken into account when generating geofence entry and exit events, and can they cause false positives in geofence reports?

## Answer

Yes. LBS-based positions can be used by the platform, and they can cause false positives in geofence reporting.

This usually happens when:

* the tracker cannot provide reliable GPS/GNSS (indoors, weak signal, etc.)
* the tracker sends an LBS position with a large radius
* the LBS radius overlaps a geofence boundary

If the radius overlaps the geofence, the platform can interpret it as an exit and later an entry.

### How to reduce false positives

Check the device's GSM/LBS data quality first.

Then adjust **LBS detection radius** in device settings so the LBS radius remains within the expected area.

## Links

* [LBS detection radius](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/location-and-movement/lbs-detection-radius-block)
