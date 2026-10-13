---
description: >-
  How to build an IoT Logic flow that treats some messages differently from
  the rest, with a worked example from a temperature sensor.
tags:
  - guide
  - beta
---

# Build conditional flows

By the end of this page, you will have a flow that calculates a value, tests it, and handles the messages above a limit differently from the rest. Both paths reach the Navixy platform.

Most real flows have this shape. The [Quick start](../quick-start.md) builds a straight line from devices to output. The flow on this page branches.

Before you start, build the [Quick start](../quick-start.md) flow or read it. This page assumes that you know how to add a node, connect two nodes, and save a flow. You also need a role that lets you create flows. See [Access to flows](README.md#access-to-flows).

In this example, refrigerated trucks have a temperature sensor that reports a raw value. The business needs the temperature in degrees on every message. It also needs the messages above the safe limit to be marked, so that Data Stream Analyzer and Tracking show which trucks were over the limit.

## 1. Bring in the devices

Add a **Data Source** node and select the trucks that have the sensor. Name it `Reefer trucks`.

Select only the devices carrying that sensor. A device without it runs through the flow and gets only `over_limit`, always false, so leaving it in makes the flow harder to follow later.

## 2. Calculate the temperature

Add an **Initiate Attribute** node and connect the **Data Source** node to it. Name it `Conversions`.

Add one attribute, `temp_celsius`, with the coefficient from your sensor's datasheet:

```
temp_raw * 0.1
```

Every message from those devices now has `temp_celsius`, whatever happens later in the flow. When every message needs a value, calculate the value before the branch. A value that is calculated inside a branch exists only on that branch.

## 3. Test the temperature

Add an **IF/THEN Logic** node and connect the **Initiate Attribute** node to it. Name it `Over limit`.

Enter the condition:

```
temp_celsius != null && temp_celsius > 8
```

{% hint style="warning" %}
Start every condition with `attribute != null &&`. This check keeps a message without the value out of the then branch, whatever operator follows. The check works only when it comes first. See [Missing values](../expressions/missing-values.md).
{% endhint %}

Set **Expression name** to `over_limit` as well. The node then stores its own true or false answer as an attribute. Anything that reads the data can then count excursions, which are readings above the limit, without recalculating the threshold.

## 4. Mark the messages above the limit

Add a second **Initiate Attribute** node and connect the then branch of the Logic node to it: the upper outgoing point, marked with a check-mark icon. Name it `Excursion`.

Add one attribute, `excursion_degrees`, that records how far over the limit the reading was:

```
temp_celsius - 8
```

This node is on the then branch, so it runs only for messages above the limit. Messages on the else branch never reach this node and never have `excursion_degrees`.

## 5. Send both paths to the platform

Add one **Output Endpoint** node and name it `To platform`.

Connect two paths to the node:

* The **Excursion** node, which is the end of the then branch
* The else branch of the Logic node, directly: the lower outgoing point, marked with a cross icon

Both paths end at the same node. The flow allows this connection because only one of the two paths is live for any message: a message is either over the limit or not.

{% hint style="info" %}
Two branches that are both live must not meet. Here the two branches can't both be live, because the then and else branches are alternatives. Two branches that fan out from the same node and rejoin would fail the save, with **Two live paths reach this node, so one path's work would be lost**.
{% endhint %}

## 6. Save the flow

Click **Save flow**. With **Flow enabled** left on in the flow form, the first save creates the flow and switches it on. Go back to the flow list and check that the flow shows **Enabled**.

## 7. Check it

Open [Flow monitoring](../monitoring.md) and watch one truck. A message below the limit has `temp_celsius` and `over_limit`. A message above the limit also has `excursion_degrees`.

If `excursion_degrees` never appears, the condition is never true. If it appears on every message, the condition is always true. In both cases, start with the reading itself: compare the value of `temp_celsius` with the threshold that you set.

{% hint style="success" %}
Now you have:

* `temp_celsius` on every message from those trucks
* `over_limit`, true or false, on every message
* `excursion_degrees` on the messages above the limit only
{% endhint %}

## Next steps

* **Watch the excursions.** Open **Data Stream Analyzer** on the flow and select `over_limit` and `excursion_degrees` to see which trucks are over the limit as their messages arrive. Tracking shows the same attributes. See [Flow monitoring](../monitoring.md).
* **Test two things at once.** When both conditions must be true, join them with `&&` in one node rather than chaining two Logic nodes.
* **Compare against earlier readings** instead of a fixed threshold, so the flow reacts to a change rather than a level. See [Earlier values](../expressions/history.md).
