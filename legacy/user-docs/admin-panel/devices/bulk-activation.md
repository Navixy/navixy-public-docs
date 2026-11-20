---
description: >-
  Activate many GPS trackers at once: users import files of up to 1,000
  trackers of one model, and Navixy support uploads mixed lists for you.
---

# Bulk activation

When you move a large number of GPS trackers to Navixy, for example from another platform, activating each tracker in the wizard takes too long. You can activate many trackers at once in two ways:

* [Import from a file](bulk-activation.md#import-from-a-file): the user activates up to 1,000 trackers of one model per file from an Excel or CSV file, without help from support.
* [Upload by Navixy support](bulk-activation.md#upload-by-navixy-support): you send a CSV or Excel file to [Navixy support](mailto:support@navixy.com), and support registers the trackers.

## Import from a file

A user activates trackers from a file in the web app's device activation wizard. After the user selects the model and the group, **Import from file** opens a window with a template for that model and activation method. The steps are in [Activate many devices from a file](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/quick-start/activate-gps-device#activate-many-devices-from-a-file) in the User Guide.

You can also run the import for a customer. Open the user in **Users**, click **Login as user**, and follow the same steps.

The import has these limits:

* Sub-users don't see **Import from file**. Only the user who owns the account can import.
* **Import from file** is available in the web app on a computer, in a full-width browser window.
* One file activates trackers into one user account.
* All trackers in one file are the same model, use the same activation method, and go to the same group.
* A file has at most 1,000 rows, one row per tracker. For more than 1,000 trackers, the user splits the list into files of up to 1,000 rows and imports them one after another.
* Mobile apps, such as X-GPS Tracker, can't be activated from a file.
* On-premise installations don't have **Import from file**. Use the upload by Navixy support.

## Upload by Navixy support

Navixy support can register up to 5,000 trackers from one file, including trackers for several users. Use the upload by support for these cases:

* Trackers for several users, or of several models, in one file.
* The X-GPS Tracker app.
* An on-premise installation.

The minimum for an upload is 30 trackers. All trackers in one file use the same activation method and go to the same group.

### Tracker list template

The file has a header row with the column names below. Navixy support reads the columns by their names, so their order doesn't matter. Navixy provides a [sample file](https://cdn.navixy.com/hardware_trackers_activation_example_2781a794af.csv) for guidance.

| Column | What to enter | Required |
| --- | --- | --- |
| `user_id` | The ID of the user who gets the tracker, from the user list in Admin Panel. | When support uploads trackers for several users. In a file for one user, support ignores the column. |
| `model` | The tracker model code. | Yes |
| `label` | The tracker label. | Yes |
| `device_id` | The device ID, such as the IMEI. | Yes |
| `phone` | The phone number of the SIM card, with the country code and without "+". | No |
| `iccid` | The ICCID of the SIM card. | No |
| `apn_name` | The APN name. | The column is required, the value isn't: leave the cell empty if the tracker needs no APN. |
| `apn_user` | The APN username. | No |
| `apn_password` | The APN password. | No |
| `activation_code` | The activation code. | No |
| `comment` | Additional information about the tracker. | No |

Avoid special characters such as "°" or double spaces in the file. A file without one of the columns `model`, `label`, `device_id`, or `apn_name` is rejected.

When the file is ready, send it to [Navixy support](mailto:support@navixy.com).

### Bulk activation of the X-GPS Tracker app

For the X-GPS Tracker mobile app, use these columns:

* `model`
* `label`
* `notification_phone`
* `notification_email`

Fill in `notification_phone`, `notification_email`, or both. The device IDs are generated automatically. Send the file to [Navixy support](mailto:support@navixy.com).
