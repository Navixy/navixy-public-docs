---
description: "Activate a GPS device in Device management: choose automatic or manual setup, select the model, enter the details, and wait for the device to connect."
tags:
  - guide
  - beta
---

# Activate a device

Activating a GPS device connects it to the Navixy platform. After activation, the device appears in the **Devices** list. It starts to send its data as soon as its settings tell it where to send them.

This page is for anyone who adds GPS devices to a workspace. Activate each device once, when you install it.

A device needs two settings before it can send data: the address of the Navixy server and the access point name (APN) of the mobile carrier of its SIM card. You choose who sets them:

- **Automatic setup:** Navixy sends the settings to the device in a configuration SMS. You enter the phone number of the SIM card and the APN, and you don't configure the device by hand.
- **Manual setup:** You set the server address, the port, and the APN on the device yourself, for example in the configuration tool of the manufacturer. Navixy sends nothing to the device.

Choose automatic setup when the SIM card can receive SMS. Choose manual setup when you configure devices before you install them, or when the SIM card can't receive SMS.

**Before you start**, prepare the following:

- A GPS device of a supported model, with its SIM card installed and the device powered on
- The IMEI of the device
- A SIM card with mobile data
- The APN of the mobile carrier of the SIM card, with the APN username and password if the carrier uses them
- For automatic setup: the phone number of the SIM card, which must be able to receive SMS
- For manual setup: access to the configuration of the device
- A role that lets you create devices in the workspace, as described in [Access to Device management](README.md#access-to-device-management)

## 1. Start activation

Device management is part of Business Data Repository, so it's in the menu of every workspace that has Business Data Repository.

1. Go to **Device management**.
2. Point to **Activate device**, and then click the setup you want:
   * **Automatic setup**: "The platform configures the device by SMS"
   * **Manual setup**: "You set the server address and port on the device"

   The **Activate device** page opens on the **Model** step. The setup you chose shows next to the page title.

If the workspace has no devices yet, the **Devices** list shows "No devices yet" and an **Activate device** button with the same two setups.

To stop at any step, click **Cancel**. Navixy returns you to the **Devices** list and doesn't keep what you entered.

### Start from an asset

You can also start the activation from the asset that the device will belong to, such as a vehicle. You then activate the device and assign it to the asset in one step.

1. Go to **Assets**, open the tab of the asset type, and click <picture><source srcset="../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> in the asset's row.
2. Next to the **Device** field, click <picture><source srcset="../.gitbook/assets/icons/add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/add.svg" alt="Create" data-size="line"></picture>.
   The **Activate device** panel opens on the **Setup** step.
3. Select **Automatic setup** or **Manual setup**, and click **Next**.
   The **Model** step opens. To change the setup, click **Back**.

The **Device** field has the same icon when you create an asset.

After the **Setup** step, the panel has the same steps as the **Activate device** page, but no **Cancel** button. To stop, click <picture><source srcset="../.gitbook/assets/icons/close-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/close.svg" alt="Close" data-size="line"></picture>.

## 2. Select the model

The list shows the models that Navixy can activate, grouped by vendor. Each vendor shows the number of its models.

1. In the search field, enter the model name, the model code, or the vendor name.
   Search ignores letter case, spaces, and the `-`, `_`, `.`, and `/` characters, so `fmb 920` finds **FMB920**.
2. Open the vendor group and select the model.
3. Click **Next**.

If you click **Next** without a model, the page shows "Select a device model".

If the list of models doesn't load, the page shows "Failed to load devices." Click **Retry** to load it again.

If no model matches your search, the list shows "No models match your search." Check the model name on the device label or on its box, and try the vendor name instead. If your model isn't in the list, Navixy can't activate it yet.

Select the exact model of the device. Navixy uses the model that you select to read the data of the device.

Some models are listed more than once, by firmware version or by hardware variant, for example **FM1100 FW 01.14-01.23** and **FM1100 FW since 01.24**. Select the entry that matches the firmware version of the device.

## 3. Enter the device details

At the top, the **Details** step shows the vendor and the model that you selected. To choose a different model, click **Back**. If you return to the same model, the form keeps the details you entered. If you select a different model, the form clears them.

The fields of the **Details** step depend on the setup.

### Enter the details for automatic setup

1. Optional: in **Name (optional)**, enter a name for the device, up to 255 characters.
   If you leave the field empty, Navixy names the device after its model and IMEI, for example `FMB920 356938035643809`.
2. In **Country code**, select the country of the SIM card.
   The United States is selected by default. To find a country, enter its name or its calling code.
3. In **Phone number**, enter the phone number of the SIM card, without the country code.
   Together with the country code, the number has 5 to 20 digits. You can enter it with spaces, brackets, or dashes, for example `(201) 555-0123`. The form keeps only the digits.
   You can also enter the full number with a plus sign and the country code, for example `+1 201 555 0123`: the form selects the country and removes the code. If it can't recognize the country code, it shows "Select the country from the list and enter the phone number without the country code". A number with letters shows "Phone number can't contain letters".
   When you leave the field with a valid number, the form shows "Failed to detect the mobile carrier. Enter the values in the "GPRS settings" section manually." and expands **GPRS settings**.
4. If **GPRS settings** is closed, expand it.
5. In **APN**, enter the access point name of the mobile carrier.
6. Optional: enter the **APN username (optional)** and the **APN password (optional)**, if the carrier uses them.
7. In **IMEI**, enter the 15-digit IMEI of the device.
   The IMEI is printed on the device and on its box.

The mobile carrier publishes the APN settings for its SIM cards, usually on its website. The APN, the APN username, and the APN password accept Latin letters, digits, and the `.`, `_`, and `-` characters, up to 40 characters each. The username and the password also accept `@`.

### Enter the details for manual setup

The **Details** step of manual setup has no phone number and no APN fields. Instead, it shows the **Server address** and the **Port** that the device must send its data to. They are under "Set the server address and port on the device".

1. Optional: in **Name (optional)**, enter a name for the device, up to 255 characters.
   If you leave the field empty, Navixy names the device after its model and IMEI, for example `FMB920 356938035643809`.
2. In **IMEI**, enter the 15-digit IMEI of the device.
   The IMEI is printed on the device and on its box.

You set the server address and the port on the device in [4. Activate the device](#id-4.-activate-the-device). The device page shows them again after activation.

If the server address doesn't load, the form shows "Failed to load the server address." You can still activate the device. If the form shows "The server address isn't available yet.", your installation of Navixy doesn't publish a server address yet, and you can't set up the device by hand. Use automatic setup, or ask the people who set up your workspace.

### Check the IMEI

Check the IMEI before you click **Activate**. To correct a wrong IMEI later, you cancel the activation and activate the device again: see [Cancel the activation](#cancel-the-activation).

## 4. Activate the device

1. Click **Activate**.
   The device page opens, with **Awaiting connection** next to the name of the device. With automatic setup, Navixy shows "Activation started. Configuration SMS queued." With manual setup, it shows "Activation started. Waiting for the device to connect."
2. With manual setup, set the server address, the port, and the APN of the mobile carrier on the device.
   Use the configuration tool or the SMS commands of the manufacturer, for example Teltonika Configurator. The device page shows the server address and the port under **Manual setup**.

If you started from an asset, Navixy shows the same message and closes the panel. No device page opens, and the **Device** field of the asset shows the new device. If the field already had a device, the new device replaces it. Click **Save** to save the asset. To follow the activation, go to **Device management** and click the device.

If a field is missing or has the wrong format, the form shows the error under the field and doesn't activate the device. For example, an IMEI that doesn't have 15 digits shows "Use 15 digits". A phone number that is too short or too long shows "Use 5 to 20 digits". If an APN field has an error, **GPRS settings** expands so you can see it.

{% hint style="warning" %}
**If Navixy refuses the activation**

Two messages mean that the phone number or the device is already in use. Each shows under its field:

- "This phone number is already in use": a device already uses this SIM card, in this workspace or in any other. Check the phone number. If the SIM card moved from another device in your workspace, deactivate that device or cancel its activation first. The form checks the number when you leave the **Phone number** or the **IMEI** field, so this message can show before you click **Activate**.
- "A device with this IMEI is already on the platform": the IMEI belongs to a device that is already connected, to a device in another workspace, or to a device that was deactivated or whose activation was canceled. Check the IMEI on the device label.
{% endhint %}

## 5. Wait for the device to connect

While the device waits for its first data, **Awaiting connection** shows next to its name on the device page, and in the **Last activity** column of the **Devices** list.

The device page shows how the device gets its settings:

- With automatic setup, **Automatic configuration by SMS** says that the configuration SMS is queued, and shows the time left until you can retry the activation. The SMS usually arrives in 1–2 minutes. It sets the APN in the device and the address that the device sends its data to.
- With manual setup, **Manual setup** shows the server address and the port to set on the device, and "Waiting for the device to connect…".

Under it, **Activation progress** shows the three steps of the activation:

| Step | Automatic setup | Manual setup |
|---|---|---|
| **Registered** | "Device added to the platform", with the time of the activation | The same |
| **Configured** | "Configuration SMS queued", with the APN you entered | "Server address and port set on the device manually", with the server address and the port |
| **Connected** | "Waiting for the first data from the device" | The same |

With manual setup, Navixy can't see the settings of the device, so it marks **Configured** as done as soon as you activate the device. The device connects only after you set the server address, the port, and the APN on it.

The device connects when it's powered on, within cellular coverage, and has its settings. When the first data arrives, **Awaiting connection** disappears, and **Last activity** shows when the device sent its data. If the device page is open at that moment, **Connected** changes to "Device connected, activation complete", and the page shows **Activation complete**: "The device is connected and sending data. It's ready to use." If you open the page later, it shows the device without the activation progress.

## If the device is already on the platform

A device can already be in your workspace without being connected: it isn't activated yet, or it's still awaiting connection. If you enter the IMEI of such a device, **Activate** opens a dialog with the name of that device: **Update the "{title}" device?**. Activation then updates that device instead of creating a second one.

* To activate that device, click **Update device**.
* To go back to the form, click **Cancel**.

The device takes the model you selected and the details you entered. It also takes the setup you chose, so you can change the setup of a device that hasn't connected yet. If you leave **Name (optional)** empty, the device keeps the name it already has. You need permission to change that device.

If Navixy hasn't sent the configuration SMS of the earlier activation yet, it cancels that SMS.

## If the device doesn't connect

If the device sends no data within 2 minutes of the activation, its page shows "Device not connected". Under it, the page lists possible causes in groups. Expand each group to see its causes.

"Device not connected" doesn't mean that the activation failed. The page keeps checking, and when the device connects later, the activation completes. Until it does, the device stays **Awaiting connection**.

### If a device with manual setup doesn't connect

The page shows two groups of causes, **Device** and **SIM card**. Navixy can't check these causes or change the settings of the device. The causes are the following:

- **Server settings:** The server address or the port isn't set on the device, or has a typo. Compare them with the values under **Manual setup** on the device page.
- **APN:** The APN isn't set on the device. Set the APN of the carrier together with the server address and the port.
- **Power:** The device is powered off, or its battery is empty.
- **Sleep mode:** Sleep mode or power saving is on, so the device connects rarely or on a schedule.
- **Stored data:** The device keeps its data in its internal memory and sends it later.
- **Firmware:** The firmware is out of date. Update it to the latest version.
- **SIM card plan:** The plan doesn't include mobile data.
- **PIN code:** The SIM card asks for a PIN code. Turn the PIN off.
- **Roaming:** The SIM card is roaming while data roaming is off.
- **Carrier:** The carrier blocks connections to the platform server.

Fix the causes that apply. You don't need to activate the device again: when the device connects, the activation completes.

### If a device with automatic setup doesn't connect

The page shows three groups of causes: **Device**, **SIM card**, and **Platform**. The causes in the **Device** and **SIM card** groups are on the device or with the mobile carrier. Navixy can't check them or change the settings of the device. These causes are the following:

- **Power:** The device is powered off, or its battery is empty.
- **Sleep mode:** Sleep mode or power saving is on, so the device connects rarely or on a schedule.
- **Cold start:** The first search for satellites after a cold start can take up to 15 minutes under an open sky. Give a new device that time before you change anything.
- **SMS password:** The device has an SMS password that isn't the default password, so it rejects the configuration commands. Remove the password on the device, for example in Teltonika Configurator, and retry the activation.
- **Time zone:** The time zone of the device isn't UTC, so its data arrives with an out-of-date time and is left out of the track.
- **Stored data:** The device keeps its data in its internal memory and sends it later.
- **Firmware:** The firmware is out of date. Update it to the latest version.
- **SIM card plan:** The plan doesn't include SMS or mobile data. Also check that the SIM card still has mobile data and balance left.
- **PIN code:** The SIM card asks for a PIN code. Turn the PIN off.
- **Roaming:** The SIM card is roaming while data roaming is off.
- **Carrier:** The carrier blocks connections to the platform server, or it shut down the network standard that the device uses, such as 2G or 3G, in this region.

The **Platform** group lists the reasons why the configuration SMS might not reach the device. "Configuration SMS queued" doesn't confirm that the SMS was sent. Three of the reasons are settings of your installation of Navixy: the SMS gateway isn't configured, the daily SMS limit is reached or the SMS balance is too low, or the platform doesn't send default settings. Report them to the people who set up your workspace. The fourth reason is an SMS that was lost on the way: retrying the activation sends the SMS again.

Other settings on the device can also make it reject the configuration SMS. Navixy sends the SMS with an empty SMS login and password, so a device that has its own SMS login rejects it. A device that accepts SMS only from authorized phone numbers rejects it too. For most Teltonika models, the SMS starts with two spaces: the empty login and the empty password. If a carrier or an SMS gateway between Navixy and the device removes these spaces, the device can reject the command.

To check the causes and try again, follow these steps:

1. In **Device management**, click the device.
2. Expand each group and check its causes.
   For example, check that the device is powered on and that the SIM card plan includes SMS and mobile data. Also check that the SIM card doesn't ask for a PIN code.
3. Fix the causes that apply.
4. Click **Retry activation**.
   The **Activate device** page opens on the **Details** step, with the details you entered before.
5. Correct the phone number or the APN settings, if they were wrong.
6. If the carrier uses an APN password, enter it again in **APN password (optional)**.
7. Click **Activate**.
   Navixy queues the configuration SMS again, and the 2-minute wait starts again.

If the model of the device is no longer supported, the **Activate device** page stays on the **Model** step and shows "The model of this device is no longer available for activation."

If the device can't receive the configuration SMS, set it up by hand instead. Point to **Activate device**, click **Manual setup**, and activate the device again with the same IMEI. See [If the device is already on the platform](#if-the-device-is-already-on-the-platform).

### Cancel the activation

If the device still doesn't connect, you can cancel the activation. **Cancel activation** shows on the device page 2 minutes after the activation.

1. Click **Cancel activation**.
2. Select the **I understand that this action can't be undone** check box.
3. Click **Cancel activation**.

Navixy shows "Activation canceled" and returns you to the **Devices** list. Navixy removes the device from the **Devices** list, and the device stops reporting to the platform. Its phone number becomes available for another device. If the device is in an optional device field of an asset, Navixy clears that field. You can't undo the cancellation.

You can't cancel the activation of a device that an IoT Logic flow uses. Remove the device from every **Data Source** node that has it, or delete the flow, and then cancel the activation. If you try while a flow still uses the device, canceling fails with the message "Failed to complete the action. Try again." You also can't cancel the activation while the device is in a required device field of an asset or a geo object: assign another device to the field first.

{% hint style="warning" %}
After you cancel, Navixy keeps the IMEI of the device in use. You can't activate a device with this IMEI again, in this workspace or in any other: activation shows "A device with this IMEI is already on the platform". Cancel an activation only when its IMEI is wrong or when you won't use the device again.
{% endhint %}

To correct a wrong IMEI, cancel the activation, and then [activate the device](#id-1.-start-activation) again with the correct IMEI.

## Next steps

- [Manage devices](manage-devices.md)
- [Assets](../bdr/assets/README.md): assign the device to the vehicle or the equipment it tracks
