---
title: Calculated values and original readings
description: "A value that an IoT Logic flow calculates is added to the message, next to the reading it came from."
tags:
  - fundamentals
---

# Calculated values and original readings

## Question

Does a calculated value replace the reading it came from?

## Answer

No. The message keeps both. An IoT Logic flow adds to the device's data and doesn't remove from it.

The one exception is a device that starts sending a reading with the same name as an attribute that the flow already calculates. The calculated value then replaces that reading, and the flow can't be saved again until the name changes.

See [Initiate Attribute](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/initiate-attribute).
