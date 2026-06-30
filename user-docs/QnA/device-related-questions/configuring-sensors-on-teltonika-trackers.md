---
title: Configuring sensors on Teltonika trackers
description: Add measurement sensors to Teltonika devices using the configurator tool, parameter settings, and AVL IO mapping. Covers FMB920 sensor setup in Navixy
---

# Configuring sensors on Teltonika trackers

## Question

How to configure a measurement/discrete sensor on a Teltonika tracker?

## Answer

To configure a sensor on a Teltonika tracker, follow the step-by-step guidance below.

This is a common guide for setting up sensors and fields for Teltonika devices. Using this guide, you can set up almost any sensor for Teltonika devices in Navixy.

Here we provide instructions for Teltonika FMB920, where we configure Board Voltage as an example.

{% stepper %}
{% step %}
## Download the configurator

Download the correct configurator version for your model:

* [https://wiki.teltonika-gps.com/view/Teltonika\_Configurator\_versions](https://wiki.teltonika-gps.com/view/Teltonika_Configurator_versions)

Find your model (Ctrl+F), then download the latest version.

![](<../.gitbook/assets/Unknown image (147)>)

If it is your first time downloading and using a Teltonika configurator, unzip the archive, install the additional software that comes with the configurator, and only then open the configurator.

![](<../.gitbook/assets/Unknown image (148)>)
{% endstep %}

{% step %}
## Open the configurator

Connect the device to the PC or create an offline preset.

If you don't have the device on hand, you can upload the configuration later using FOTA. Or you can configure the device remotely via SMS/GPRS using Air Console (see the optional step below).

![](<../.gitbook/assets/Unknown image (149)>)

![](<../.gitbook/assets/Unknown image (150)>)

![](<../.gitbook/assets/Unknown image (151)>)
{% endstep %}

{% step %}
## Choose the parameter

Find the parameter by meaning.

Example: Board Voltage may be named **External Voltage**.

![](<../.gitbook/assets/Unknown image (152)>)
{% endstep %}

{% step %}
## Configure sending settings

To work well with Navixy:

* Priority: **Low**
* Event Only: **No**
* Operand: **Monitoring**

![](<../.gitbook/assets/Unknown image (153)>)

Save the configuration to the device.

If the device is not nearby, you can update the configuration online via Air Console.
{% endstep %}

{% step %}
## Optional: set parameters via Air Console (GPRS) / SMS

If the device is remote, configure via Air Console or SMS.

Look up the **Parameter ID** by hovering in configurator.

Example:

* External Voltage priority ID: `50080`
* Priority Low value: `1`

Command:

`setparam 50080:1`

Use the Teltonika parameter list to confirm values:

* https://wiki.teltonika-gps.com/view/FMB920\_Parameter\_list

{% hint style="info" %}
When sending Teltonika configuration commands via SMS, add **two leading spaces** before the command. It is mandatory for SMS delivery.
{% endhint %}
{% endstep %}

{% step %}
## Verify the parameter arrives to Navixy

Find the **AVL ID** (not Parameter ID).

Example for External Voltage AVL ID is `66`.

Teltonika AVL list example:

* https://wiki.teltonika-gps.com/view/FMB920\_Teltonika\_Data\_Sending\_Parameters\_ID

In Air Console, verify `avl_io 66` appears.

![](<../.gitbook/assets/Unknown image (165)>)
{% endstep %}

{% step %}
## Create the sensor in Navixy

1. Open **Devices & Settings**.
2. Select the tracker.
3. In **Sensors and buttons**, add a measurement sensor.
4. Choose input type **AVL IO \[N]**.
5. Enter the AVL IO number (`66` in this example).

![](<../.gitbook/assets/Unknown image (168)>)
{% endstep %}

{% step %}
**Save**


{% endstep %}
{% endstepper %}
