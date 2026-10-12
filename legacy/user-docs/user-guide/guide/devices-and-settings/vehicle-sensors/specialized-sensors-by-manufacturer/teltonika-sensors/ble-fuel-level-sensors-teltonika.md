---
description: "Add Teltonika BLE fuel sensors to Navixy: select the LLS level input, configure calibration, and monitor fuel, temperature, humidity, and battery charge."
---

# BLE fuel level sensors (Teltonika)

Many [Teltonika GPS devices](https://www.navixy.com/devices/teltonika/) support wireless Fuel sensors that connect via Bluetooth. The advantages of using these Bluetooth fuel sensors are significant:

* **No wires**: The sensor and GPS device do not need a physical connection, simplifying installation.
* **Independence from external power sources**: These sensors come with a built-in battery that can last for several years without the need for recharging.
* **Additional data**: In addition to fuel levels, the sensor also transmits data on temperature, humidity, and the battery charge level.

## GPS device preparation

To prepare your Teltonika GPS device for Bluetooth fuel sensor integration, follow these steps.

<div align="left"><figure><img src="https://www.navixy.com/wp-content/uploads/2019/09/teltonika.configurator_2019-09-28_13-56-33-600x365.png" alt="" width="563"><figcaption></figcaption></figure></div>



1. **Download the Teltonika configurator**: Obtain the app from the official Teltonika website.
2. **Update the firmware**: Ensure that your device is running the latest firmware version.
3. **Run the configurator**:

* Go to the **System** tab.
* Change the data transfer protocol to **Codec 8 Extended** (see the note below for why this is required).

4. **Connect the fuel sensor**:

* Navigate to the **Bluetooth** tab in the configurator.
* Connect the fuel sensor to the GPS device.

5. **Enable the necessary parameters**:

* Go to the **I/O** tab.
* Ensure that the parameter corresponding to the fuel sensor is enabled.

{% hint style="warning" %}
**Codec 8 Extended** is Teltonika’s proprietary communication protocol that supports up to 65,535 data parameters (AVL IDs), allowing for more detailed data transmission compared to the standard Codec 8, which supports only 255.
{% endhint %}

## Sensor setup in Navixy

Once the GPS device is connected and transmitting fuel data, follow these steps to set up the corresponding sensors on the Navixy platform.

<div align="left"><figure><img src="https://www.navixy.com/wp-content/uploads/2019/09/chrome_2019-09-28_13-59-40-600x296.png" alt="" width="375"><figcaption></figcaption></figure></div>

{% stepper %}
{% step %}
### Create a new measurement sensor

1. Navigate to Devices and settings → Sensors and buttons.
2. Click Create a new [measurement sensor](../../measurement-sensors/).
{% endstep %}

{% step %}
### Configure the sensor

1. Select the input labeled "BLE: LLS level".
2. Set the sensor type and units. If necessary, fill in the calibration table and adjust other settings.
{% endstep %}

{% step %}
### Repeat for additional sensors

If you have multiple fuel sensors, repeat the setup process for each sensor, selecting the appropriate input for each.
{% endstep %}

{% step %}
### Monitor and report

Once configured, you can monitor the fuel level in the designated widget on the platform. You can also generate detailed reports on fuel consumption.
{% endstep %}
{% endstepper %}
