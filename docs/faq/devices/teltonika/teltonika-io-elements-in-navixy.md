---
title: Teltonika I/O elements in Navixy
description: Navixy stores every I/O element that a Teltonika device sends, under a readable name when it knows the element and always as a raw value with its AVL ID
tags:
  - reference
---

# Teltonika I/O elements in Navixy

## Question

Which Teltonika I/O elements reach Navixy, and how do I set them up?

## Answer

Navixy stores every I/O element that arrives from a Teltonika device. You don't map elements to sensors in Navixy:

* An element that Navixy knows gets a readable name. For example, AVL ID 66 is the board voltage.
* Every element is also stored as a raw value, `avl_io`, with its AVL ID as the channel. In IoT Logic, read it as `avl_io_` followed by the AVL ID, such as `avl_io_221`.

The device decides which elements it sends. Set this up in Teltonika Configurator:

* An element with the priority **None** is never sent, so it never reaches Navixy.
* Any other priority works. The priority **Panic** marks the record as an alarm.
* **Event only** and **Operand** decide when the device includes an element in a record. See the Teltonika documentation.

The configuration SMS that Navixy sends at activation doesn't change these settings.

Teltonika numbers elements in two ways: parameter IDs in configuration commands, such as `setparam`, and AVL IDs in the data that the device sends. The names in Navixy follow the AVL ID. In a Teltonika SMS command, `setparam` starts with two spaces when the device has no SMS login and password.
