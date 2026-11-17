---
title: Trips report shows No driver key
description: "The Trips report assigns a trip to a driver only when the device sends the driver's hardware key at the start of the trip."
---

# Trips report shows No driver key

## Question

Why does the Trips report show "No driver key" when the driver is already identified by RFID or iButton?

## Answer

The **Trips** report takes the driver from the hardware key that the device sends at the start of each trip. When the first records of a trip have no key, the report puts the trip under **No driver key**, even if the object widget in **Tracking** shows the driver.

This happens most often after sleep mode or a power cut. It also happens when the driver scans the key briefly and the key clears before the vehicle starts moving.

To find the cause, open **Raw data** for the start of the trip and check that the first records have the key. Then check that the key matches the hardware key in the driver's record in **Drivers**. Also check that the device is configured to keep sending RFID, iButton or BLE identification.

## Links

* [Drivers](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/fleet-management/drivers)
* [Set up driver identification](set-up-driver-identification.md)
