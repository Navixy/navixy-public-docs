---
description: Connect any SMPP-compatible SMS center to Navixy on-premise using JSON gateway configuration. Covers minimal and complete parameter sets for SMPP protocol setup.
---

# SMPP

[SMPP](https://en.wikipedia.org/wiki/Short_Message_Peer-to-Peer) (or Short Message Peer-to-Peer) is a protocol used for exchanging SMS messages between Short Message Service Centers (SMSCs) and applications. It enables fast, reliable delivery of SMS messages and provides a range of features for managing message traffic and monitoring delivery status.

Navixy supports SMPP v. 3.4 as a means of sending and receiving SMS messages. With SMPP, Navixy can handle large volumes of SMS traffic and offer advanced features such as message batching, message status tracking, and more.

By configuring an SMS gateway in the **'sms\_gates'** table of the **'google'** database, you can connect Navixy to an external SMS center and utilize its messaging capabilities within Navixy platform.

## Navixy JSON configuration for SMPP connection

The minimal Navixy JSON configuration for SMPP gateway:

```json5
owner_id: 1
 label: <any_label_here>
 type: transceiver
 provider: smpp
 params:
 {
 "addresses": ["smpp.server.com:1234"] # SMPP server address and port
 "login": <login>
 "password": <password>
 }
 enabled: 1
 class_filter: *
```

A more complete JSON configuration for an SMS gateway might include the following parameters:

```json5
{
 "addresses": null, //mandatory, array of server addresses, ["smpp.server.com:1234"]
 "login": null, //mandatory, systemId
 "password": null, // mandatory, max 8 chars
 "default_charset": "GSM8", // possible values: GSM8, GSM7 (packed to 7bit GSM8), ISO-8859-1, ISO-8859-15, UTF-8
 "source_ton": -1, // -1 means auto, if sourceAddress is alphanumeric alnum_ton/alnum_npi will be used as source_ton/source_npi otherwise num_ton/num_npi
 "source_npi": 1,
 "dest_ton": 1,
 "dest_npi": 1,
 "alnum_ton": 5,
 "alnum_npi": 0,
 "num_ton": 1,
 "num_npi": 1,
 "override_originator": null, //null means no override
 "long_sms_transmit_method": "udh", //udh or payload
 "interface_version": null, //v33 or v34, null means auto negotiation
 "default_coding": 0,
 "binary_coding": 4,
 "unicode_coding": 8,
 "null_padded_octet_strings": false, //explicitly add null to short_message octet string
 "connect_timeout": 10000,
 "bind_timeout": 10000,
 "request_expiry_timeout": 15000, //MUST be greater than submit_timeout because we use sync submit for now
 "enquire_link_timeout": 3000,
 "enquire_link_interval": 15000,
 "submit_timeout": 10000, //MUST be less than request_expiry_timeout because we use sync submit for now
 "reconnect_wait": 15000,
 "log_pdu": false,
 "log_bytes": false,
 "window_size": 10,
 "system_type": null,
 "registered_delivery_receipt_request": true,
 "support_binary": true
 }
 provider: smpp
 type: transceiver
```

To set up Navixy for SMS messaging, you'll need to obtain all the necessary data from your SMS provider, including passwords and other required information. Be sure to check the comments in the 'sms\_gates' table for additional guidance, indicated by two slashes '//', as they may provide helpful tips for configuration.

With the proper setup and configuration, SMPP can be a straightforward way to manage SMS messaging within Navixy. If you have any questions or concerns during the process, don't hesitate to consult the Navixy documentation or reach out to their support team for assistance.
