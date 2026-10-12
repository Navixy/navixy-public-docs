---
title: IoT Logic branches without Output Endpoint nodes
description: "A branch that doesn't reach an Output Endpoint node runs, but the flow discards its calculated values for the messages that take it."
tags:
  - fundamentals
---

# IoT Logic branches without Output Endpoint nodes

## Question

Can an IoT Logic branch end without an Output Endpoint node?

## Answer

Yes, the flow saves, but the branch adds nothing to the message. For a message that takes such a branch, the flow discards every value that it calculated, including the values calculated before the **IF/THEN Logic** node. The message still reaches the Navixy platform, with only what the device sent.

To keep the calculated values, connect every branch to an **Output Endpoint** node. Both branches of one **IF/THEN Logic** node can share one.

See [IF/THEN Logic](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/logic) and [Output Endpoint](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/output-endpoint).
