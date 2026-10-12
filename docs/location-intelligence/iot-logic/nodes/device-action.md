---
hidden: true
noIndex: true
description: >-
  The Device Action node sends a command back to a device when incoming data
  triggers it, either to the device that sent the data or to devices you map
  to it.
tags:
  - reference
  - beta
---

# Device Action

## What the node does

{% columns %}
{% column %}
The **Device Action** node sends a command to a device at the moment its data arrives.

A temperature crosses a limit and the node switches a relay. A vehicle enters a restricted area and the node runs an immobilizer command. The trigger is the reading itself, so the response happens on the next message rather than on the next check.
{% endcolumn %}

{% column %}
<figure><img src="../../.gitbook/assets/iot-logic-device-action-tile.png" alt="The Device Action node on the flow canvas, with one incoming connection point and no outgoing connection."><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

By default a command goes only to the device that sent the triggering data. The **Advanced** tab changes this default, so a reading from one device can act on others.

{% hint style="info" %}
**Device Action** is a node that finishes a path. It performs its actions and the path ends there, so no connection can follow it.
{% endhint %}

## Where it sits in a flow

<figure><img src="../../.gitbook/assets/iot-logic-device-action-in-flow.png" alt="A flow with an IF/THEN Logic node whose then branch leads to a Device Action node, while the else branch continues to an Output Endpoint node."><figcaption></figcaption></figure>

Connect it to the **then** branch of an **IF/THEN Logic** node so the actions run only when the condition is met.

{% hint style="warning" %}
Connect a **Device Action** node to an **IF/THEN Logic** node so its actions run only when the condition is met. Connected to anything else, it runs them on every message from every device in the flow.
{% endhint %}

Because the node ends the path, put an **Output Endpoint** node in parallel. Without one, every attribute that this flow calculated for the triggering message is discarded. The message still reaches the Navixy platform, but unenriched. See [IF/THEN Logic](logic.md).

## Device Action settings

{% columns %}
{% column width="58%" valign="middle" %}
The node's settings open in two tabs:

* **Standard** holds the actions the node runs.
* **Advanced** maps a triggering device to the devices the actions run on. Optional.
{% endcolumn %}

{% column width="42%" %}
<figure><img src="../../.gitbook/assets/iot-logic-device-action-form.png" alt="The Device Action node settings dialog showing the Standard tab, with an action type selector and its parameters."><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

### Standard tab

A node holds one or more actions, and runs them in the order they are listed.

| Field | What it does |
| --- | --- |
| **Name** | Required. What the node is called on the canvas |
| **Node ID** | The node's identifier, which you can't change |
| **Action type** | **Switch output** or **Send GPRS command** |
| **Add action** | Adds another action to this node, up to 10 |

#### Switch output

Turns a device output on or off: a relay, an immobilizer, a siren.

| Field | What it does |
| --- | --- |
| **Output number** | Which output to switch, 1 to 8. An output already used by another action in this node isn't offered |
| **Signal on** / **Signal off** | The state to set it to |

<figure><img src="../../.gitbook/assets/iot-logic-device-action-switch-output.png" alt="The Switch output action configured in the Device Action node, showing the output number selector and the signal on and off toggle."><figcaption></figcaption></figure>

#### Send GPRS command

Sends a command string to the device over its own protocol.

| Field | What it does |
| --- | --- |
| **Command string** | The command text, up to 512 characters |

<figure><img src="../../.gitbook/assets/iot-logic-device-action-gprs.png" alt="The Send GPRS command action configured in the Device Action node, showing the command string field."><figcaption></figcaption></figure>

The command text is specific to the device model. Check the manufacturer's documentation for the exact string, and confirm it on one device before you apply it to a fleet.

### Advanced tab

Without a mapping, a command goes to the device whose data triggered it. A mapping changes the target: a reading from one device runs the actions on other devices instead.

<figure><img src="../../.gitbook/assets/iot-logic-device-action-advanced.png" alt="The Advanced tab of the Device Action node, showing a mapping row with a source device and a list of target devices."><figcaption></figcaption></figure>

| Field | What it does |
| --- | --- |
| **Source device** | The device whose data triggers the actions |
| **Target devices** | The devices the actions run on. At least one |
| **Add custom mapping** | Adds another source and its targets |

Both lists offer the devices connected to this flow through its **Data Source** nodes. If you remove a device from the flow, it is removed from the mappings too.

A mapping covers the cases where the sensor and the actuator aren't the same device: a gate controller opened by a reading from an approaching vehicle, or a depot siren triggered by any of a group of trucks.

## How to configure a Device Action node

{% stepper %}
{% step %}
### Name the node

Enter a **Name** that says what the node does, such as `Lock trailer door`.
{% endstep %}

{% step %}
### Choose the action type

On the **Standard** tab, select **Switch output** or **Send GPRS command**, then fill in its fields.
{% endstep %}

{% step %}
### Add any further actions

Click **Add action** for each further command. They run in the order listed.
{% endstep %}

{% step %}
### Map the devices, if the actuator is a different device

If the actions should run on devices other than the triggering device, switch to **Advanced** and add a mapping. Otherwise, skip this step.
{% endstep %}

{% step %}
### Apply and connect it to a condition

Click **Apply changes**, then connect the node to the **then** branch of an **IF/THEN Logic** node.
{% endstep %}
{% endstepper %}
