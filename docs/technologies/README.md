---
description: >-
  This space documents Navixy Generic Protocol, for sending device data, and
  Navixy IoT Logic Expression Language, for formulas in IoT Logic flows.
tags:
  - overview
---

# Technologies

This space documents technologies that developers build with: a protocol for device data and a formula language. A technology is documented here when more than one Navixy product uses it.

The space is for developers: firmware engineers, integrators who move data between systems, and anyone who writes formulas in an IoT Logic flow. It assumes that you can read JSON and a code example. It doesn't explain the Navixy platform's interface. For the interface, start with the [Location Intelligence documentation](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/).

## Section contents

The space has the following sections:

* [Navixy Generic Protocol](navixy-generic-protocol/overview.md): An open protocol for sending telematics data to Navixy from your own devices or systems. In Navixy Generic Protocol (NGP) 4.0, your device or server sends Protocol Buffers (protobuf) packets over TCP. Navixy saves the data for devices that you activate in Location Intelligence with the **NGP device** model. The section has guides, a migration page for NGP 1.2 senders, and the protocol specification.
* [Navixy IoT Logic Expression Language](navixy-iot-logic-expression-language/overview.md): The full syntax of IoT Logic formulas, with operator and function tables and all formula errors.

## See also

* [IoT Logic](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic): Write formulas in the IoT Logic interface of Location Intelligence.
* [Activate a device](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/device-management/activate-device#enter-the-details-of-an-ngp-device): Connect a GPS device, including an NGP device.
