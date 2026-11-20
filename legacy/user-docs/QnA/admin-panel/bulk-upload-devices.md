---
title: Bulk upload devices
description: "Activate many devices at once by importing a file in the web interface, by sending a file to Navixy support, or through the API."
---

# Bulk upload devices

## Question

How do I activate multiple devices at once?

## Answer

You can activate many devices at once in three ways: import a file in the web interface, send a file to Navixy support, or call the API.

Option 1: import the devices from an Excel or CSV file. In **Device activation**, select the model and the group, then click **Import from file**. One file activates up to 1,000 devices of one model into one account. For more devices, import several files one after another. Only the account owner can import, on a computer.

Option 2: for devices of several users or several models in one file, for the X-GPS Tracker app, or on an on-premise installation, send a CSV or XLSX file to [Navixy support](mailto:support@navixy.com). The columns are listed in the tracker list template of the Admin Panel page on bulk activation.

Option 3: use the API `tracker/register` in a loop. By default, you can use `plugin_id: 44`.

Example request:

```bash
curl -X POST 'https://api.us.navixy.com/v2/tracker/register' \
  -H 'Content-Type: application/json' \
  -d '{
    "hash": "[HASH]",
    "label": "Courier",
    "group_id": 0,
    "plugin_id": 44,
    "model": "qlgv55lite",
    "phone": "79123122312",
    "device_id": "123451234512346",
    "apn_name": "fast.tmobile.com",
    "apn_user": "tmobile",
    "apn_password": "tmobile"
  }'
```

## Links

* [Activate many devices from a file](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/quick-start/activate-gps-device#activate-many-devices-from-a-file)
* [API: tracker/register](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/resources/tracking/tracker#post-tracker-register)
* [Bulk activation: tracker list template](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/devices/bulk-activation#tracker-list-template)
* [Automatic device activation](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/quick-start/activate-gps-device#activate-gps-device-automatically)
