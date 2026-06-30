---
description: Register 30 or more GPS trackers in Navixy at once. Submit a CSV or Excel file to Navixy support with device model, ID, APN, and user assignment.
---

# Bulk activation

If you are transferring a large number of GPS devices from another platform to Navixy, activating each device manually can be a challenge. Instead, you can contact [Navixy support](mailto:support@navixy.com) to register hundreds or even thousands of devices with just a single CSV or Excel file.

## Device list template

Before you begin, ensure that your CSV file contains the following columns in the specified order:

- user_id
- model
- label
- device_id
- phone
- apn_name
- apn_user
- apn_password
- comment

If you need to assign devices to different users, fill in the `user_id` column. In case of a file without `user_id`, all devices are added to a single user. Avoid using special characters such as "+", "°", or double spaces in the file. The comment field is optional and can be used to add additional information about a device. Navixy also provides a [sample file](https://www.navixy.com/wp-content/uploads/2022/04/hardware-trackers-activation-example.csv) for guidance.

Once the bulk upload file has been created, reach out to Navixy’s support team to assist further with the upload.

Note that the minimum bulk upload amount is 30 devices.

## Bulk activating of X-GPS tracker app

In case of X-GPS Tracker mobile app, use the following column names and order:

- model
- label
- notification_phone
- notification_email

The device IDs are generated automatically. If you have any questions or concerns, feel free to reach out to the Navixy customer success team.
