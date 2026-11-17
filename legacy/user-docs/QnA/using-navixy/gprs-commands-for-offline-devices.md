---
title: GPRS commands for offline devices
description: "A GPRS command reaches the device only while it is connected, and the platform sends a command for an offline device after it reconnects."
---

# GPRS commands for offline devices

## Question

What happens to a GPRS command that I send while the device is offline?

## Answer

The command waits and the platform sends it when the device reconnects. A GPRS command reaches the device only while the device is connected to the platform over mobile data. An SMS command uses a different channel and waits until the device confirms it.

If the device never reconnects, for example because it has a wrong server address, no mobile data or a SIM card problem, it doesn't receive the command. Before you send the command again, check the device's connection status and the command's status in the **Commands** block or the Air Console.

## Links

* [Connection state](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/tracking/objects-list/connection-state)
