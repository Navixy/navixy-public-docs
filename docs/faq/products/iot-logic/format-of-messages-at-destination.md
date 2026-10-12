---
title: Format of messages that IoT Logic flows send
description: "A message leaves IoT Logic as the device sent it, with the flow's calculated attributes added and marked as computed."
tags:
  - reference
---

# Format of messages that IoT Logic flows send

## Question

What does a message from an IoT Logic flow look like when it reaches the Navixy platform?

## Answer

It is the message that the device sent, with the attributes that your flow calculated added to it. Each calculated attribute is marked as computed. The original readings are unchanged, unless a device starts sending a reading with the same name as a calculated attribute: the calculated value then replaces it.

See [Output Endpoint](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/output-endpoint).
