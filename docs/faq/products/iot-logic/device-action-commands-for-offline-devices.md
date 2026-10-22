---
title: Device Action commands for offline devices
description: "A Device Action command for an offline device waits in a queue: up to 3 days for Switch output, up to 1 hour for a command string."
tags:
  - reference
---

# Device Action commands for offline devices

## Question

What happens to a Device Action command if the device is offline?

## Answer

The command waits in the Navixy platform's command queue, and the platform sends it when the device next connects.

How long a command waits depends on its type:

* A **Switch output** command waits up to 3 days.
* A command string sent with **Send GPRS command** waits up to 1 hour.

A command that is still waiting after this time is dropped and never reaches the device. Up to 100 commands can wait for one device, and a newer command is then lost. A command that is the same as one still waiting isn't added again. For anything time-critical, confirm that the device is online.

See [Device Action](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/device-action).
