---
description: >-
  How to find a device, rename it, change its phone number or APN, and
  deactivate it.
tags:
  - how-to
  - beta
---

# Manage devices

{% hint style="info" %}
**Access**

Each task on this page needs a role that allows it. For example, to rename a device, your role must let you view and edit devices. See [Access to Device Management](README.md#access-to-device-management).
{% endhint %}

## Find a device

The **Devices** list of Device Management shows every device of your workspace. Search finds a device by its name, or by its device identifier when you enter only digits.

1. Go to **Device Management**.
2. In the search field, enter the name of the device or its IMEI.
3. Click the device.
   The device page opens.

If nothing matches, the list shows "No devices match your search."

## Rename a device

1. In **Device Management**, click the device.
2. Click **Rename**.
3. In **Name**, enter the new name.
4. Click **Save**.

## Change the phone number or the APN

You enter the phone number of the SIM card and the APN when you activate a device. While the device hasn't connected yet, **Retry activation** lets you correct them and sends the configuration SMS again. See [If the device doesn't connect](activate-device.md#if-the-device-doesnt-connect).

After the device has connected, Navixy can't change its phone number or its APN. When you replace the SIM card of a connected device, set the new APN on the device itself, for example in the configuration tool of the manufacturer. Navixy sends commands to a connected device over its mobile data connection, not by SMS, so it doesn't use the stored phone number again. The stored number stays reserved: no other device can be activated with it until you deactivate this device.

## Deactivate a device

Deactivate a device when you stop using it, for example when you take it out of a vehicle for good. A deactivated device is removed from the device list and stops reporting to the platform. Its phone number becomes available for another device.

1. In **Device Management**, click the device.
2. In **Device deactivation**, click **Deactivate**.
3. Turn on **I understand that this action can't be undone**.
4. Click **Deactivate**.

You can't undo a deactivation. If the device is assigned to an asset, Navixy removes it from the asset.

You can't deactivate a device that an IoT Logic flow uses, even a disabled flow. Remove the device from every **Data Source** node that has it, or delete the flow, and then deactivate the device. If you try while a flow still uses the device, deactivation fails with the message "Failed to load the data." The same applies when the device is in a required device field of an asset or a geo object: assign another device to the field first. A device in an optional field doesn't block the deactivation: Navixy clears that field.

{% hint style="warning" %}
After you deactivate a device, Navixy keeps its IMEI in use. You can't add or activate a device with this IMEI again, in this workspace or in any other. To move a device to another vehicle, don't deactivate it: [assign it to the other asset](../bdr/assets/manage-assets.md#assign-a-device-to-an-asset).
{% endhint %}

To stop a device that is still waiting to connect, cancel its activation instead. See [If the device doesn't connect](activate-device.md#if-the-device-doesnt-connect).

## Assign a device to an asset

You assign a device to an asset in Business Data Repository, from the asset. See [Assign a device to an asset](../bdr/assets/manage-assets.md#assign-a-device-to-an-asset).

To activate a new device and assign it to an asset in one step, start the activation from the asset. See [Start from an asset](activate-device.md#start-from-an-asset).
