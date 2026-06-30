---
title: JC261(P/D) Input Mapping
description: "JC261 maps 3 logical inputs in Navixy: Input 1 is SOS button (event only), Input 2 is ignition, Input 3 is input voltage. Device has 2 physical inputs"
---

# JC261(P/D) Input Mapping

**Question:** Why does the JC261(P/D) display 3 inputs in Navixy if the device physically only has 2 inputs?

**Answer:**\
The three inputs displayed in Navixy are related to how the camera internally interprets and reports its input signals. Although the device physically provides only two external inputs, the camera also treats the SOS button as an additional logical input.

As a result, the input mapping is represented as follows:

* **Input 1:** SOS button
* **Input 2:** Ignition
* **Input 3:** Input voltage

However, Input 1 will not display an “active” status when the SOS button is pressed. This is because the input is intended only for event detection purposes and not for monitoring a closed/open electrical circuit state.

<p align="center"><img src="../.gitbook/assets/image (10).png" alt="" data-size="original"><br></p>

Links: [Event Configuration on JC events ](https://navixy.com/docs/expert-center/vehicle-telematics-technology/video-telematics/configuration-guides/jimi-iot/event-configuration-on-jimi-jc-series)

