---
title: Device shows "GPS not updated" while it reports every 5 minutes
description: GPS not updated status appears when a device sends heartbeats without coordinates or when a time zone mismatch exists on the platform
---

# Device shows "GPS not updated" while it reports every 5 minutes

## Question

Why is my device showing "GPS not updated" even though it's reporting every 5 minutes?

![](<../.gitbook/assets/Unknown image (5)>)

## Answer

When a device goes into the "GPS not updated status," it generally means that the device hasn't sent the platform a recent data packet with new, valid coordinates. This can be due to a number of things, including:

* The device is only sending a heartbeat (a keep-alive signal), meaning no coordinate data is being sent. Please look into the device settings and verify it's set to send coordinates even when not moving.
* If the time doesn't currently match real-time, such as if the device is sending data from its buffer or the device isn't in UTC+0, the device will also display as "GPS not updated." Simply wait for the buffer to clear or adjust the timezone to get the device back online.

## Links

* [Device statuses](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/gps-devices/add-and-manage-devices/device-connection-status)
