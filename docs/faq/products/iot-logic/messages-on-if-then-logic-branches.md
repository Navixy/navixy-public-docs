---
title: Messages on IF/THEN Logic branches
description: "An IF/THEN Logic node sends the whole message down the branch that matched and adds only its own result."
tags:
  - fundamentals
---

# Messages on IF/THEN Logic branches

## Question

What happens to the message on each branch of an IF/THEN Logic node?

## Answer

The whole message goes to the branch that matched. Nothing is removed. The only thing that the node can add is its own result. It adds the result when its **Expression name** is filled in, and stores the result under that name.

See [IF/THEN Logic](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/logic).
