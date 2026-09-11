# Configuring COBAN Family Devices to Receive GPRS Commands

### Question

How can I configure a COBAN family device to receive GPRS commands?

### Answer

COBAN family devices commonly support different commands for controlling their outputs depending on whether the command is sent via SMS or GPRS. Therefore, even if an output command such as stop123456 works correctly via SMS, the equivalent GPRS command may use a different format.

Before using the GPRS commands, the device must be configured to use the appropriate protocol. According to the COBAN protocol documentation, this can be done by sending the following command via SMS:

**protocol123456 18 out**

The documentation specifically recommends configuring Protocol 18 before integrating the device with a server.

If the change is successfully applied, the device should return a confirmation similar to:

_**change protocol sucess**_

Once Protocol 18 is enabled and the device is connected to the platform, the documented GPRS commands for controlling the oil/power output can be used.

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Activate the output / Cut off oil and power:

**\*\*,imei:\<IMEI>,109**

The device should respond with 109 when the command is successfully accepted. The protocol also defines 509 for the execution of the cut-off when the vehicle speed is below 20 km/h.

Deactivate the output / Resume oil and power:

**\*\*,imei:\<IMEI>,110**

A successful command should generate a response containing keyword 110.

In summary: if a COBAN family device responds correctly to output commands via SMS but not through the platform, verify that Protocol 18 has been configured first. After receiving confirmation of the protocol change, commands 109 and 110 can be tested through the active GPRS connection for output cut-off and restoration, respectively.

<br>
