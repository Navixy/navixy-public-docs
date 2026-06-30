---
title: Why isn't my model connecting to standalone?
description: "Device model won't connect: verify the required port is open using nc. Check the device list page for the model-specific port number"
---

# Why isn't my model connecting to standalone?

## Question

Why isn't my specific device model connecting, while other models work?

## Answer

If you have a specific device model that won't connect to your platform (for example, Teltonika), while other models work, the most likely cause is a closed port.

Example: Teltonika typically uses port **47776**.

Verify from an external host:

`nc -zv your_domain.com 47776`

Open the required port, then retest.

For a full list of ports, search for the device model on the device list page.

## Links

* [Device list (ports)](https://www.navixy.com/devices/)
* [Network](https://app.gitbook.com/s/kUnMmePH99SsdChtqqu7/on-premise/how-to-guide/requirements/network)
* [Device activation network](https://app.gitbook.com/s/kUnMmePH99SsdChtqqu7/on-premise/how-to-guide/troubleshooting/device-activation#network)
