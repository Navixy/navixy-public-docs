---
description: Configure external Bluetooth sensors on a GPS device in Navixy by MAC address and type, so the platform interprets their values correctly.
---

# Bluetooth sensors

Configures external Bluetooth sensors connected to certain device models. These sensors transmit raw data, often just a MAC address and a value, so you map each one here and the Navixy platform interprets it correctly. This block pairs with the platform-side [Sensors and buttons](../../vehicle-sensors/) block: saving here creates the corresponding sensors there.

<!-- SCREENSHOT: Bluetooth sensors block, MAC address field and sensor type selector. Annotate: MAC address entry, sensor type dropdown. -->

## How to configure Bluetooth sensors

{% stepper %}
{% step %}
### Open the block

In **Devices and settings**, select the device, then expand the **Bluetooth sensors** block.
{% endstep %}

{% step %}
### Enter the MAC address

Enter the unique MAC address (the hardware identifier printed on the sensor's label) of the Bluetooth sensor so the platform associates it with the correct device.
{% endstep %}

{% step %}
### Select the sensor type

Choose the sensor type:

* Temperature and humidity sensor
* Tire pressure and temperature sensor
* Panic button
* Hardware key
* Door sensor
* Relay
{% endstep %}

{% step %}
### Save

Save the configuration. The platform creates the corresponding sensors in the **Sensors and buttons** block.
{% endstep %}
{% endstepper %}

## Availability

Appears on device models that support external Bluetooth sensors.

## Limitations

* Bluetooth sensors often need configuration on the device side too. Channel numbering can differ between the device's configurator and Navixy.

## See also

* [Vehicle sensors](../../vehicle-sensors/): the platform-side sensors this block feeds.
