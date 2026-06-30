---
description: Configure the ignition source for GPS devices that use virtual ignition detection via motion sensor or voltage instead of a direct ignition cable connection.
---

# Ignition source

This page covers creating an ignition sensor on the Navixy platform for devices that use virtual ignition: Derived from the motion sensor or on-board voltage, instead of a direct ignition-cable connection.

{% hint style="info" %}
This is the **platform-side sensor**. To choose *how the device itself* detects ignition (digital input, on-board voltage, or motion), use the [Ignition source](../../location-and-movement/ignition-source.md) under Location and movement.
{% endhint %}

## How virtual ignition works

Some devices do not allow connecting the ignition cable to the device input to transmit the ignition status. Instead, they use virtual ignition based on the motion sensor readings or the vehicle's onboard voltage.

When the engine is running, the alternator (generator) charges the battery and raises the vehicle's electrical voltage above its resting level, typically above 13.2 V. The device detects this voltage increase and uses it to determine that the engine is on.

The ignition detection by the movement sensor is convenient if the device is not connected to the vehicle's onboard electrical system. However, when the vehicle is towed, the ignition is also detected as the vehicle moves, although the engine doesn't start.

## Settings

The ignition source is selected from a drop-down list. If the ignition source is the onboard power supply voltage, the range of values in which the ignition is considered switched on must be selected.

## Availability

Available on devices that expose a suitable input or support virtual ignition.

## See also

* [Ignition source](../../location-and-movement/ignition-source.md): set the device-side ignition detection mode.
* [Engine hours](../../location-and-movement/engine-hours.md): can use an ignition sensor as its data source.
