---
title: Parked vehicle shows Moving
description: "A parked vehicle shows Moving when its device is online but has lost GPS signal. The status changes when the device sends a usable position."
tags:
  - troubleshooting
---

# Parked vehicle shows Moving

## Question

Why does a vehicle show as **Moving** on the map when it is actually parked, for example in an underground garage?

## Answer

The GPS device in the vehicle is still **Online**, but it has no GPS signal. The motion status then keeps its value from the last usable position, which was **Moving**. A device loses its GPS signal in a tunnel, in underground parking, or inside a building. A damaged GPS antenna has the same effect.

Without a GPS signal, the device keeps sending messages, but the messages have no coordinates. The device therefore stays **Online**, and its marker stays at the last usable position. The motion status changes when the device sends a usable position again. A device that sends no message for 10 minutes becomes **Offline**, and its motion status is then **Unknown**, not **Moving**.

If one vehicle often shows **Moving** while it is parked, check its GPS antenna and the antenna connection.

See [Asset statuses](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/tracking#asset-statuses) in Tracking to learn what each status means.
