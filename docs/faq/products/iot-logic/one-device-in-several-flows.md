---
title: One device in several IoT Logic flows
description: "A device can belong to several IoT Logic flows, and their results are combined into one message."
tags:
  - fundamentals
---

# One device in several IoT Logic flows

## Question

Can a device belong to more than one IoT Logic flow?

## Answer

Yes. Each flow processes the device's messages, and IoT Logic combines their results into the one message that it sends on.

Two rules apply:

* Two flows that cover the same device can't produce an attribute with the same name. The second flow doesn't save.
* A flow can't read the attributes that another flow calculates. Each flow sees the device's own readings and the results of that flow.

See [Data Source](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/data-source).
