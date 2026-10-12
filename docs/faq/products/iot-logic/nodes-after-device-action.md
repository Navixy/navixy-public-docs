---
hidden: true
noIndex: true
title: Nodes after Device Action nodes
description: "A Device Action node finishes its path. An Output Endpoint node connected in parallel sends the data on."
tags:
  - fundamentals
---

# Nodes after Device Action nodes

## Question

Why can't I connect anything after a Device Action node?

## Answer

A **Device Action** node finishes a path: it performs its actions and passes nothing forward. To send the data on as well, connect an **Output Endpoint** node to the same **IF/THEN Logic** node, in parallel.

See [Device Action](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/device-action).
