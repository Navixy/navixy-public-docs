---
description: >-
  Retransmit GPS, sensor, and event data to Wialon-based monitoring servers or
  to a second Navixy server using the Gurtam Wialon IPS protocol over TCP.
---

# Wialon IPS

Wialon IPS is a public protocol from Gurtam for passing GPS device data between servers. Because it's openly documented and widely supported, it's the general-purpose option when the receiving system isn't one of the named integrations.

Two uses are common:

* Forwarding data from Navixy to a Wialon-based monitoring server, or to any server that accepts Wialon IPS.
* Copying data between two Navixy servers, for example from a ServerMate account to an On-Premise installation.

Wialon IPS sends more of the original device data than any other forwarding protocol, because it includes every sensor reading the device reported as a named parameter.

In Navixy, select **Wialon IPS** in the **Protocol** dropdown.

## What Navixy sends

Navixy opens a TCP connection, sends a login packet, then sends one data packet per message. Both are plain text.

The login packet identifies the device and, when a password is set, authenticates:

```
#L#<device id>;<password>
```

Each data packet begins with `#D#` and contains these values in order:

| Value | Contents |
| ----- | -------- |
| Date and time | When the device recorded the position, in UTC |
| Latitude and longitude | Position in NMEA format |
| Speed | Speed |
| Heading | Direction of travel in degrees |
| Altitude | Altitude in meters |
| Satellites | Number of satellites used, or `NA` |
| HDOP | Horizontal accuracy, or `NA` |
| Inputs and outputs | Digital input and output states, or `NA` |
| Analog sensors | Analog sensor values, separated by commas |
| Driver id | The driver identification key, or `NA` |
| Named parameters | Every remaining sensor reading, as `name:type:value` |

The named parameters always include `EVENT`, which contains the Navixy event code. Navixy adds these when the data is available:

* `SOS`, set to `1` on an SOS or emergency contact event
* `raw_mileage`, the odometer reading in meters
* `battery_level`, the battery level
* `gsm.signal.csq`, the mobile signal strength
* One parameter for each additional sensor input the device reported

Navixy sends every message, including messages without a position.

## Before you start

Collect the following:

* The address and port of the receiving server.
* The Wialon IPS password, when the receiving server requires one. Many don't.

When you forward between two Navixy servers, use the address of the receiving server and port `47768`.

External ID isn't required. Navixy sends the device id unless you set one.

{% hint style="info" %}
To receive data from Wialon into Navixy rather than send it, point the Wialon side at `tracker.navixy.com` for the EU platform or `tracker.us.navixy.com` for the US platform, on port `47768`. You also need to create a device in Navixy using a Wialon IPS compatible model, such as Bitrek, so that Navixy accepts the incoming data.
{% endhint %}

## Set up forwarding

{% stepper %}
{% step %}

### Open the Data forwarding block

Go to **Devices and settings**, select a device, then find the **Data forwarding** block.
{% endstep %}

{% step %}

### Open the protocol list

Click **Protocols**, then click **+** to add a configuration.
{% endstep %}

{% step %}

### Enter the Wialon IPS settings

Fill in the fields as follows:

* **Name**: a label that identifies this retranslator
* **Protocol**: **Wialon IPS**
* **Address**: the address of the receiving server
* **Port**: the port of the receiving server, or `47768` between two Navixy servers
* **Password**: the Wialon IPS password, when the receiving server requires one

This protocol doesn't use a login. Leave that field empty.
{% endstep %}

{% step %}

### Enable and save

Switch on **Enabled**, then click **Save**. A retranslator that isn't enabled sends nothing.
{% endstep %}

{% step %}

### Link the retranslator to a device

In the device's **Data forwarding** block, switch on the toggle for the retranslator you created, then click **Save**. Leave External ID empty unless the receiving server requires a different device id.
{% endstep %}
{% endstepper %}

## Manage forwarding

To change or stop forwarding over Wialon IPS:

* Switch off the toggle in the **Data forwarding** block to stop forwarding for one device, and keep the configuration for the others.
* Click **Protocols**, select the row, and edit it to change the name, address, port, or password. The change applies to every device linked to this retranslator.
* Delete the configuration from the **Protocols** list to stop forwarding for all devices. Confirm in the dialog.

## Troubleshooting

When data doesn't appear on the receiving server, check the following in order:

1. The retranslator is enabled, and its toggle is switched on for the device.
2. The address and port are correct, and the receiving server accepts TCP connections on that port.
3. The password matches what the receiving server requires, when it requires one.
4. The receiving server accepts the device id that Navixy sends. When it requires a different id, set it in the External ID field.

When you receive data from Wialon into Navixy and nothing arrives, confirm that a device was created in Navixy with a Wialon IPS compatible model.
