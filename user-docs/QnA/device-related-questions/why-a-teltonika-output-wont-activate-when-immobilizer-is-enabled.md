# Why a Teltonika Output Won’t Activate When Immobilizer Is Enabled

### Question

Why can’t I turn on a Teltonika digital output from the platform when that output is assigned to the Immobilizer scenario?

### Answer

Teltonika Immobilizer is not a platform switch. It is a device-level scenario that takes full control of the assigned digital output (DOUT). That is why a GPRS command from Navixy platform often looks successful on the platform side, but the output never changes.

#### What Immobilizer actually does

On Teltonika devices, Immobilizer is designed to stop anyone from starting the vehicle unless the tracker itself authorizes the driver. Authorization happens locally, not in the cloud. Typical methods are:

* a 1-Wire iButton or RFID card
* a configured BLE beacon

The assigned DOUT is the actuator. According to Teltonika’s own logic:

1. When the Immobilizer is active, the selected DOUT goes high (1) and blocks the starter, depending on which output the device has been configured to use.
2. The tracker waits for a valid key, RFID, or BLE beacon.
3. Only after that local authorization does the DOUT go low (0) and allow the engine to start.
4. After ignition is turned off and the Ignition Off timeout expires, the scenario resets. The next start requires authorization again.

If Depend on Ignition is enabled, the output stays low until ignition is detected, then it goes high and waits for the key or beacon. If that option is disabled, Immobilizer can take the output immediately after the scenario is enabled.

**In both cases, the decision lives inside the tracker.**

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

#### Why a platform DOUT command is not enough

Navixy already has standard commands to control physical outputs. For Teltonika, that is typically something like `setdigout`. Those commands work when the output is a normal digital output.

They do not complete Immobilizer authorization.

A standard output command only says: _set this pin high or low_. Immobilizer requires something different: _has the configured key or beacon been detected by this device?_ If the tracker accepted a remote DOUT command as a valid release, anyone with platform access could bypass the hardware authorization step. That would defeat the purpose of Immobilizer.

This is also why Teltonika treats Immobilizer as the highest-priority DOUT scenario. Officially, Immobilizer cannot share the same output with other features because it takes full control of that DOUT. Other commands, including platform output control, can be ignored while the scenario owns the pin.

Navixy documents the same idea at platform level: some manufacturers implement scenario-based omission. The platform can send the command, but the device decides whether to apply it.

