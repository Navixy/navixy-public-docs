---
title: Send data from a non-integrated device (Navixy Generic Protocol)
description: "Convert device data to Navixy JSON format and send it over HTTP/HTTPS using the Navixy Generic Protocol for non-integrated hardware"
---

# Send data from a non-integrated device (Navixy Generic Protocol)

## Question

How can I send data to the platform from a non-integrated device?

## Answer

If your specific device isn't integrated with the Navixy platform, you can use our Navixy Generic Protocol to do so. This is a universal protocol allowing you to send the specific data you need to our platform without waiting or paying for the costs associated with device integration. Once you have your device data, you will need to "convert" it to Navixy's accepted JSON format as shown in the documentation. Then all you have to do is send the data over HTTP/HTTPS to our platform and your device data will display.

The minimum required JSON is as follows:

| { "message\_time": "...", "device\_id": "..." } |
| ----------------------------------------------- |

## Links

* [Navixy Generic Protocol](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-generic-protocol)
* [Message structure and attributes](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-generic-protocol/message-structure-and-attributes)
