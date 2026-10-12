---
title: Why GPS devices stop transmitting
description: Navixy sees only that messages stopped, not why. The battery, power, and signal values that the device sent before it went silent point to the cause
tags:
  - troubleshooting
noIndex: true
---

# Why GPS devices stop transmitting

## Question

How can I determine why my GPS device stopped transmitting data?

## Answer

Navixy can tell that a device stopped sending messages, but not why. [Tracking](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/tracking) shows a device as offline after 10 minutes without any message. The Live status dashboard uses its own periods, described in [Connection status](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/dashboards/live-status#connection-status).

The usual causes are on the side of the device or the mobile network:

* **Device and power:** a discharged battery, cut or disconnected power, a device failure, or faulty wiring.
* **Network:** a fault in the mobile network, poor coverage, no money on the SIM card, an outage of the carrier, or GSM jamming.

To narrow down the cause, look at what the device sent before it went silent:

1. Generate a [Sensor readings](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/reports/sensor-readings) report for the days before the device stopped. Select the battery, voltage, and signal values that the device sends.
2. Check how the values changed before the last message. A falling battery voltage or board voltage points to a power problem. A weak signal points to poor coverage.

For a device that didn't connect after activation, the device page in Device Management lists more causes, such as sleep mode, a SIM card PIN, or roaming settings. See [Activate a device](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/device-management/activate-device).
