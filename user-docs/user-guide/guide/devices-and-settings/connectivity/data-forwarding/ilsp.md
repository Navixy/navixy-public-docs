---
description: >-
  Forward vehicle positions and security alarms from Navixy to ILSP monitoring
  centers in Mexico, using an OAuth-authenticated JSON API.
---

# ILSP

ILSP provides private security services in Mexico, and the ILSP data forwarding protocol delivers vehicle positions and security alarms into its monitoring centers. Companies that share a monitoring center use it to give ILSP visibility of their vehicles, and one Navixy account can forward to several ILSP customers at once.

In Navixy, select **ILSP** in the **Protocol** dropdown.

## What Navixy sends

Navixy sends one HTTP POST request per message to `/CustomerServices/api/SetLastEvent`, with a JSON body containing these fields:

| Field | Contents |
| ----- | -------- |
| `customerId` | The customer number, taken from the first part of External ID |
| `transportLineId` | The transport line number, taken from the third part of External ID |
| `plates` | The license plate, taken from the second part of External ID |
| `generatedEvent` | The event code: `2` for SOS, `3` for power lost, `4` for GSM damp alarm, and `1` for every other message |
| `generatedEventDate` | When the device recorded the message |
| `latitude` and `longitude` | Position in degrees |
| `speed` | Speed |
| `heading` | Direction of travel |
| `odometer` | Distance traveled, sent only when the device reports it |
| `battery` | Battery level, sent only when the device reports it |

### When Navixy sends

Navixy sends a message when it has a valid position, or when it reports an SOS, a power loss, or a GSM damp alarm. An alarm goes out even without a position, so ILSP receives the alert. Any other message without a position is skipped.

## Before you start

Collect the following from ILSP:

* The endpoint address and port. The address is commonly `https://www.ilspservices.com.mx/` on port `443`.
* Your client id and client secret. Navixy requires both and won't save the retranslator without them.
* The customer number, license plate, and transport line number for each vehicle.

{% hint style="info" %}
ILSP authenticates with OAuth rather than with a username and password. Enter the client id in the **Login** field and the client secret in the **Password** field. Navixy requests a token before sending data, and renews it when it expires. After five failed token requests Navixy disables the retranslator, so a wrong client id or secret stops forwarding rather than retrying indefinitely.
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

### Enter the ILSP settings

Fill in the fields as follows:

* **Name**: a label that identifies this retranslator
* **Protocol**: **ILSP**
* **Address**: `https://www.ilspservices.com.mx/`
* **Port**: `443`
* **Login**: your ILSP client id
* **Password**: your ILSP client secret
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

Click the link icon at the right of the retranslator row, then enter the three values that identify this vehicle to ILSP, joined by a pipe character:

```
customerId|licensePlate|transportLineId
```

For example, `4821|ABC-123|17`. All three parts are required. The customer number and the transport line number must be digits, and the license plate may contain uppercase letters, digits, and hyphens.

Repeat this step and the previous one for every device that forwards to ILSP. The retranslator configuration is shared across the account, but External ID is set per device.
{% endstep %}
{% endstepper %}

## Manage forwarding

To change or stop forwarding to ILSP:

* Click the link icon at the right of the retranslator row to change the External ID of a device.
* Switch off the toggle to stop forwarding for one device, and keep the configuration for the others.
* Click **Protocols**, select the row, and edit it to change the name, address, or credentials. The change applies to every device linked to this retranslator.
* Delete the configuration from the **Protocols** list to stop forwarding for all devices. Confirm in the dialog.

## Troubleshooting

When data doesn't appear in the ILSP system, check the following in order:

1. The retranslator is enabled, and its toggle is switched on for the device.
2. The client id and client secret are correct. Five failed token requests disable the retranslator, so correct the credentials and then switch **Enabled** off and on again.
3. The address and port are correct.
4. External ID has all three parts in the right order, separated by pipes, with no spaces around them.
5. The customer number, license plate, and transport line number match the records on the ILSP side.
