---
title: Analog-digital input not showing in the Inputs widget
description: Manually map analog-digital inputs in Sensors and Settings by creating virtual or measurement sensors for each input parameter
---

# Analog-digital input not showing in the Inputs widget

## Question

Why does an analog-digital input not show in the Inputs widget?

## Answer

Some manufacturers include analog–digital inputs, which can read either an analog voltage range or digital values (0 for OFF, 1 for ON). Since the nature of this type of input is not fully digital, it cannot appear automatically in the "Inputs" widget.

Instead, it must be mapped manually in the Sensors & Settings section.

To visualize this input on the platform, you need to create a sensor (virtual or measurement type) and assign it to the corresponding analog or digital parameter.

**Example:** For Teltonika FMC920 devices, the desired digital state is found in the parameter **avl\_2**. Therefore, in Navixy you must create a virtual or measurement sensor that uses this **avl\_2** parameter so it can be displayed in the widget.

This behavior is common across multiple devices that have analog or digital readings. Keep it in mind when configuring inputs.
