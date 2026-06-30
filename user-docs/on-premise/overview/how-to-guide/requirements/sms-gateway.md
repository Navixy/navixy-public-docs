---
description: "Select an SMS gateway for Navixy on-premise: cloud providers, SMPP-compatible SMS centers, or Yeastar hardware VoIP gateways for device activation and notifications."
---

# SMS gateway

Navixy highly recommends using an SMS center (SMSC) for outgoing messages and leasing a dedicated phone number for incoming SMS messages to utilize the full potential of the platform, including automatic device activation, user notifications and alerts, and device configuration directly from the user interface.

## Options for SMS center

1. **Cloud-based SMS service.** We support the following popular global SMS services:

* [https://www.twilio.com/](https://www.twilio.com/)
* [https://www.nexmo.com/](https://www.nexmo.com/)
* [https://www.textlocal.com/](https://www.textlocal.com/)
* [https://www.infobip.com/](https://www.infobip.com/)
* [https://www.tyntec.com/](https://www.tyntec.com/)

2. **Any service or device working over SMPP protocol.** SMPP is an alternative to HTTP API and is widely used by different SMS services world-wide. If your preferred service support SMPP v3.4, then we can work with it. To connect SMPP service or device with our platform, we need the following information:

* Mandatory parameters:
  * IP address of SMPP provider server
  * Port
  * Login (also known as system id)
  * Password
* Optional parameters:
  * Link timeout
  * Source TON/NPI
  * Destination TON/NPI
  * Default charset
  * Allowed sender

3. **Hardware VoIP devices.** We support Yeastar (former Neogate) line of products, specifically TG series:

* [https://www.yeastar.com/gsm-cdma-umts-gateways/](https://www.yeastar.com/gsm-cdma-umts-gateways/)

For successful delivery and automatic GPS trackers configuration your SMSC should comply with the following requirements:

* Permit to send SMS messages using the _'from'_ phone number in the international format – to all the GSM networks you will use
* Support texting using charsets in either GSM0338 8bit or Latin-1 (these formats are used to sending SMS commands to GPS trackers),  UCS2 (this format is used to sent texts with latin chars).

You can send the connection details for SMSC of your choice to Navixy technical support and we will configure them for you. If you prefer configuring them yourself, please refer to our how-to documentation on SMS gateway configuration.
