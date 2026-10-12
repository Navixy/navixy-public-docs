---
title: Two branches into one Output Endpoint node
description: "Two branches can reach one Output Endpoint node when only one of them runs for any message."
tags:
  - troubleshooting
---

# Two branches into one Output Endpoint node

## Question

Can two branches connect to the same Output Endpoint node?

## Answer

Yes, when only one of them is live for any given message, such as the **then** and **else** branches of one **IF/THEN Logic** node. A branch is live for a message when the message travels along it. Two branches that are both live for the same message and meet at one node stop the flow from saving.

See [Output Endpoint](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/output-endpoint).
