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

Each task on this page needs a role that allows it. For example, to rename a device, your role must let you view and edit devices. See [Access to Device management](README.md#access-to-device-management).
{% endhint %}

## Find a device

The **Devices** list in Device management shows every device in your workspace. Search finds a device by its name, its device identifier, or the phone number of its SIM card. You can enter a phone number with spaces, brackets, dashes, or a plus sign.

1. Go to **Device management**.
2. In the search field, enter the name of the device, its IMEI, or its phone number.
3. Click the device.
   The device page opens.

If nothing matches, the list shows "No devices match your search."

Some devices are added without activation, for example through the Navixy GraphQL API. Such a device shows **Not activated** next to its name on the device page. It was never activated, with either setup, but it can still send data if someone configured it by hand. Its page offers **Rename** only. To activate it, start an activation with its IMEI and click **Update device**: see [If the device is already on the platform](activate-device.md#if-the-device-is-already-on-the-platform).

## Rename a device

1. In **Device management**, click the device.
2. Click **Rename**.
3. In **Name**, enter the new name.
4. Click **Save**.

## Change the phone number or the APN

With automatic setup, you enter the phone number of the SIM card and the APN when you activate a device. If the device sends no data within 2 minutes, you can correct them with **Retry activation**, and Navixy sends the configuration SMS again. To correct them sooner, activate the device again with the same IMEI. See [If the device doesn't connect](activate-device.md#if-the-device-doesnt-connect).

With manual setup, Navixy stores no phone number and no APN for the device. You set the APN on the device itself, together with the server address and the port.

After a device with automatic setup has connected, Navixy can't change its phone number or its APN. When you replace the SIM card in a connected device, set the new APN on the device itself, for example in the configuration tool of the manufacturer. Navixy sends commands to a connected device over its mobile data connection, not by SMS, so it doesn't use the stored phone number again. The stored number stays reserved: no other device can be activated with it until you deactivate this device.

## Deactivate a device

Deactivate a device when you stop using it, for example when you take it out of a vehicle permanently. A deactivated device is removed from the device list and stops reporting to the Navixy platform. Its phone number becomes available for another device.

**Deactivate** shows on the page of a device that was activated and isn't awaiting connection.

1. In **Device management**, click the device.
2. In **Device deactivation**, click **Deactivate**.
3. Select the **I understand that this action can't be undone** check box.
4. Click **Deactivate**.

You can't undo a deactivation. If the device is assigned to an asset, Navixy removes it from the asset.

You can't deactivate a device that an IoT Logic flow uses, even a disabled flow. Remove the device from every **Data Source** node that has it, or delete the flow, and then deactivate the device. If you try while a flow still uses the device, deactivation fails with the message "Failed to complete the action. Try again." The same applies when the device is in a required device field of an asset or a geo object: assign another device to the field first. A device in an optional field doesn't block the deactivation: Navixy clears that field.

{% hint style="warning" %}
After you deactivate a device, Navixy keeps its IMEI in use. You can't add or activate a device with this IMEI again, in this workspace or in any other. To move a device to another vehicle, don't deactivate it: [assign it to the other asset](../bdr/assets/manage-assets.md#assign-a-device-to-an-asset).
{% endhint %}

To remove a device that is still awaiting connection, cancel its activation instead. See [Cancel the activation](activate-device.md#cancel-the-activation).

## Assign a device to an asset

You assign a device to an asset in Business Data Repository, from the asset. See [Assign a device to an asset](../bdr/assets/manage-assets.md#assign-a-device-to-an-asset).

To activate a new device and assign it to an asset in one step, start the activation from the asset. See [Start from an asset](activate-device.md#start-from-an-asset).
