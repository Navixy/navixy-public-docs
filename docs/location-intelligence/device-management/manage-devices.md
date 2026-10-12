---
description: >-
  How to find a device, add a device to an inventory without activating it,
  rename a device or change its model, move it to another inventory, delete or
  deactivate it, and create and manage inventories.
tags:
  - how-to
  - beta
---

# Manage devices

{% hint style="info" %}
**Access**

Each task on this page needs a role that allows it. For example, to rename a device, your role must let you view and edit devices. Inventories have access settings of their own. See [Access to Device Management](README.md#access-to-device-management).
{% endhint %}

## Find a device

The **Devices** list of Device Management shows every device of your workspace. Search finds a device by its name, or by its device identifier when you enter only digits.

1. Go to **Device Management**.
2. In the search field, enter the name of the device or its IMEI.
3. Click the device.
   The device page opens.

If nothing matches, the list shows "No devices match your search."

## Add a device without activating it

Add a device to an inventory when you have the device but haven't installed it yet. For example, add the devices of a batch that you receive from the supplier. When you install the device later, [activate it](activate-device.md): activation finds the device by its IMEI and doesn't add it a second time.

Navixy sends a device added this way no settings until you activate it. If the device is already configured, for example by its supplier, Navixy accepts its data before activation.

1. Go to **Business Data Repository** > **Devices**.
2. Click **Add device**.
   The **Add device to inventory** panel opens.
3. In **Model**, select the device model.
4. Optional: in **Name (optional)**, enter a name. If you leave it empty, Navixy names the device after its vendor, model, and IMEI, for example `Teltonika FMB920 356938035643809`.
5. In **Inventory**, select the inventory. To create one, click **+** next to the field.
6. In **Device type**, select the type of the device.
7. In **IMEI**, enter the 15-digit IMEI of the device.
8. Click **Save**.

The device appears in the list with the status **Not activated**.

## Rename a device

1. In **Device Management**, click the device.
2. Click **Rename**.
3. In **Name**, enter the new name.
4. Click **Save**.

You can also rename a device in Business Data Repository, in the **Edit device** panel. See [Change the model of a device](#change-the-model-of-a-device).

## Change the model of a device

1. Go to **Business Data Repository** > **Devices**.
2. Point to the device and click the edit icon.
   The **Edit device** panel opens.
3. In **Model**, select the model. To rename the device at the same time, change the **Name (optional)**.
4. Click **Save**.

## Change the phone number or the APN

You enter the phone number of the SIM card and the APN when you activate a device. While the device hasn't connected yet, **Retry activation** lets you correct them and sends the configuration SMS again. See [If the device doesn't connect](activate-device.md#if-the-device-doesnt-connect).

After the device has connected, Navixy can't change its phone number or its APN. When you replace the SIM card of a connected device, set the new APN on the device itself, for example in the configuration tool of the manufacturer. Navixy sends commands to a connected device over its mobile data connection, not by SMS, so it doesn't use the stored phone number again. The stored number stays reserved: no other device can be activated with it until you deactivate or delete this device.

## Move a device to another inventory

1. Go to **Business Data Repository** > **Devices**.
2. Point to the device and click the edit icon.
3. In **Inventory**, select the new inventory.
4. Click **Save**.

To see the inventory of each device, select **Inventory** in **Group by**. Devices that aren't in an inventory are listed under **Without inventory**.

## Deactivate a device

Deactivate a device when you stop using it, for example when you take it out of a vehicle for good. A deactivated device is removed from the device list and stops reporting to the platform. Its phone number becomes available for another device.

1. In **Device Management**, click the device.
2. In **Device deactivation**, click **Deactivate**.
3. Turn on **I understand that this action can't be undone**.
4. Click **Deactivate**.

You can't undo a deactivation. If the device is assigned to an asset, Navixy removes it from the asset.

You can't deactivate a device that an IoT Logic flow uses. Remove the device from every **Data Source** node that has it, or delete the flow, and then deactivate the device. If you try while a flow still uses the device, deactivation fails with the message "Failed to load the data." The same applies when the device is in a required device field of an asset or a geo object: assign another device to the field first.

{% hint style="warning" %}
After you deactivate a device, Navixy keeps its IMEI in use. You can't add or activate a device with this IMEI again, in this workspace or in any other. To move a device to another vehicle, don't deactivate it: [assign it to the other asset](../bdr/assets/manage-assets.md#assign-a-device-to-an-asset).
{% endhint %}

To stop a device that is still waiting to connect, cancel its activation instead. See [If the device doesn't connect](activate-device.md#if-the-device-doesnt-connect).

## Delete a device

1. Go to **Business Data Repository** > **Devices**.
2. Point to the device and click the delete icon.
3. In the **Delete device** window, click **Delete**.

You can't restore a deleted device. If the device is assigned to an asset or a geo object, Navixy removes it from there.

You can't delete a device that an IoT Logic flow uses. Remove the device from every **Data Source** node that has it, or delete the flow, and then delete the device. If you try while a flow still uses the device, the message says that the device is still referenced by an IoT Logic Flow. The same applies when the device is in a required device field of an asset or a geo object: assign another device to the field first.

{% hint style="warning" %}
After you delete a device, Navixy keeps its IMEI in use. You can't add or activate a device with this IMEI again, in this workspace or in any other.
{% endhint %}

## Create an inventory

An inventory is a stock location for devices, such as a warehouse or a branch.

1. Go to **Business Data Repository** > **Devices**.
2. Open the **Inventories** tab.
3. Click **Create inventory**.
4. In **Name**, enter the name of the inventory.
5. Click **Create**.

You can also create an inventory from the **Inventory** field when you add a device.

## Rename or delete an inventory

1. Go to **Business Data Repository** > **Devices**.
2. Open the **Inventories** tab.
3. Point to the inventory and click the edit icon or the delete icon.
4. To rename the inventory, enter the new **Name** and click **Save**.
5. To delete the inventory, click **Delete** in the **Delete inventory** window.

You can delete only an inventory that has no devices. To empty an inventory, [move its devices to another inventory](#move-a-device-to-another-inventory). You can't restore a deleted inventory.

## Assign a device to an asset

You assign a device to an asset in Business Data Repository, from the asset. See [Assign a device to an asset](../bdr/assets/manage-assets.md#assign-a-device-to-an-asset).
