---
description: >-
  Forward GPS and telematics data from Navixy to the Unigis TMS platform,
  sending one position per minute per vehicle over HTTP.
---

# Unigis

Unigis is a transport management system, and the Unigis data forwarding protocol delivers vehicle positions from Navixy into it. Logistics departments at manufacturing and retail companies use it to receive data from their carriers, including suppliers to chains such as Walmart, Home Depot, and Kimberly-Clark. A carrier can send data to Unigis without giving the receiving company access to the Navixy account.

Unigis is most common with companies in the United States, Mexico, Colombia, Chile, Argentina, Brazil, and Spain.

In Navixy, select **UNIGIS** in the **Protocol** dropdown. That is how Unigis is named in the interface.

## What Navixy sends

Navixy sends one HTTP POST request per position to the `LoginYInsertarEvento` method of the Unigis endpoint. The body is form-encoded, and it contains these fields:

| Field | Contents |
| ----- | -------- |
| `SystemUser` and `Password` | The Unigis credentials from the retranslator configuration, sent with every request |
| `Dominio` | The External ID of the device, or the Navixy device id when External ID is empty |
| `Codigo` | The Navixy event code for this message |
| `Latitud`, `Longitud`, `Altitud` | Position in degrees, altitude in meters |
| `Velocidad` | Speed |
| `Rumbo` | Heading |
| `FechaHoraEvento` | When the device recorded the position, in ISO 8601 |
| `FechaHoraRecepcion` | When Navixy received the message, in ISO 8601 |

Events such as a door alarm, a panic button press, or an engine shutdown aren't separate fields. Each arrives as a value in `Codigo` on the message that reports it.

Unigis responds to each request with a numeric code. A negative code means Unigis rejected the position, and Navixy closes the connection and retries.

### When Navixy sends

Navixy sends at most one position per minute per device. When a device reports more often than that, the positions in between are skipped rather than queued, so the data in Unigis is a one-minute sample and not a full track.

Two rules override the one-minute limit:

* An urgent event is sent as soon as it arrives, even if the previous position went out less than a minute earlier.
* A message without valid coordinates is never sent, whatever its timing.

## Before you start

Collect the following from Unigis:

* The endpoint address and port. Unigis operates several hubs, so confirm which one applies to your account.
* Your Unigis login and password. Navixy requires both and won't save the retranslator without them.

You also need the license plate as it's registered on the Unigis side, which goes in the External ID field of each device. Navixy accepts any non-empty value here, so a plate that doesn't match the Unigis record is accepted at save time and then rejected by Unigis.

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

### Enter the Unigis settings

Fill in the fields as follows:

* **Name**: a label that identifies this retranslator
* **Protocol**: **UNIGIS**
* **Address**: the endpoint address from Unigis, for example `http://unigis2.unisolutions.com.ar/HUB/UNIGIS/MAPI/SOAP/GPS/Service.asmx`
* **Port**: the port from Unigis, commonly `80`
* **Login** and **Password**: your Unigis credentials

When you enter an address without a path, Navixy uses `/HUB/UNIGIS/MAPI/SOAP/COMMServer/service.asmx`. Enter the full address that Unigis gave you rather than relying on this default.
{% endstep %}

{% step %}

### Enable and save

Switch on **Enabled**, then click **Save**. A retranslator that isn't enabled sends nothing.
{% endstep %}

{% step %}

### Link the retranslator to a device

In the device's **Data forwarding** block, switch on the toggle for the retranslator you created, then click **Save**.
{% endstep %}

{% step %}

### Set the External ID

Click the link icon at the right of the retranslator row, then enter the license plate as registered on the Unigis side. Click **Save**.

Repeat this step and the previous one for every device that forwards to Unigis. The retranslator configuration is shared across the account, but External ID is set per device.
{% endstep %}
{% endstepper %}

## Manage forwarding

To change or stop forwarding to Unigis:

* Click the link icon at the right of the retranslator row to change the External ID of a device.
* Switch off the toggle to stop forwarding for one device, and keep the configuration for the others.
* Click **Protocols**, select the row, and edit it to change the name, address, or credentials. The change applies to every device linked to this retranslator.
* Delete the configuration from the **Protocols** list to stop forwarding for all devices. Confirm in the dialog.

## Troubleshooting

When data doesn't appear in Unigis, check the following in order:

1. The retranslator is enabled, and its toggle is switched on for the device.
2. The login and password match your Unigis credentials. Navixy sends them with every position, so a wrong password fails every request rather than only the first.
3. The address includes the full path that Unigis gave you, and the port is correct.
4. The External ID matches the license plate registered on the Unigis side.
5. The device reports valid coordinates. Positions without a GPS fix are never forwarded.

When positions arrive but there are fewer than expected, that's the one-minute limit rather than a fault. Unigis receives a one-minute sample of the track.
