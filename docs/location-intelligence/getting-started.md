---
description: >-
  Activate your first GPS device, create the vehicle that it tracks, assign the
  device to the vehicle, and see the vehicle on the map.
tags:
  - guide
  - beta
noIndex: true
---

# Getting started

This guide takes you from a GPS device in a box to a vehicle that you can follow on the map. When you complete it, your workspace will have one activated device, one vehicle, and a link between the two. The map will show the device's position as the vehicle's position.

The guide is for the person who sets up Navixy for their fleet: a fleet manager, an installer, or an administrator. It follows the simplest path through Device Management and Assets.

## Prerequisites

Before you start, make sure that you have the following:

* Access to a workspace where you can open **Assets** and **Device Management**
* A role that lets you create devices and assets in that workspace, as described in [Access and roles](access-and-roles.md)
* A GPS device of a supported model, with a SIM card installed and the device powered on
* The phone number of the SIM card, the access point name (APN) of its mobile carrier, and the IMEI of the device

The SIM card needs mobile data and must be able to receive SMS. If you don't know the APN, the mobile carrier publishes it, usually on its website. The IMEI is printed on the device and on its box.

## How to get started with Location Intelligence

### 1. Activate the GPS device

Activation connects the device to Navixy. You select the device model and enter the SIM card details and the IMEI. Navixy then sends the device an SMS with its settings, so you don't configure the device by hand. Activate each device once, when you install it.

To activate the device, follow these steps:

1. Go to **Device Management** and click **Activate device**. The **Activate device** page opens on the **Model** step.
2. In the search field, enter the model name or the vendor name, for example, FMB920.
3. Open the vendor group, select the model, and click **Next**. The **Details** step opens.
4. (Optional) In **Device name (optional)**, enter a name for the device. Without a name, Navixy names the device after its model and IMEI.
5. In **Phone number**, enter the phone number of the SIM card, with the country code. The number has 5 to 15 digits. You can type it with spaces, brackets, dashes, or a plus sign, for example, +1 (201) 555-0123: the form keeps only the digits.
6. Expand **GPRS settings** and enter the mobile carrier's **APN**. If the carrier uses them, also enter the **APN username (optional)** and the **APN password (optional)**.
7. In **IMEI**, enter the 15-digit IMEI of the device.
8. (Optional) In **Activation code (optional)**, enter the activation code, if you have one.
9. Click **Activate**. The device page will open and show the device as **Waiting to connect**.

If the workspace has no devices yet, the **Devices** list will show an **Activate device** button, which opens the same page. If no model matches your search, check the model name on the device label or on its box. If a field is missing or has the wrong format, the form shows the error under the field and doesn't activate the device.

Now you have a device in the **Devices** list of Device Management. Navixy sends the configuration SMS to the phone number that you entered, and the device page shows the activation progress. When the first data arrives, **Waiting to connect** disappears, and **Last activity** shows when the device sent its data. The SMS usually arrives in 1–2 minutes, so you can continue with the vehicle while the device connects.

{% hint style="warning" %}
If Navixy refuses the activation because the phone number or the device ID is already in use, the SIM card or the device is already in a workspace. Check the phone number and the IMEI.

If the device sends no data within 2 minutes, its page says that the device hasn't connected and lists the possible causes. Fix the causes that apply and click **Retry activation**. For details, see [If the device doesn't connect](device-management/activate-device.md#if-the-device-doesnt-connect).
{% endhint %}

### 2. Create the vehicle and assign the device to it

A GPS device reports its position but doesn't know whether it's installed in a van or who drives that van. In **Assets**, you describe the van as an asset: something that your business tracks, such as a vehicle, a person, or a container. The asset uses the position of the device assigned to it, so the map shows the van, not the device. A device can be assigned to one asset at a time, though an asset can have several devices.

Every asset is based on an asset type, which is a template that decides which properties the asset has. Your workspace starts with three preset asset types: **Vehicle** for cars, vans, trucks, and equipment, **Workforce** for the people you track, and **Cargo** for containers, pallets, and parcels. This guide uses **Vehicle**. To build an asset type of your own, see [Create asset](bdr/assets/create-asset.md).

To create the vehicle and assign the device to it, follow these steps:

1. Click **Assets** on the sidebar.
2. Click the **Vehicle** tab.
3. Click **Create asset**. A panel titled **Vehicle** opens.
4. Enter a **Name**, for example, the vehicle's fleet number or its license plate.
5. In the **Vehicle kind** field, select the kind that fits, such as Van or Truck.
6. (Optional) Enter the **License plate** and any other details that you have.
7. In the **Device** field, select the device that you activated.
8. Check that **Primary device** is turned on. If you have only one device selected, Navixy turns it on automatically. The primary device is the one whose position is displayed as the vehicle's position. A vehicle without a primary device won't appear on the map.
9. Click **Save**.

The vehicle appears in the **Vehicle** tab of the **Assets** page with the device assigned to it. When you move the device to another vehicle later, you assign it in that vehicle's **Device** field, and Navixy transfers it. If the device already belongs to another asset, Navixy will ask you to confirm its transfer.

### 3. View the vehicle on the map

Go to **Tracking**. The vehicle is listed under **Assets** and shown on the map at the last position that its device reported. If the device hasn't connected yet, the vehicle will show on the map as soon as the first data package arrives.

Add every other vehicle the same way: activate its device, then create the vehicle and assign the device to it.

{% hint style="success" %}
You activated a GPS device, created a vehicle, and assigned the device to it. The position that the device reports is now the vehicle's position, and your team can follow the vehicle in Tracking.
{% endhint %}

## Next steps

* [Activate a device](device-management/activate-device.md): Activate the next device and find what to do when a device doesn't connect.
* [Create asset](bdr/assets/create-asset.md): Build an asset type of your own when the preset types don't fit what you track.
* [Manage assets](bdr/assets/manage-assets.md): Reassign a device, group assets, and add a custom field to an asset type.
* [Manage devices](device-management/manage-devices.md): Find a device, rename it, and deactivate it.
* [Assets](bdr/assets/): Learn how assets, asset types, and custom fields work.
* [Device Management](device-management/): Learn how devices, models, and activation work.
* [Access and roles](access-and-roles.md): Learn what your role lets you do in each product, and what to do when something isn't available.
