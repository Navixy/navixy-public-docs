---
title: My vehicle didn't stop, yet the trip is split
description: Review Parking Detection settings and inspect raw data to find the cause; disable Split by stops in History to view the full track
---

# My vehicle didn't stop, yet the trip is split

## Question

Why are my device's trips stopping and starting randomly?

## Answer

The most likely cause for a trip to seemingly end and start later, farther down the road where it can't park is most likely due to Parking Detection. Please review your Parking Detection settings and compare against the data within the Raw Data Export tool. The device most likely was moving at a speed lower than the Parking Detection limit.

Other possibilities include device issues due to GPS signal or the device itself not providing speed information. The Raw Data Export tool will show this specifically.

A possible way around this is to uncheck the **Split by stops** option on the object widget, which shows the path as a single track.

## Links

* [Parking detection](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/events-and-notifications/movement-monitoring/parking-state-detection)
* [Raw data export](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/object-management/raw-data-block)
