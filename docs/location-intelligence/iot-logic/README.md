---
description: >-
  Process telematics data without coding in IoT Logic. Receive data from GPS
  devices, add calculated values, and send the results to the Navixy
  platform.
tags:
  - overview
  - beta
noIndex: true
---

# IoT Logic

Your devices send what their manufacturer decided to send. IoT Logic changes this data before it reaches anything else.

IoT Logic is a tool in the Navixy platform for processing data without writing code. It gives telematics operators direct control over how device data is processed before it reaches the platform. They don't need a developer for every new requirement. It works with GPS devices, dash cams, and IoT sensors. Incoming data is normalized into a consistent format, whatever its origin. From there it is enriched with calculated attributes and sent on to the Navixy platform.

## What you can do with a flow

**Derive values the device never sent.** A sensor reports a raw count, and what the business needs is liters, a percentage, or degrees Fahrenheit. A formula converts it on every message. From then on, the message has the new value next to the reading that it came from.

**Compare a reading against the ones before it.** A flow keeps the last 12 values of every attribute, so a formula can look back rather than only at what just arrived. This history is the difference between knowing that fuel is at 40 liters and knowing that it fell 40 liters since the last message.

**Send data down different paths depending on what it says.** A message that meets a condition goes one way and the rest go another. One flow can calculate a value only for the vehicles that are speeding, or mark each speeding message so that Tracking shows it.

**Act on the data as it arrives.** A flow can send a command to the device that sent the message, such as switching on one of its outputs. The command goes out when the message arrives, not on a schedule.

{% hint style="info" %}
**Navigation**

To open IoT Logic, click **IoT Logic** in the Location Intelligence menu.
{% endhint %}

## Who it is for

IoT Logic is for the people who run a fleet, or run a service on top of a fleet. These people need the data shaped differently from the way that devices send it.

That work usually needs a developer and a service that sits between the devices and everything else. A flow replaces that service. The people who know what the data should mean can change it themselves, and the change takes effect on the next message.

## How a flow is built

A device sends its data as a **message**: a set of named values, such as `speed` or `temperature`. Each named value is an **attribute**. A value that a flow adds to the message is a calculated attribute.

A **flow** is a drawing on a canvas. Each step of a flow is a **node**. You drag in the nodes that you want and join them with lines. The data follows the lines.

Take a temperature sensor that reports a raw number, where the team needs degrees. Three steps do the conversion:

1. A **[Data Source](nodes/data-source.md)** node selects the devices. Every message that they send starts at this node.
2. An **[Initiate Attribute](nodes/initiate-attribute.md)** node calculates the degrees with a formula that you enter in a field and adds the degrees to the message.
3. An **[Output Endpoint](nodes/output-endpoint.md)** node sends the result to the Navixy platform, where Tracking shows the new value next to the device's own readings.

Add an **[IF/THEN Logic](nodes/logic.md)** node when some messages need different handling from the rest, and a **[Device Action](nodes/device-action.md)** node to send a command to the device. These nodes are the whole set: see [Nodes](nodes/).

Start from an empty canvas, from a [template](flow-management/templates.md), or from a flow that someone has already built.

### Formulas

Wherever a flow works something out or asks a question, you write it as a formula in a field, and it runs on every message. The same short language covers both: `temp_c * 1.8 + 32` to produce a value, `speed > 90` to ask a question.

See [Expressions](expressions/) to learn how to write formulas and how to read the earlier readings of a device.

### Watching it work

Data Stream Analyzer shows the latest values that arrive from the devices of a flow, live. The values that your flow added are next to the readings that they came from. It is where you confirm that a formula did what you meant, and the first place to look when it didn't.

See [Flow monitoring](monitoring.md).

## For developers

Whatever protocol a device uses, Navixy converts its data into one common format before a flow receives the data. A single set of rules therefore covers every device that you own.

You can create and manage flows through the Navixy GraphQL API as well as on the canvas. See the [Navixy GraphQL API documentation](https://navixy.com/docs/navixy-graphql-api). Through the API, your role allows the same actions on flows as on the canvas. See [Access to flows](flow-management/README.md#access-to-flows).

## Section contents

New to IoT Logic? Build your first flow in [Quick start](quick-start.md).

* [How flows work](how-flows-work.md)
* [Quick start](quick-start.md)
* [Flow management](flow-management/)
  * [Flow editor](flow-management/flow-editor.md)
  * [Build conditional flows](flow-management/conditional-flow.md)
  * [Devices without flows](flow-management/default-processing.md)
  * [Templates](flow-management/templates.md)
* [Nodes](nodes/)
  * [Data Source](nodes/data-source.md)
  * [Initiate Attribute](nodes/initiate-attribute.md)
  * [IF/THEN Logic](nodes/logic.md)
  * [Device Action](nodes/device-action.md)
  * [Output Endpoint](nodes/output-endpoint.md)
* [Expressions](expressions/)
  * [Attribute names](expressions/attributes.md)
  * [Earlier values](expressions/history.md)
  * [Missing values](expressions/missing-values.md)
  * [Formula examples](expressions/examples.md)
* [Flow monitoring](monitoring.md)
* [Troubleshooting](troubleshooting/)
  * [Why flows won't save](troubleshooting/flow-wont-save.md)

## Questions and answers

The [IoT Logic section of the FAQ](https://app.gitbook.com/s/iUOsBLCF28U0Cm4ggUHo/products/iot-logic) answers common questions about flows and nodes, such as whether two flows can calculate the same attribute.
