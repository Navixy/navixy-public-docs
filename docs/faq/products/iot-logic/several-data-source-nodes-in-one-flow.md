---
title: Several Data Source nodes in one IoT Logic flow
description: "One IoT Logic flow can hold several Data Source nodes, each with its own devices and its own path."
tags:
  - fundamentals
---

# Several Data Source nodes in one IoT Logic flow

## Question

Can one IoT Logic flow have several Data Source nodes?

## Answer

Yes. Each **Data Source** node has its own devices and its own onward path, so one flow can serve several groups of devices that need different handling.

If two **Data Source** nodes share a device, the paths of the two nodes must not meet.

See [Data Source](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/data-source).
