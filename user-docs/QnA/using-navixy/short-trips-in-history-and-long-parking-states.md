---
title: Short trips in history and long parking states
description: Disable Consider ignition state in Parking Detection to prevent inconsistent ignition signal from fragmenting trips into short segments
---

# Short trips in history and long parking states

## Question

Why does trip history show very short trips along with long parking durations?

![](<../.gitbook/assets/Unknown image (22)>)

## Answer

Most likely, the device has **Consider ignition state** enabled in the **Parking detection** block.

Your tracker might be sending inconsistent and intermittent ignition state data. Once the platform detects ignition OFF state, the trip state is changed to **Parked**, bypassing the **Min idle detection** logic:

* The trip starts if the speed is greater than or equal to min\_speed and the ignition is on.
* The trip ends if the speed drops below min\_speed and either the elapsed time exceeds min\_parking or the ignition is off.

Since it is enough for the device to send OFF ignition state for a trip end, many trips in the trip history are short and last no longer than a couple of seconds. The duration of such trips is determined by the duration of the ignition ON state.

To make sure that the ignition is the root cause, you can prepare a raw data spreadsheet with the discrete\_inputs columns selected:

![](<../.gitbook/assets/Unknown image (24)>)

To resolve the situation, you can either fix ignition detection from the device side so it detects ignition consistently without unexpected drops, or switch off the **Consider ignition state** option in the **Parking detection** block. The old track durations will not change after the fix.

## Links

* [Parking detection](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/location-and-movement/parking-detection-block)
* [Raw data](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/object-management/raw-data-block)
