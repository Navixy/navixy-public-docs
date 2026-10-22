---
title: Several commands in one Device Action node
description: "A Device Action node can send up to 10 commands, in the order that they are listed."
tags:
  - how-to
---

# Several commands in one Device Action node

## Question

Can one Device Action node run several commands?

## Answer

Yes. Click **Add action** for each command, up to 10 in one node. The node sends them in the order that they are listed. The platform sends a **Switch output** command first, so it can reach the device before an earlier command string that is still waiting.

See [Device Action](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/device-action#standard-tab).
