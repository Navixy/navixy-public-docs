---
description: >-
  What to do when an IoT Logic flow won't save, an attribute doesn't
  appear, a value isn't what you expected, or a device command doesn't arrive.
tags:
  - troubleshooting
  - beta
---

# Troubleshooting

Start from what you see. Each section of this page names one symptom and lists the checks that find its cause, in the order to try them.

## The flow won't save

The flow isn't saved, and one or more messages appear on screen. [Why flows won't save](flow-wont-save.md) lists every message, what causes it, and how to fix it.

Fix every problem in the list before you save again, not only the first problem. One save finds most problems, so one round of fixes usually clears them.

Everything else on this page is for a flow that saved and then didn't do what you expected.

## A calculated attribute doesn't appear

The flow is enabled, the device is reporting, and the attribute isn't in the data.

{% stepper %}
{% step %}
### Confirm that the flow is switched on

Only a flow with the status **Enabled** processes data. Check the **Status** column in **Created flows**.
{% endstep %}

{% step %}
### Confirm that the device is attached to this flow

The fastest check is **Mapping table** on the IoT Logic start page. It lists every device that is selected in a flow, with the flows that it enters. If the device isn't in the list, no flow has it.

Then open the flow's **Data Source** node and check the **Selected** tab. When you save the node, the node's whole device list is replaced. A device can be missing because a later edit replaced the list instead of adding the device to it.
{% endstep %}

{% step %}
### Check where the path splits

