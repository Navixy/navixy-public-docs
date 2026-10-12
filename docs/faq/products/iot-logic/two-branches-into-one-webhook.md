---
hidden: true
noIndex: true
title: Two branches into one Webhook node
description: "Two branches can reach one Webhook node when only one of them runs for any message, such as then and else."
tags:
  - troubleshooting
---

# Two branches into one Webhook node

## Question

Can two branches send to the same Webhook node?

## Answer

Yes, when only one of them runs for any given message, such as the **then** and **else** branches of one **IF/THEN Logic** node.

Two branches that can both run for the same message and meet at the node stop the flow from saving, with the message "Two live paths reach this node, so one path's work would be lost."

See [Webhook](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/webhook).
