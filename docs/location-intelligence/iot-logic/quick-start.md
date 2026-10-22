---
description: "How to build your first IoT Logic flow: connect devices, calculate an attribute, and send the result to the platform."
tags:
  - guide
  - beta
---

# Quick start

By the end of this page, you will have a working flow. The flow takes a reading from your devices, converts it into a value that your team can use, and sends the result to the Navixy platform.

The example converts the reading of a temperature sensor from Celsius to Fahrenheit. The same three nodes cover most first flows, and only the formula changes.

Before you start, you need at least one device that sends data into your workspace and a role that lets you create flows in IoT Logic. See [Access to flows](flow-management/README.md#access-to-flows).

## 1. Create the flow

There are three ways to start. This page builds a flow by hand, which is the way that shows you how the parts fit together.

| Start from | When |
| --- | --- |
| **Create flow** | An empty canvas. What this page does |
| **Upload flow** | A file downloaded from a flow you or a colleague already built |
| **Flow templates** | A prepared flow for a common case, which you then connect to your own devices. See [Flow templates](flow-management/templates.md) |

1. Open IoT Logic. The **Created flows** table lists the flows in your workspace. The table is empty if you have no flows yet.
2. Click **Create flow**.
3. Enter a **Flow name** that says what the flow is for, such as `Reefer temperature`. Use only characters from a standard English keyboard: accented and Cyrillic letters are refused when you save. Add a description if others will maintain it.
4. Leave **Flow enabled** on, and click **Save**.

The flow isn't created yet: it is created, and switched on, the first time you save it on the canvas.

The flow opens on the canvas, empty, next to the **Nodes** panel.

You now have an empty flow open on the canvas. It appears in **Created flows** once you save it.

## 2. Choose the devices

The **Data Source** node decides whose data runs through the flow.

{% stepper %}
{% step %}
### Add the node

Drag **Data Source** from the **Start with** group onto the canvas.
{% endstep %}

{% step %}
### Select the devices

Open the node to reach its settings. Search for the devices you want and select them. The **Selected** tab shows how many devices you chose.

Select only the devices that have the sensor that you are about to use. A device without the sensor still passes through the flow, but it produces nothing.
{% endstep %}

{% step %}
### Name the node and apply

Give the node a **Name** that identifies the group, such as `Reefer trucks`, then click **Apply changes**.
{% endstep %}
{% endstepper %}

## 3. Calculate the new value

The **Initiate Attribute** node creates a value the device never sent.

{% stepper %}
{% step %}
### Add the node

Drag **Initiate Attribute** from the **Process** group onto the canvas, then connect the **Data Source** node to it. See [Flow editor](flow-management/flow-editor.md) if you haven't drawn a connection before.
{% endstep %}

{% step %}
### Name the attribute

Open the node and enter an **Attribute name**. The value has this name from now on, so make the name descriptive: `temp_fahrenheit`.

Names use lowercase Latin letters, digits, and underscores. See [Attribute names](expressions/attributes.md) for the full rule.
{% endstep %}

{% step %}
### Write the formula

Enter the **Expression**:

```
temperature * 1.8 + 32
```

`temperature` is the attribute that the device reports. Type it exactly as the device sends it: check the name in [Flow monitoring](monitoring.md) if you aren't sure. The attribute button in the field lists only the attributes that this flow calculates, so it is empty in a first flow.
{% endstep %}

{% step %}
### Apply

Click **Apply changes**.
{% endstep %}
{% endstepper %}

## 4. Send the result on

The **Output Endpoint** node ends the path and sends the enriched message to the Navixy platform.

1. Drag **Output Endpoint** from the **End with** group onto the canvas.
2. Connect the **Initiate Attribute** node to it.

The node needs no settings. The new attribute appears in Tracking alongside the device's own readings. See [Output Endpoint](nodes/output-endpoint.md).

## 5. Save the flow

1. Click **Save flow** on the canvas. A message confirms the save.
2. Go back to the flow list. The flow is listed with the status **Enabled**.

Saving checks the whole flow. If something is wrong, the save stops and each problem is marked on the node it belongs to. Fix them and save again. A second save can find problems that the first one didn't report. See [Why flows won't save](troubleshooting/flow-wont-save.md).

## 6. Check that it works

Open Data Stream Analyzer and watch the data that arrives from one of the connected devices. See [Flow monitoring](monitoring.md). The new attribute appears alongside the readings it was calculated from, as soon as that device sends its next message.

A device that reports every few minutes takes a few minutes to show the new attribute. If the attribute doesn't appear at all, see [Troubleshooting](troubleshooting/README.md).

{% hint style="success" %}
Now you have:

* A flow with the status **Enabled** in **Created flows**
* Every selected device sending its data through it
* A new attribute, `temp_fahrenheit`, on every message those devices send from now on
{% endhint %}

## Next steps

* [Build conditional flows](flow-management/conditional-flow.md): Handle a reading over a threshold differently from the rest.
* [Earlier values](expressions/history.md): Read what a device reported earlier, to calculate a change rather than a value.
* [Device Action](nodes/device-action.md): Send a command to the device when a reading crosses a limit.
