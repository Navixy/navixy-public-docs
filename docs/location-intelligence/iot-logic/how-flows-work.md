---
description: "How a device message travels through IoT Logic: what happens to it, in what order, and what comes out the other end."
tags:
  - fundamentals
  - beta
---

# How flows work

A flow runs once for every message that a device sends, as soon as the message arrives. This page follows one message from the device to the Navixy platform.

## One message in, one message out

Whatever a device sends is one message. IoT Logic receives the message, runs it through every flow that the device belongs to, and sends on a single result.

That result is the message that the device sent, with the values that your flows calculated added to it. The original readings are all still there, unchanged. The only exception is described in [Attribute names](expressions/attributes.md#names-you-choose): a device that starts sending a reading with the same name as a calculated attribute.

A device can belong to several flows at once and still produce one message at the end, rather than one message for each flow.

## The order of events

{% stepper %}
{% step %}
### The message arrives

The device sends its data, and the data reaches IoT Logic already decoded into a common format. Whatever protocol the manufacturer used, from this point on every message has the same shape.
{% endstep %}

{% step %}
### Its flows are found

IoT Logic finds every switched-on flow that the device is selected in, and in each flow, the **Data Source** node that the message starts from. A device selected in two **Data Source** nodes of one flow runs through that flow twice, from two starting points.
{% endstep %}

{% step %}
### Each flow runs

Inside a flow, the message moves from its **Data Source** node along the connections that you drew. Each node acts on the message in turn:

* An **Initiate Attribute** node calculates new values and adds them to the message.
* An **IF/THEN Logic** node evaluates its condition and sends the message down the **then** branch or the **else** branch.
* An **Output Endpoint** node finishes the path and sends the result to the Navixy platform.

When a path reaches an **Output Endpoint** node, the flow keeps what it calculated. A path that finishes anywhere else keeps nothing: the message still reaches the platform, but with only what the device sent. See [IF/THEN Logic](nodes/logic.md).

A value calculated early in a flow is available to every node after it, so you can build one attribute from another.
{% endstep %}

{% step %}
### Values are stored

The history of the device keeps everything that the device reported. It also keeps the values that a flow calculated on a path that reached an **Output Endpoint** node. A formula can read the last 12 values. See [Earlier values](expressions/history.md).
{% endstep %}

{% step %}
### The result is sent on

One message leaves IoT Logic, with the readings and everything that the flows added.
{% endstep %}
{% endstepper %}

## Live paths

A path is **live** for a message when the message travels along it.

* The **then** and **else** branches of an **IF/THEN Logic** node are never live for the same message. Each message takes one of them.
* Two connections that leave any other node are both live. The message travels along both.

Two live paths must not meet at one node: the flow doesn't save. Two live paths that don't meet save without an error, but the flow keeps the calculated values of only one of them. To process the same messages in two ways, split the path with an **IF/THEN Logic** node. See [Nodes](nodes/README.md).

## What happens when a device has no flow

A device that belongs to no flow, or whose only flow is switched off, still reaches the platform. Its message passes through unchanged, with no calculated attributes added. A message older than one already received from the same device is left out, with or without a flow.

## What a flow can't do

* **Connections move forward only.** You can't save a flow that has a loop.
* **A calculation sees one device.** It reads the values of the device whose message IoT Logic handles, and the earlier readings of that device.
* **A formula reads the timestamps of the message.** It reads them through `event_time`, rather than the current time.
* **Each calculated attribute belongs to one flow per device.** Two flows that share a device can't both produce `temp_celsius`. Two flows that cover separate fleets can. See [Attribute names](expressions/attributes.md).
* **A late message is left out.** IoT Logic handles the messages of each device in the order that they were recorded. A reading that arrives after IoT Logic has handled a newer reading is left out rather than applied out of sequence. A device that stores readings while offline and sends them in a burst loses the readings that arrive too late. See [Troubleshooting](troubleshooting/).
