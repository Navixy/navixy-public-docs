---
description: >-
  Device Management is where you connect GPS devices to Navixy: activate a
  device so it starts to send data, and see every device of your workspace in
  one list.
tags:
  - overview
  - beta
noIndex: true
---

# Device Management

A GPS device you install in a vehicle sends nothing to your workspace until Navixy knows the device. Device Management is where you connect devices to Navixy: you activate each device once, and Navixy configures it and starts to accept its data.

## What you can do here

**Connect a device in one form.** You choose the model and enter the phone number of the SIM card, the APN of the mobile carrier, and the IMEI of the device. Navixy sends the device an SMS with its settings, so you don't configure the device by hand.

**Follow each activation until the device connects.** The device page shows the progress of the activation. If the device doesn't connect, the page lists the possible causes and lets you send the configuration SMS again.

**See every device in one list.** The list shows the model, the device identifier, and the phone number of each device, and when it last sent data. Search finds a device by its name or by its IMEI.

**Add a device before you install it.** Add a device from the asset that it will belong to, and activate it when you install it. Activation finds the device you added by its IMEI, so the device isn't added twice.

## Who it is for

Device Management is for the people who install and look after GPS devices: installers, fleet managers, and the support staff of a telematics service provider. It is part of [Business Data Repository](../bdr/README.md), so every workspace that has Business Data Repository has Device Management. Activate a device before you assign it to an asset in Business Data Repository. An asset shows on the map in [Tracking](../tracking/README.md) only when its primary device sends data.

## Core concepts

A device is the hardware. The thing the device is fitted to, such as a vehicle, is an asset. You link the two in Business Data Repository.

* **Device:** a piece of tracking hardware, such as a GPS device, a sensor, or a beacon.
* **Device model:** the product of one vendor, such as Teltonika FMB920. The model decides which settings Navixy sends to the device during activation.
* **Device identifier:** the number that the device reports to Navixy, printed on its label. For a GPS device with a SIM card, the identifier is the IMEI, a 15-digit number. Two devices can't have the same identifier, because Navixy uses it to recognize which device sent the data.
* **Activation:** connecting a device to Navixy. Navixy stores the SIM card details, sends the configuration SMS, and waits for the first data from the device. You activate a device once.

## How device data reaches Navixy

A device connects to Navixy over mobile data and reports its IMEI. Navixy accepts data only from devices it knows, and refuses data from an unknown device. Navixy then processes the data in the [IoT Logic](../iot-logic/README.md) flows that the device is in. Navixy also shows the data on the asset that the device is assigned to.

A device decides on its own when to send a position, for example after a set time, a set distance, or a change of direction. Many devices also have sleep or power-saving modes, in which they turn off satellite positioning or the mobile data connection between reports. These settings are in the configuration of the device, not in Navixy. When Navixy activates a device by SMS, it sets the device to report every 60 seconds while moving and every 180 seconds while stopped.

Navixy reads the time in a message as UTC and doesn't adjust it for each device. Keep the clock of the device on UTC: a device set to local time shows its positions and readings shifted by its time zone offset. The configuration SMS doesn't set the clock of the device.

A device can keep data in its internal memory, for example while it has no coverage, and send it later. Navixy accepts this stored data, whatever its age. The first data that arrives activates the device, even when that data is old.

## Access to Device Management

Device Management follows your access to devices and inventories in [Business Data Repository](../bdr/README.md#access-to-business-data-repository). It has no access settings of its own.

* The device list shows only the devices that your role lets you see.
* To activate a device, your role must let you create devices. If the device is already in your workspace, for example added without activation, your role must also let you view and edit it.
* To change a device, such as renaming it or changing its model, your role must let you view and edit devices.

**Activate device** and the other actions are shown to everyone. If your role doesn't allow the action, an error message appears when you try it. To learn more, see [Access and roles](../access-and-roles.md).

## Where to start

Start with [Activate a device](activate-device.md). It takes you from a GPS device with a SIM card to a device that sends data to your workspace.

## Section contents

* [Activate a device](activate-device.md): Choose the model, enter the SIM card and IMEI details, wait for the device to connect, and retry if it doesn't.
* [Manage devices](manage-devices.md): Find a device, add one without activating it, rename it or change its model, and deactivate it.
