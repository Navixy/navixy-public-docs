---
title: Set up driver identification
description: "Configure the tracker for RFID, iButton, or BLE data; populate the driver list in Field Service and map each driver to a hardware key"
---

# Set up driver identification

## Question

How to use and set up driver identification?

## Answer

The first step in setting up the driver identification system is to configure the tracker so that it sends RFID/iButton/BLE identification data. Different manufacturers provide different tools for configuring identification. Contact your tracker manufacturer to enable sending identification data to the platform. Once configured, you should be able to see the identification data in Air Console as soon as it is triggered by an RFID card or other identification medium.

Once the tracker is configured, you can proceed with setting up the platform. The main task is to populate the list of drivers in the Field Service section and specify the hardware keys in the drivers' records:

![](<../.gitbook/assets/Unknown image (115)>)

A hardware key is usually a HEX code text string. Enter a hardware key for each driver in the list.

You can also use import to bulk import drivers with predefined hardware keys and other properties.

Once the driver list is ready, driver identification starts working automatically. Upon authorization on the tracker side, the tracker sends a hardware key to the platform, and the platform recognizes it and assigns a certain driver from the list to the tracker. Now you should be able to build reports and track the driver assignment status in the Tracking module (tracker widgets).

## Links

* [Drivers](https://app.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/s/446mKak1zDrGv70ahuYZ/~/edit/~/changes/106/guide/fleet-management/drivers)