Follow the path from the **Data Source** node to the node that calculates the attribute. If a node other than **IF/THEN Logic** connects to two or more nodes on the way, all those paths are live, and the flow keeps the calculated values of only one of them. The flow saves without an error. Split the path with an **IF/THEN Logic** node instead. See [Live paths](../how-flows-work.md#live-paths).

A node that connects to a **Device Action** node and an **Output Endpoint** node on the same branch is safe. The **Device Action** node adds no values, so the flow loses nothing.
{% endstep %}

{% step %}
### Check the attribute name in the formula

A name that matches nothing returns nothing, and the attribute is left out. A wrong attribute name is the most common cause.

Compare the name in the formula against what the device actually sends, in [Flow monitoring](../monitoring.md). For an attribute that the flow calculates, use the attribute button in the expression field to insert the name rather than typing it.
{% endstep %}

{% step %}
### Check whether the value was in that message

A formula that reads index 0 gets nothing if the message doesn't contain the attribute. The formula doesn't use an earlier reading instead. A sensor that reports less often than the device sends messages produces exactly this pattern: the attribute appears on some messages and not others.

To use the last reading that had the value, see [How to use the last known value instead](../expressions/missing-values.md#how-to-use-the-last-known-value-instead). See [Missing values](../expressions/missing-values.md) to learn what an absent value does to a condition.
{% endstep %}

{% step %}
### Check the types

Rule out a reading that arrives as text where you assumed a number. To see what the device sends, open [Flow monitoring](../monitoring.md). The save accepts the formula in both cases, because the syntax is correct and only the values don't fit.
{% endstep %}

{% step %}
### Check that there is history

A formula that reads an earlier value of a calculated attribute produces nothing until the flow has calculated that attribute on an earlier message. The device's own readings keep their history whether or not the device is in a flow.
{% endstep %}
{% endstepper %}

## The wrong messages take a branch

An **IF/THEN Logic** node sends messages to a branch that you didn't expect. Most often, every message takes the branch instead of the few messages that you meant.

Check the operator first. A condition that uses `!=` or `!~` returns `true` when the value is missing. A device that never reports the attribute then takes the then branch on every message. A condition that uses `>` or `<` fails instead, and the message goes to the else branch.

Guard the condition with `!= null &&` ahead of the comparison. See [Missing values](../expressions/missing-values.md).

## The attribute appears but the value is wrong

The formula runs. The arithmetic isn't what you meant.

* Check the order of operations. `a + b * 2` doubles `b` only. Parentheses fix it.
* Check the coefficient against the sensor's datasheet, not against a value copied from another flow.
* Check which channel you are reading. A device with two of the same sensor reports each on its own channel, and channel numbering starts at 0. See [Attribute names](../expressions/attributes.md).
* Check `valid` against `all` when the formula reads an earlier value. The two values differ whenever the attribute isn't in every message. See [Earlier values](../expressions/history.md).

## Messages from a device are missing

Readings from a period are absent, most often after the device was offline.

IoT Logic discards a message that arrives after a newer message was already processed. A device can store readings while it is offline and send them together when it reconnects. That device loses the readings that are older than a reading that IoT Logic already handled.

Dropping a late message isn't configurable. It applies to the whole message rather than to one attribute, and to every device, including devices in no flow.

Readings can also be missing for many devices at once, for a short period. IoT Logic skips a message when the Navixy platform can't identify which device the message came from, for example during a platform slowdown. No flow runs on a skipped message, and the message isn't stored. The same applies to a message with a value that the platform can't read. No flow setting changes this behavior. If readings are missing across many devices for the same period, contact Navixy.

## The flow can't be switched on

Switching the flow on in **Created flows** fails with "The flow can't be enabled while it has problems. Open it and fix them."

Switching a flow on runs the same checks as a save. Open the flow and click **Save flow** to see the problems on the canvas. Fix them, save, and switch the flow on again. See [Why flows won't save](flow-wont-save.md).

## The flow changed while you were editing it

The save fails with "The flow changed while you were editing it. Reload it and repeat your change."

Someone else saved the same flow after you opened it, or you saved it in another browser tab. Your changes aren't saved. Reload the page to open the latest version, then make your change again.

## Data Stream Analyzer is unavailable

The **Data Stream Analyzer** tab can't be selected, and its tooltip reads "Save the flow and enable it to watch its data".

The tab is available only for a flow that has no unsaved changes and is switched on. Click **Save flow**, and check that the flow shows **Enabled** in **Created flows**. See [Flow monitoring](../monitoring.md).

## Calculated values stop when a flow is switched off

When you switch off a flow, the calculations of that flow stop. The device's own readings still reach the Navixy platform, unchanged.

If a dashboard stopped showing a value, the missing value is the calculated attribute, not the device's data.

## A device command doesn't arrive

A **Device Action** node should have sent a command, and the device didn't act on it. The interface doesn't show the status of a command, so work through these checks:

* **Check the device model.** Only Teltonika devices receive commands. For a device of any other model, or a device without an IMEI, the node sends nothing, and nothing on screen says so.
* **Check that the node is on a conditional branch.** If no **IF/THEN Logic** node comes before the **Device Action** node on its path, the node runs on every message. If it follows a condition that is never true, it never runs at all.
* **Check the condition.** Look at the attributes in [Flow monitoring](../monitoring.md) and work out whether the condition was true for the message.
* **Check the custom mappings.** On the **Advanced** tab, a custom mapping sends the commands for a source device to its target devices instead. The source device gets no command unless it is one of the targets too.
* **Check the command string** against the manufacturer's documentation. The command text is specific to the device model, and the device ignores a string that it doesn't recognize. The text is sent exactly as written, and characters outside the standard English keyboard don't arrive as you entered them.
* **Check whether the device was online.** A command for an offline device waits in a queue and is sent when the device next connects. A **Switch output** command waits up to 3 days. A **Send GPRS command** action waits up to 1 hour. After that time, the command is dropped.
* **Check how many commands wait for the device.** Up to 100 commands can wait for one device. When 100 commands are waiting, a new command is refused and lost. A command that matches a command already waiting isn't added again.

The node sends a command on every message that reaches it, not only when a value changes. While the condition holds, a device that reports every 10 seconds gets a command every 10 seconds. See [Device Action](../nodes/device-action.md).

## The flow saves but nothing happens at all

* Confirm that the flow is switched on, in the **Status** column.
* Confirm that the path reaches an **Output Endpoint** node. A path that stops before an **Output Endpoint** node keeps nothing that the flow calculated. A path that ends at a **Device Action** node keeps nothing either, so connect an **Output Endpoint** node to the same branch as the **Device Action** node.
* Confirm that the reporting devices are the devices that you expect. Check the **Connected devices** count in **Created flows**.
