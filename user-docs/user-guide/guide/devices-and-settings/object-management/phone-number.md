---
description: Set the SIM phone number and APN for a GPS device in Navixy. Needed after a SIM change for SMS-based commands, location requests, and two-way communication.
---

# Phone number

Holds the SIM and mobile-data settings for the device's cellular modem. The GSM network connects the device to the Navixy platform over GPRS/EDGE and SMS, so the SIM details must be correct for communication and SMS-based commands.

<!-- SCREENSHOT: Phone number block, phone number and APN name/user/password fields, Change phone number button. Annotate: phone field, APN fields, the change/confirm flow. -->

## Settings

* **Phone number**: The SIM's phone number. Click **Change phone number** to edit it.
* **APN name, user, and password**: The mobile-data access point for the SIM's operator.

## Updating the SIM details

{% stepper %}
{% step %}
### Open and change

In the block, click **Change phone number**.
{% endstep %}

{% step %}
### Enter the number and APN

Specify the SIM's phone number. The platform auto-fills the operator's default APN, which you can override.
{% endstep %}

{% step %}
### Save

Click **Save**. An SMS command with the new settings is sent to the SIM, after a confirmation prompt.
{% endstep %}
{% endstepper %}

## Availability

Appears on devices whose model has a SIM/phone. It is the only block shown for blocked devices.

## Limitations

* If no number is set, the device shows as **"Inbox SIM"** (not tied to a specific SIM).
* Entering a number auto-fills the operator's default APN, override it if your provider differs.
* Changing the number sends an SMS command to the SIM and asks for confirmation first.

## See also

* [Trusted phone numbers](trusted-phone-numbers.md): numbers allowed to send commands to the device.
