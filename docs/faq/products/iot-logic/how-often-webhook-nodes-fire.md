---
title: How often Webhook nodes fire
description: "A Webhook node fires once for every message that reaches it."
tags:
  - reference
---

# How often Webhook nodes fire

## Question

How often does a Webhook node fire?

## Answer

Once for every message that reaches it, not only when a value changes. A device that reports every 10 seconds produces a request every 10 seconds, unless a condition before the node limits the requests. A message that answers a command counts too.

See [Webhook](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/webhook).
