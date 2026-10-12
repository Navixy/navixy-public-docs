---
hidden: true
noIndex: true
title: Message values in Device Action command text
description: "The Command string field of a Device Action node holds fixed text. A Webhook node sends values from the message."
tags:
  - reference
---

# Message values in Device Action command text

## Question

Can the command text in a Device Action node include a value from the message?

## Answer

The **Command string** field holds fixed text. To send data that changes with each reading, use a **Webhook** node.

See [Device Action](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/device-action) and [Webhook](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/webhook).
