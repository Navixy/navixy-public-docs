---
title: Nodes after Device Action nodes
description: "A Device Action node finishes its path. An Output Endpoint node on the same branch keeps the calculated values."
tags:
  - fundamentals
---

# Nodes after Device Action nodes

## Question

Why can't I connect anything after a Device Action node?

## Answer

A **Device Action** node finishes a path: it sends its commands and passes nothing forward. To keep the values that the flow calculated for the same messages, connect an **Output Endpoint** node to the same branch as the **Device Action** node.

See [Device Action](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/device-action#where-it-sits-in-a-flow).
