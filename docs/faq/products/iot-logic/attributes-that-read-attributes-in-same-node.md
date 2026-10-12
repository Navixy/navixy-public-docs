---
title: Attributes that read other attributes in one Initiate Attribute node
description: "An Initiate Attribute node calculates its attributes in order, and each one can read the ones listed above it."
tags:
  - fundamentals
---

# Attributes that read other attributes in one Initiate Attribute node

## Question

Can one attribute use another attribute calculated in the same Initiate Attribute node?

## Answer

Yes. The **Initiate Attribute** node calculates its attributes in the order that they are listed, and each one can read the ones above it. Add the attributes in the order that they depend on each other.

A second **Initiate Attribute** node later in the flow also works and is clearer when the two values are unrelated.

See [Initiate Attribute](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/initiate-attribute).
