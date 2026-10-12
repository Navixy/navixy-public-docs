---
title: Deleting devices used in IoT Logic flows
description: "A device that an IoT Logic flow uses can't be deleted until you remove it from the flow."
tags:
  - reference
---

# Deleting devices used in IoT Logic flows

## Question

Can I delete a device that an IoT Logic flow uses?

## Answer

Not while the flow uses it. A device that is in a **Data Source** node of a flow can't be deleted or deactivated. Remove the device from every **Data Source** node that has it, or delete the flow, and then delete the device. A deleted flow doesn't stop the delete.

If you try while a flow still uses the device, the delete fails:

* In **Business Data Repository**, the message says that the device is still referenced by an IoT Logic Flow.
* In **Device Management**, **Deactivate** shows the message **Failed to load the data.**

A flow that still lists a deleted device saves and keeps running, and the **Connected devices** count in the flow list still includes the deleted device.

See [A device is gone](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/troubleshooting/flow-wont-save#a-device-is-gone), [Data Source](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/data-source), and [Manage devices](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/device-management/manage-devices).
