---
description: >-
  View tracker status and run core actions like edit/assign, clone, export,
  activation, plan changes, and Air Console.
---

# Tracker details and operations

## How to view tracker details

To view detailed information about a tracker, double-click its name in the table. Its details appear on the right of the tracker list, including the tracker's connection status, last update time, battery level, and other relevant information.

You also see the following operations below:

* **Edit tracker:** Allows you to change the tracker's name and owner or make it hidden. See [Editing tracker](basic-operations.md#editing-tracker) for more information.
* **Air Console:** Opens [Air Console](air-console.md) for the tracker.
* **Tracker settings**: Opens the **Tracker settings** window for the tracker that shows its information. Only available to activated and non-suspended trackers.\
  This information can also be viewed in the **Devices and settings** module of the Navixy platform. To learn more, see [Devices and settings](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings).

<figure><img src="../.gitbook/assets/{9B1DA52F-55E2-47FC-B79E-234D63D0C19A} (1).png" alt="" width="291"><figcaption></figcaption></figure>

* **Create clone:** Creates a [tracker clone](tracker-clones.md). Only available to trackers that aren't clones.
* **Remove clone**: Deletes a [tracker clone](tracker-clones.md). Only available to trackers that are clones.
* **Change plan:** Opens the [Change plan](change-plan.md) window for the tracker. Only available to trackers that aren't hidden.
* **Retry activation**: Sends default activation commands to the device.
* **Cancel activation:** Permanently removes the device from the database.

## How to edit trackers

Click **Edit tracker** to modify the selected tracker's parameters. The following parameters can be changed:

* **Label**: Update the tracker's name for easier identification on the platform.
* **Tracker owner:** Reassign the device to a different user within your organization.
* **Hidden:** Check this box to deactivate the tracker and remove it from future billing cycles.

{% hint style="info" %}
While a tracker is hidden, it isn't included in the next billing period. The tracker remains active until the end of the current paid period. If the device continues to transmit data during this time, it is still considered active for the billing purposes for the current cycle.

You can't change the subscription plan for a tracker while it is set to hidden.
{% endhint %}

* **Comments:** You can add a few words about the tracker for reference purposes.

## How to export trackers

Tracker data can be exported as a CSV file. Choose which table rows to export by checking the columns in the <img src="../.gitbook/assets/image (12).png" alt="Customize columns" data-size="line"> menu and click <img src="../.gitbook/assets/image (18) (1).png" alt="" data-size="line"> on the toolbar to open the **Export trackers** dialogue.&#x20;

You can choose which trackers to export (selected or all of them) and the output (formatted or raw data). Raw data is data sent via API without any formatting applied to language, date, or time. A raw data file contains all available columns, no matter which you chose.

<figure><img src="../.gitbook/assets/image (5).png" alt="Export trackers dialogue" width="287"><figcaption><p>Exporting trackers</p></figcaption></figure>

