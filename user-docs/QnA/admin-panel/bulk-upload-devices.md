---
title: Bulk upload devices
description: "Activate multiple devices in bulk by uploading CSV-XLSX to Support or using the tracker/register API endpoint with device specifications"
---

# Bulk upload devices

## Question

How do I activate multiple devices at once?

## Answer

Option 1: contact Support with a CSV/XLSX containing:

* model
* label
* device\_id (IMEI)
* phone (cannot be null)
* apn\_name
* apn\_user
* apn\_password

Option 2: use the API `tracker/register` in a loop. By default, you can use `plugin_id: 44`.

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

* [API: tracker/register](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/tracking/tracker#register)
* [Bulk activation](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/devices/bulk-activation)
* [Automatic device activation](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/quick-start/activate-gps-device#activate-gps-device-automatically)
