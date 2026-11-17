---
title: 'Online, Offline and GPS not updated statuses'
description: "Online means valid coordinates arrive, GPS not updated means none for 5 minutes, and Offline means no data for the connection timeout."
---

# Online, Offline and GPS not updated statuses

## Question

What do the Online, Offline and GPS not updated statuses mean?

## Answer

The status shows how recent the device's data is. It doesn't show whether the device is broken.

**Online:** the device is connected and sends valid coordinates.

**GPS not updated:** the device is connected, but no new valid coordinates have arrived for 5 minutes. Common causes are sleep mode, a weak GPS signal, old records that the device uploads from its memory, and a device clock that isn't set to UTC.

**Offline:** no data has arrived during the connection timeout. Common causes are a weak cellular signal, deep sleep, a SIM card or roaming problem, and a wrong server address.

To control when a device becomes **Offline**, set the timeout in the device's **Connection state** settings. Without your value, the platform uses a built-in timeout that depends on the device and the connection type.

## Links

* [Connection state](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/tracking/objects-list/connection-state)
* [Accuracy and freshness of tracker data](accuracy-and-freshness-of-tracker-data.md)
