---
description: "What happens to a device's data when no IoT Logic flow handles it, and what a flow changes."
tags:
  - fundamentals
  - beta
---

# Devices without flows

Most devices work without a flow, and most workspaces have far more devices than flows. Their data reaches the Navixy platform unchanged.

This page explains what a flow adds, so that you can tell which devices need a flow.

## What happens with no flow

The device sends its data. The platform receives it, stores it, and shows it in Tracking, reports, and everything else that reads device data. No flow is involved and nothing is added.

The same is true for a device whose only flow is switched off. While the flow is switched off, it adds nothing, and the readings of the device still arrive.

One rule applies to every device, with or without a flow: a message older than one already received from the same device is left out, and doesn't reach the platform. A device that uploads stored messages after reconnecting can lose some of them. See [Messages from a device are missing](../troubleshooting/README.md#messages-from-a-device-are-missing).

## What a flow adds

A flow adds to the device's data.

| Without a flow | With a flow |
| --- | --- |
| The readings the device sends | The readings the device sends, unchanged |
| No calculated attributes | The attributes your flow calculates |
| No commands to the device | Commands to the device, when the flow has a **Device Action** node |

Everything the device reported is still there afterwards. A flow that calculates a fuel percentage doesn't remove the raw sensor count it was calculated from.

## When a device needs a flow

Add a device to a flow when one of these conditions is true:

* **The reading isn't in the unit that you need.** For example, a raw sensor count, a temperature in the wrong scale, or a value that needs a coefficient applied.
* **You need a value that the device never sends.** For example, a difference against an earlier reading, a combination of two sensors, or a decoded bit from a status word.
* **You need something to happen when the data says so.** For example, a command that switches on one of the device's outputs when a reading crosses a limit.

A device that needs none of these doesn't need a flow.

## Where the result goes

An **Output Endpoint** node sends the message with its calculated attributes to the Navixy platform. The platform is the same destination that the device data reaches without a flow.

For this reason, every flow ends in an **Output Endpoint** node. The flow adds to what the platform already receives and doesn't send the data somewhere else instead.
