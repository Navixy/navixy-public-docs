---
description: >-
  Forward GPS positions, cargo temperature, and humidity from Navixy to the
  Recurso Confiable platform over SOAP, used across Mexico and Central America.
---

# Recurso Confiable

Recurso Confiable is a security and logistics platform for transport operations, and this protocol forwards vehicle data from Navixy into it. Carriers use it to deliver tracking data to customers who monitor shipments through Recurso Confiable, without giving those customers access to the Navixy account. It's used in Mexico, Colombia, the United States, and Central America.

Navixy also offers **Recurso Confiable (Walmart)**, a variant for forwarding to Walmart through Recurso Confiable. It's configured the same way as described here.

In Navixy, select **Recurso Confiable** in the **Protocol** dropdown.

## What Navixy sends

Navixy sends one SOAP request per message to the `GPSAssetTracking` method. The request contains these fields:

| Field                                         | Contents                                                                                                                                                             |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `code`                                        | The AVL event code for this message. See [Recurso Confiable event codes](https://app.gitbook.com/s/5dyjvbUVXEbs60EnxnDz/using-navixy/recurso-confiable-event-codes). |
| `asset`                                       | The license plate, from the first part of External ID                                                                                                                |
| `shipment`                                    | The shipment id, from the second part of External ID                                                                                                                 |
| `customer`                                    | The customer id and customer name, from the third and fourth parts of External ID                                                                                    |
| `serialNumber`                                | The Navixy device id                                                                                                                                                 |
| `date`                                        | When the device recorded the message                                                                                                                                 |
| `latitude`, `longitude`, `altitude`           | Position in degrees, altitude in meters                                                                                                                              |
| `speed`, `course`, `direction`                | Speed, heading in degrees, and heading as a compass direction                                                                                                        |
| `ignition`                                    | Whether the ignition is on                                                                                                                                           |
| `odometer`                                    | Distance traveled                                                                                                                                                    |
| `battery`                                     | Battery level                                                                                                                                                        |
| `temperature`                                 | Cargo temperature, when the device reports it                                                                                                                        |
| `humidity`                                    | Cargo humidity, when the device reports it                                                                                                                           |
| `vehicleType`, `vehicleBrand`, `vehicleModel` | Vehicle details from Fleet management                                                                                                                                |

{% hint style="info" %}
Temperature and humidity make this protocol usable for cold-chain monitoring. Navixy reads whichever sensor is available, in this order of preference:

* Temperature: the internal temperature sensor, then an external temperature sensor, then a Bluetooth temperature sensor.
* Humidity: the internal humidity sensor, then a humidity sensor, then a Bluetooth humidity sensor.

When the device has no matching sensor, the field is sent empty.
{% endhint %}

### When Navixy sends

Navixy sends a message when it has valid coordinates, or when it reports an urgent event. An urgent event goes out even without a position. Any other message without coordinates is skipped.

## Before you start

Collect the following from Recurso Confiable:

* The endpoint address and port. The address is commonly `http://gps.rcontrol.com.mx/Tracking/wcf/RCService.svc` on port `80`.
* Your Recurso Confiable user id and password. Navixy requires both and won't save the retranslator without them.
* The license plate, shipment id, customer id, and customer name for each vehicle, as registered on the Recurso Confiable side.

Navixy authenticates by calling `GetUserToken` with the user id and password, then reuses the token until it expires.

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
### Enter the Recurso Confiable settings

Fill in the fields as follows:

* **Name**: a label that identifies this retranslator
* **Protocol**: **Recurso Confiable**
* **Address**: `http://gps.rcontrol.com.mx/Tracking/wcf/RCService.svc`
* **Port**: `80`
* **Login**: your Recurso Confiable user id
* **Password**: your Recurso Confiable password
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

Click the link icon at the right of the retranslator row, then enter up to four values joined by pipe characters, in this order:

```
licensePlate|shipmentId|customerId|customerName
```

For example, `ABC123|1|123|John`.

<figure><img src="../../../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

Only the license plate is required, and it must be 5 to 7 characters:

```
ABC123
```

When you skip a value in the middle, keep its pipe so the remaining values stay in the right positions:

```
ABC123||123|
```

<figure><img src="../../../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

Repeat this step and the previous one for every device that forwards to Recurso Confiable. The retranslator configuration is shared across the account, but External ID is set per device.
{% endstep %}
{% endstepper %}

## Manage forwarding

To change or stop forwarding to Recurso Confiable:

* Click the link icon at the right of the retranslator row to change the External ID of a device.
* Switch off the toggle to stop forwarding for one device, and keep the configuration for the others.
* Click **Protocols**, select the row, and edit it to change the name, address, or credentials. The change applies to every device linked to this retranslator.
* Delete the configuration from the **Protocols** list to stop forwarding for all devices. Confirm in the dialog.

## Troubleshooting

When data doesn't appear in the Recurso Confiable system, check the following in order:

1. The retranslator is enabled, and its toggle is switched on for the device.
2. The user id and password are correct. Navixy can't request a token without them, so no data is sent at all.
3. The address and port are correct.
4. External ID has the values in the right order, with pipes kept for any value you skipped.
5. The license plate, shipment id, customer id, and customer name match the records on the Recurso Confiable side.
6. The device reports valid coordinates, unless you expect only urgent events.

When positions arrive but temperature or humidity is empty, the device has no sensor that Navixy recognizes for that reading. Check the sensor configuration for the device.
