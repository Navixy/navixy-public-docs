---
description: >-
  Two technologies that developers build against: the Navixy Generic Protocol,
  for sending data from your own devices and systems to the Navixy tracking
  servers, and the expression language that IoT Logic flows calculate with.
tags:
  - overview
noIndex: true
---

# Technologies

This space documents technologies that developers build against, rather than any one product's interface. A technology lands here when more than one product uses it, or when the people who need it are building against Navixy rather than working in it.

It is written for developers: firmware engineers connecting a device, integrators moving data between systems, and anyone writing the formulas inside an IoT Logic flow. It assumes you can read JSON and a code example, and it does not explain the platform's interface. For that, start with the product documentation.

## What is here

* [Navixy Generic Protocol](navixy-generic-protocol/overview.md): an open, JSON-based protocol for sending telematics data to the Navixy tracking servers over HTTP/HTTPS or MQTT, from your own devices or from another system. Covers the transport layer, message structure, data types, and the standard event identifiers. The GPS devices that you activate in Location Intelligence don't use it: they connect through [Device Management](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/device-management/activate-device), with the protocol of their manufacturer.
* [Navixy IoT Logic Expression Language](navixy-iot-logic-expression-language/overview.md): the full syntax of the formulas IoT Logic evaluates, with the operator and function tables and a reference of every formula error.

## Related documentation

To write formulas in the platform's own interface, see [IoT Logic](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/iot-logic) in the Location Intelligence documentation. To connect a GPS device, see [Activate a device](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/device-management/activate-device).
