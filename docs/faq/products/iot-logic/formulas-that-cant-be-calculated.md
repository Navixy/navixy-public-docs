---
title: IoT Logic formulas that can't be calculated
description: "When a formula can't be calculated, IoT Logic skips that attribute for the message and the rest of the flow continues."
tags:
  - troubleshooting
---

# IoT Logic formulas that can't be calculated

## Question

What happens if an IoT Logic formula can't be calculated?

## Answer

IoT Logic skips the attribute of that formula for the message that it is processing. The rest of the flow continues, and the message still reaches the Navixy platform.

The usual cause is a formula that reads an attribute that the device didn't send in this message. See [Missing values](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/expressions/missing-values) and [Initiate Attribute](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/initiate-attribute).
