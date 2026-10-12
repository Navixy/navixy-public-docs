---
description: >-
  Forward vehicle positions from Navixy to the Startrack SOAP service, used by
  fleet operators in Guatemala, El Salvador, and Honduras.
---

# Startrack

Startrack receives vehicle positions from Navixy and makes them available to its own monitoring and management tools. Fleet operators in Central America use it, mainly in Guatemala, El Salvador, and Honduras, for fleet management, industrial and construction operations, and temperature-controlled transport.

In Navixy, select **Startrack** in the **Protocol** dropdown.

## What Navixy sends

Navixy sends one SOAP request per position to the `receiveWithConfirmation` method. The request contains these fields:

| Field | Contents |
| ----- | -------- |
| `vehicleId` | The External ID of the device, or the Navixy device id when External ID is empty |
| `dateAndTime` | When the device recorded the position, in ISO 8601 |
| `y` | Latitude in degrees |
| `x` | Longitude in degrees |
| `kmph` | Speed in kilometers per hour |
| `heading` | Direction of travel in degrees |
| `ignitionIsOn` | Whether the ignition is on |
| `locationIsValid` | Whether the position is a valid GPS fix |
| `trackingServiceProvider` | Always `navixy-retranslator`, identifying Navixy as the source |

Note that `x` is the longitude and `y` is the latitude.

Startrack confirms an accepted position with `<ok>true</ok>` in the response.

### When Navixy sends

Navixy sends every message that has valid coordinates. Messages without a position are skipped.

## Before you start

Collect the endpoint address and port from Startrack. The service is commonly reached at `https://cempro.gps.gt/gpsDataService?wsdl` on port `443`.

You also need the vehicle identifier that Startrack uses for each device, which goes in the External ID field.

This protocol doesn't use a login or password.

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

### Enter the Startrack settings

Fill in the fields as follows:

* **Name**: a label that identifies this retranslator
* **Protocol**: **Startrack**
* **Address**: `https://cempro.gps.gt/gpsDataService?wsdl`
* **Port**: `443`

Leave **Login** and **Password** empty.
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

Click the link icon at the right of the retranslator row, then enter the vehicle identifier that Startrack uses. Navixy accepts any value here, so a value that doesn't match the Startrack record is saved and then ignored by Startrack.

Repeat this step and the previous one for every device that forwards to Startrack.
{% endstep %}
{% endstepper %}

## Manage forwarding

To change or stop forwarding to Startrack:

* Click the link icon at the right of the retranslator row to change the External ID of a device.
* Switch off the toggle to stop forwarding for one device, and keep the configuration for the others.
* Click **Protocols**, select the row, and edit it to change the name, address, or port. The change applies to every device linked to this retranslator.
* Delete the configuration from the **Protocols** list to stop forwarding for all devices. Confirm in the dialog.

## Troubleshooting

When data doesn't appear in Startrack, check the following in order:

1. The retranslator is enabled, and its toggle is switched on for the device.
2. The address and port are correct.
3. External ID matches the vehicle identifier registered on the Startrack side.
4. The device reports valid coordinates.
5. Startrack confirms that it receives the data.

### Test the endpoint directly

To check that the Startrack endpoint accepts data, send a SOAP request with `curl` using a prepared XML file. A successful response looks like this:

{% code overflow="wrap" %}
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <ns2:receiveWithConfirmationResponse xmlns:ns2="https://cempro.gps.gt">
      <return>
        <ok>true</ok>
        <result>isOk:true statuses:[ADDED_TO_RECORD_Q] messages:[added to recorder queue]</result>
      </return>
    </ns2:receiveWithConfirmationResponse>
  </soap:Body>
</soap:Envelope>
```
{% endcode %}

The `<ok>true</ok>` element and the `ADDED_TO_RECORD_Q` status together confirm that Startrack queued the position for processing.
