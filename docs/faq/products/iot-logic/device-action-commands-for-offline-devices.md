---
hidden: true
noIndex: true
title: Device Action commands for offline devices
description: "A Device Action command for an offline device waits in a queue: up to 3 days for Switch output, up to 1 hour for a command string."
tags:
  - reference
---

# Device Action commands for offline devices

## Question

What happens to a Device Action command if the device is offline?

## Answer

The command waits in a queue. IoT Logic sends it when the device next connects and sends data.

How long a command waits depends on its type:

* A **Switch output** command waits up to 3 days.
* A command string sent with **Send GPRS command** waits up to 1 hour.

A command that is still waiting after this time is dropped and never reaches the device. For anything time-critical, confirm that the device is online.

See [Device Action](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/device-action).
