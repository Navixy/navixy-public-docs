---
title: Output Endpoint nodes in command-only flows
description: "A flow that only sends commands needs an Output Endpoint node for its calculated values to reach the platform."
tags:
  - fundamentals
---

# Output Endpoint nodes in command-only flows

## Question

Do I need an Output Endpoint node if my IoT Logic flow only sends commands?

## Answer

Yes, if the values that the flow calculates should reach the Navixy platform. A path that ends at a **Device Action** node keeps nothing that the flow calculated, though the device's own readings still arrive. Connect an **Output Endpoint** node to the same branch as the **Device Action** node.

A flow without an **Output Endpoint** node still saves, and its commands are sent.

See [Output Endpoint](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/output-endpoint) and [Device Action](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/device-action#where-it-sits-in-a-flow).
