---
title: GSENSOR calibration failure on JC261
description: The GCALIBRAT command is unsupported on JC261. Firmware v1.8.1.3+ performs automatic sensor calibration on power-on to reduce false events
---

# GSENSOR calibration failure on JC261

## Question

Why does JC261 respond with "calibration failed" to `GCALIBRAT`?

## Answer

{% hint style="info" %}
Although the documentation includes a calibration option, this command is not supported by the JC261 camera.
{% endhint %}

Starting from firmware version `JM_C261_V1.8.1.3_250925.1127`, the device performs automatic sensor calibration each time it powers on, with the aim of reducing false positive events.
