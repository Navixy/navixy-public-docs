---
description: >-
  Connect GPS devices to Navixy: activate a device by SMS or by setting it up
  yourself, follow it until it connects, and see all your devices in one list.
tags:
  - overview
  - beta
noIndex: true
---

# Device management

A GPS device that you install in a vehicle sends nothing to your workspace until Navixy knows the device. Device management is where you connect devices to Navixy: you activate each device once, and Navixy starts to accept its data.

## What you can do here

**Connect a device in one form, by SMS or by hand.** You choose the model and enter the IMEI of the device. With automatic setup, you also enter the phone number of the SIM card and the APN of the mobile carrier. Navixy then sends the device an SMS with its settings. With manual setup, Navixy shows the server address and the port, and you set them on the device yourself, together with the APN.

**Follow each activation until the device connects.** The device page shows the activation progress, and the device list marks each device that is still awaiting connection. If the device doesn't connect, the page lists the possible causes. For a device with automatic setup, you can also send the configuration SMS again from the page.

**See every device in one list.** For each device, the list shows the model, the device identifier, the phone number, and the time when the device last sent data. Search finds a device by its name, its IMEI, or the phone number of its SIM card.

**Activate a device from its asset.** Start the activation from the **Device** field of the asset that the device will belong to. You activate the device and assign it to the asset in one step.

## Who it is for

Device management is for the people who install and maintain GPS devices: installers, fleet managers, and the support staff of a telematics service provider. It is part of [Business Data Repository](../bdr/README.md), so every workspace that has Business Data Repository has Device management. Activate a device before you assign it to an asset in Business Data Repository, or activate it from the asset itself. An asset shows on the map in [Tracking](../tracking/README.md) only when its primary device sends data.

## Core concepts

A device is the hardware. The thing that the device is fitted to, such as a vehicle, is an asset. You link the two in Business Data Repository.

* **Device:** a piece of tracking hardware, such as a GPS device, a sensor, or a beacon.
* **Device model:** the product of one vendor, such as Teltonika FMB920. The model defines how Navixy reads the data of the device and which settings the configuration SMS sends.
* **Device identifier:** the number that the device reports to Navixy, printed on its label. For a GPS device with a SIM card, the identifier is the IMEI, a 15-digit number. Two devices can't have the same identifier, because Navixy uses it to recognize which device sent the data.
* **Activation:** connecting a device to Navixy. Navixy registers the device and waits for its first data. The device gets its settings in one of two ways. With automatic setup, Navixy sends them in a configuration SMS. With manual setup, you set them on the device. You activate a device once.

## How device data reaches Navixy

A device connects to Navixy over mobile data and reports its IMEI. Navixy accepts data only from devices that it knows and refuses data from an unknown device. Navixy then processes the data in the [IoT Logic](../iot-logic/README.md) flows that the device is in. Navixy also shows the data on the asset that the device is assigned to.

A device decides on its own when to send a position, for example after a set time, a set distance, or a change of direction. Many devices also have sleep or power-saving modes, in which they turn off satellite positioning or the mobile data connection between reports. These settings are in the configuration of the device, not in Navixy. When Navixy activates a Teltonika device by SMS, it sets most models to report every 60 seconds while moving and every 180 seconds while stopped. With manual setup, Navixy doesn't change these settings.

Navixy reads the time in a message as UTC and doesn't adjust it for each device. Keep the clock of the device on UTC: a device set to local time shows its positions and readings shifted by its time zone offset. The configuration SMS doesn't set the clock of the device.

A device can keep data in its internal memory, for example while it has no coverage, and send it later. Navixy accepts this stored data, whatever its age. The first data that arrives completes the activation, even when that data is old.

## Access to Device management

Device management follows your access to devices and inventories in [Business Data Repository](../bdr/README.md#access-to-business-data-repository). It has no access settings of its own.

* The device list shows only the devices that your role lets you see.
* To activate a device, your role must let you create devices. If the device is already in your workspace and isn't activated yet, your role must also let you view and edit it.
* To change a device, such as renaming it, your role must let you view and edit devices.

Everyone sees **Activate device** and the other actions. If your role doesn't allow the action, an error message appears when you try it. To learn more, see [Access and roles](../access-and-roles.md).

## Where to start

Start with [Activate a device](activate-device.md). It takes you from a GPS device with a SIM card to a device that sends data to your workspace.

## Section contents

* [Activate a device](activate-device.md): Choose automatic or manual setup, select the model, enter the device details, and wait for the device to connect.
* [Manage devices](manage-devices.md): Find a device, rename it, change its phone number or APN, and deactivate it.
