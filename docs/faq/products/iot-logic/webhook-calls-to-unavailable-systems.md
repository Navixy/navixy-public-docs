---
hidden: true
noIndex: true
title: Webhook calls to unavailable systems
description: "When the receiving system is down, the IoT Logic flow continues and the message still reaches its destination."
tags:
  - troubleshooting
---

# Webhook calls to unavailable systems

## Question

What happens to a Webhook call if the receiving system is down?

## Answer

The flow continues. The **Webhook** node doesn't read the response, so a failed call doesn't stop the message from reaching its destination.

A failed call isn't visible from inside the flow. Check the logs of the receiving system when calls seem to be missing.

See [Webhook](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/webhook).
