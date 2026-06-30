---
title: A device is showing as moving on the platform even though it is offline
description: Device continues showing its last known status until it comes back online and sends updated location information to the platform
---

# A device is showing as moving on the platform even though it is offline

## Question

Why does my device appear to be moving on the platform when it's offline?

![](<../.gitbook/assets/Unknown image>)

## Answer

When your device goes offline, the platform continues to show its last known status. If your device was moving when it sent its last update, it keeps showing as "moving" until it comes back online and sends new information.

Think of it like taking a photo - the platform shows you that last "snapshot" of your device's status until it can take a new one. The platform tracks two main things:

* Whether your device is connected (online/offline)
* Whether your device is moving or stationary

For example, if your device last reported that it was moving at 00:08 AM on November 21, 2024, and then went offline, the platform will continue showing it as "moving" since that was its last known state. This happens even though the device itself might have stopped moving after going offline.

## Links

[Connection status](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/tracking/objects-list/connection-state)

[Parking detection widget](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/location-and-movement/parking-detection-block)
