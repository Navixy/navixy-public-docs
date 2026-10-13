---
title: Chained conditions in IoT Logic
description: "IoT Logic conditions can be chained by connecting one IF/THEN Logic node to another, or joined with && in one node."
tags:
  - how-to
---

# Chained conditions in IoT Logic

## Question

Can I chain two conditions in IoT Logic?

## Answer

Yes. Connect the then branch of one **IF/THEN Logic** node to another **IF/THEN Logic** node to test something further. When both conditions must be true, it's simpler to join them with `&&` in one node.

A later node can also read the result of an earlier node if the earlier node has an **Expression name**. The result is an ordinary attribute, so a condition can test it like any other value.

See [IF/THEN Logic](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/logic).
