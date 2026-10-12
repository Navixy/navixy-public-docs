---
title: Same attribute name in two IoT Logic flows
description: "Two IoT Logic flows can calculate the same attribute name only if they share no devices."
tags:
  - troubleshooting
---

# Same attribute name in two IoT Logic flows

## Question

Can two IoT Logic flows calculate the same attribute name?

## Answer

Yes, if the two flows share no devices. When they share a device, the second flow doesn't save, and the product shows "Another flow already produces this attribute".

See [Initiate Attribute](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/initiate-attribute).
