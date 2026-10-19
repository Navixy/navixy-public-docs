---
description: "How to activate a GPS device in Device Management: select the model, enter the SIM card and IMEI details, and wait for the device to connect."
tags:
  - guide
  - beta
---

# Activate a device

Activating a GPS device connects it to the Navixy platform. After activation, the device appears in the **Devices** list. Navixy sends a configuration SMS to the SIM card in the device, and the device starts to send its data.

This page is for anyone who adds GPS devices to a workspace. Activate each device once, when you install it.

**Before you start**, prepare the following:

- A GPS device of a supported model, with its SIM card installed and the device powered on
- The phone number of the SIM card
- The access point name (APN) of the mobile carrier of the SIM card, and the APN username and password if the carrier uses them
- The IMEI of the device
- A role that lets you create devices in the workspace, as described in [Access to Device Management](README.md#access-to-device-management)

The SIM card needs mobile data and must be able to receive SMS. The device uses mobile data to send its data. The SMS contains the settings that tell the device where to send its data.

## 1. Start activation

Device Management is part of Business Data Repository, so it is in the menu of every workspace that has Business Data Repository.

1. Go to **Device Management**.
2. Click **+ Activate device**.
   The **Activate device** page opens on the **Model** step.

If the workspace has no devices yet, the **Devices** list shows "No devices yet" and an **Activate device** button. That button opens the same page.

To stop at any step, click **Cancel**. Navixy returns you to the **Devices** list and doesn't keep what you entered.

### Start from an asset

You can also start the activation from the asset that the device will belong to, such as a vehicle. You then activate the device and assign it to the asset in one step.

1. Go to **Assets**, open the tab of the asset type, and click <picture><source srcset="../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> in the asset's row.
2. Next to the **Device** field, click <picture><source srcset="../.gitbook/assets/icons/add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/add.svg" alt="Create" data-size="line"></picture>.
   The **Activate device** panel opens on the **Model** step.

The **Device** field has the same icon when you create an asset.

The panel has the same steps as the **Activate device** page, but no **Cancel** button. To stop, click <picture><source srcset="../.gitbook/assets/icons/close-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/close.svg" alt="Close" data-size="line"></picture>.

## 2. Select the model

The list shows the models that Navixy can activate, grouped by vendor. Each vendor shows the number of its models in brackets.

1. In the search field, enter the model name or the vendor name.
   Search ignores letter case, spaces, and the `-`, `_`, `.`, and `/` characters, so `fmb 920` finds **FMB920**.
2. Open the vendor group and select the model.
3. Click **Next**.

If you click **Next** without a model, the page shows "Select a device model".

If the list of models doesn't load, the page shows "Failed to load the data." Click **Retry** to load it again.

If no model matches your search, the list shows "No models match your search." Check the model name on the device label or on its box, and try the vendor name instead. If your model isn't in the list, Navixy can't activate it yet.

Select the exact model of the device. Navixy reads the data of the device by the model that you select.

Some models are listed more than once, by firmware version or by hardware variant, for example **FM1100 FW 01.14-01.23** and **FM1100 FW since 01.24**. Select the entry that matches the firmware version of the device.

## 3. Enter the device details

At the top, the **Details** step shows the vendor and the model that you selected. To choose a different model, click **Back**. If you return to the same model, the form keeps the details you entered. If you select a different model, the form clears them.

1. Optional: in **Device name (optional)**, enter a name for the device.
   If you leave the field empty, Navixy names the device after its model and IMEI, for example `FMB920 356938035643809`.
2. In **Phone number**, enter the phone number of the SIM card, with the country code.
   The number has 5 to 15 digits. You can type it with spaces, brackets, dashes, or a plus sign, for example `+1 (201) 555-0123`: the form keeps only the digits. A number with letters shows "Phone number can't contain letters". When you leave the field, the form shows "Failed to detect the mobile carrier. Enter the values in the "GPRS settings" section manually." and expands **GPRS settings**.
3. If **GPRS settings** is closed, expand it.
4. In **APN**, enter the access point name of the mobile carrier.
5. Optional: enter the **APN username (optional)** and the **APN password (optional)**, if the carrier uses them.
6. In **IMEI**, enter the 15-digit IMEI of the device.
   The IMEI is printed on the device and on its box.
7. Optional: in **Activation code (optional)**, enter the activation code, if you have one.

The mobile carrier publishes the APN settings for its SIM cards, usually on its website. The APN, the APN username, and the APN password accept Latin letters, digits, and the `.`, `_`, and `-` characters, up to 40 characters each. The username and the password also accept `@`.

Check the IMEI before you click **Activate**. To correct a wrong IMEI later, you cancel the activation and activate the device again: see [If the device doesn't connect](#if-the-device-doesnt-connect).

## 4. Activate the device

1. Click **Activate**.
   Navixy shows "Device activated, configuration SMS queued", and the device page opens with the device as **Waiting to connect**.

If you started from an asset, Navixy shows the same message and closes the panel. No device page opens, and the **Device** field of the asset shows the new device. If the field already had a device, the new device replaces it. Click **Save** to save the asset. To follow the activation, go to **Device Management** and click the device.

If a field is missing or has the wrong format, the form shows the error under the field and doesn't activate the device. If an APN field has an error, **GPRS settings** expands so you can see it.

{% hint style="warning" %}
**If Navixy refuses the activation**

Two messages mean that the phone number or the device is already in use. Each shows under its field:

- "This phone number is already in use": a device already uses this SIM card, in this workspace or in any other. Check the phone number. If the SIM card moved from another device, delete that device first. The form checks the number when you leave the **Phone number** or the **IMEI** field, so this message can show before you click **Activate**.
- "A device with this IMEI is already registered": the IMEI belongs to a device that is already connected, or to a device in another workspace. Check the IMEI on the device label.
{% endhint %}

After activation, Navixy adds the device to the **Devices** list and queues a configuration SMS to the phone number you entered. The SMS sets the APN in the device and the address that the device sends its data to. The SMS usually arrives in 1–2 minutes.

While the device waits to connect, the device page shows three sections:

- **Automatic configuration via SMS**, which says that the configuration SMS is queued and shows the time left until you can retry the activation.
- **Manual configuration**, with a server address and a port for configuring the device by hand.
- **Activation progress**, with the steps of the activation:
  - "Device registered, waiting to connect", with the time of the activation
  - "Configuration SMS queued", with the APN you entered
  - "Waiting for the first data packet from the device"

The device connects after it receives the SMS, if it's powered on and within cellular coverage. When the first data arrives, the last step changes to "Device connected, activation complete", with the time. If the device page is open at that moment, it shows **Activation complete**: "The device is connected and sending data. It's ready to use."

## If the device is already in Business Data Repository

If a device with the IMEI you enter is already in Business Data Repository and isn't activated yet, activation activates that device instead of creating a second one.

In that case, the model changes to the model you selected on the **Model** step, and the device takes the name in **Device name (optional)**. If you leave that field empty, the device keeps the name it already has. You also need permission to change that device.

## If the device doesn't connect

If the device sends no data within 2 minutes of the activation, its page shows "Device hasn't connected". Under it, the page lists possible causes in three groups: **Device**, **SIM card**, and **Platform**. Until the 2 minutes pass, the page shows "Retry available in" and the time left.

"Device hasn't connected" doesn't mean that the activation failed. The page keeps checking, and when the device connects later, it shows **Activation complete**. The first search for satellites after a cold start can take up to 15 minutes under an open sky, so give a new device that time before you change anything.

The causes in the **Device** and **SIM card** groups are on the device or with the mobile carrier. Navixy can't check them or change the settings of the device:

- **Power:** the device is powered off, or its battery is empty.
- **Sleep mode:** sleep mode or power saving is on, so the device connects rarely or on a schedule.
- **SMS password:** the device has an SMS password that isn't the default one, so it rejects the configuration commands. Remove the password on the device, for example in Teltonika Configurator, and retry the activation.
- **Stored data:** the device keeps its data in its internal memory and sends it later.
- **Firmware:** the firmware is out of date. Update it to the latest version.
- **SIM card plan:** the plan doesn't include SMS or mobile data. Also check that the SIM card still has mobile data and balance left.
- **PIN code:** the SIM card asks for a PIN code. Turn the PIN off.
- **Roaming:** the SIM card is roaming while data roaming is off.
- **Carrier:** the carrier blocks connections to the platform server, or it shut down the network standard that the device uses, such as 2G or 3G, in this region.

Other settings on the device can also make it reject the configuration SMS. Navixy sends the SMS with an empty SMS login and password, so a device that has its own SMS login rejects it. So does a device that accepts SMS only from authorized phone numbers. For a Teltonika device, the SMS starts with two spaces, the place of the empty login and password. If a carrier or an SMS gateway on the way removes these spaces, the device can reject the command.

To check the causes and try again, follow these steps:

1. In **Device Management**, click the device.
2. Expand each group and check its causes.
   For example, check that the device is powered on, that the SIM card plan includes SMS and mobile data, and that the SIM card doesn't ask for a PIN code.
3. Fix the causes that apply.
4. Click **Retry activation**.
   The **Activate device** page opens on the **Details** step, with the details you entered before.
5. Correct the phone number or the APN settings, if they were wrong.
6. If the carrier uses an APN password, enter it again in **APN password (optional)**.
7. Click **Activate**.
   Navixy queues the configuration SMS again.

If the model of the device is no longer supported, the **Activate device** page stays on the **Model** step and shows "The model of this device is no longer available for activation."

If the device still doesn't connect, you can cancel the activation:

1. Click **Cancel activation**.
2. Turn on **I understand that this action can't be undone**.
3. Click **Cancel activation**.

The device is removed from the device list and stops reporting to the platform. Its phone number becomes available for another device. If the device is assigned to an asset, Navixy removes it from the asset. You can't undo this.

You can't cancel the activation of a device that an IoT Logic flow uses. Remove the device from every **Data Source** node that has it, or delete the flow, and then cancel the activation. If you try while a flow still uses the device, canceling fails with the message "Failed to load the data." The same applies when the device is in a required device field of an asset or a geo object: assign another device to the field first.

{% hint style="warning" %}
After you cancel, Navixy keeps the IMEI of the device in use. You can't activate a device with this IMEI again, in this workspace or in any other. Cancel only an activation with a wrong IMEI, or of a device you won't use again.
{% endhint %}

To correct a wrong IMEI, cancel the activation, and then [activate the device](#id-1.-start-activation) again with the correct IMEI and the same phone number.

## Next steps

- [Manage devices](manage-devices.md)
- [Assets](../bdr/assets/README.md), to link the device to the vehicle or the equipment it tracks
