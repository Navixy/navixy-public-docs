---
title: Speed does not match with device status in the point report
description: Status in the point report reflects trip state based on parking detection settings, not the instantaneous GPS speed at that moment
---

# Speed does not match with device status in the point report

## Question

Why does speed not match the device's status in the points report?

## Answer

It's quite common to think that the speed is directly related to the status in the points report, but it also takes into account the parking detection settings.

This means that if the car is on a trip and there is a red light on the road, the vehicle will stop, showing 0 speed, but the status may still be **moving** due to the fact that the vehicle is still on a trip.

On the other hand, if the vehicle is parked and there is GPS drift, but the ignition is set to be considered in the parking state, then it will remain parked even though it might show speed.

## Links

* [Types of reports](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/reports/report-types)
* [Parking detection](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/location-and-movement/parking-detection-block)
