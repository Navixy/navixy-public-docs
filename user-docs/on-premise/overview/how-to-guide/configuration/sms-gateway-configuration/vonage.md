---
description: Configure the Vonage SMS gateway in Navixy on-premise for IoT-compatible messaging. Add API key and secret credentials using the JSON configuration example.
---

# Vonage

[Vonage](https://www.vonage.com/) is a cloud communications platform that offers a wide range of messaging and voice solutions, including SMS, voice, and video APIs. The platform allows businesses to connect with their customers through multiple channels and devices, leveraging powerful APIs and advanced messaging features. Vonage's SMS API provides fast and reliable message delivery, global coverage, and support for multiple messaging formats.

We've found that the Vonage SMS gateway is compatible with the majority of IoT devices and can handle various types of SMS commands. For instance, we've tested the gateway with Teltonika and Ruptela devices and found that it can process SMS commands even if they contain leading spaces or other non-standard formatting.

Navixy supports Vonage as an SMS gateway provider, enabling users to leverage its messaging capabilities within the Navixy platform. To use Vonage with Navixy, you'll need to obtain your Vonage **API key** and **secret**, which are unique to your Vonage account and used for authentication.

These credentials must be included in the JSON configuration for the Vonage gateway in the 'sms\_gates' table of the 'google' database. Once configured, you can use Vonage to send and receive SMS messages, and take advantage of its advanced messaging features, such as message templates and scheduled messaging.

## Navixy JSON configuration for Vonage

```json5
owner_id: 1
 label: <any_label_here>
 type: transceiver
 provider: nexmo
 params:
 {
 "api_key": <your_api_key> # find it in the settings of your Nexmo account
 "api_secret": <your_api_secret> # find it in the settings of your Nexmo account
 }
 enabled: 1
 class_filter: *
```
