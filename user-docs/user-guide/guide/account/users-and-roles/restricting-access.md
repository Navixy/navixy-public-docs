---
description: >-
  Control which GPS devices, geofences, places, and notification rules each user
  can see in Navixy. Protects sensitive data across different teams.
---

# Restricting access

Navixy enables organizations to effectively manage access to user-created information, including GPS devices and associated artifacts such as geofences, places, and notification rules. This capability is vital for maintaining data security and operational efficiency, especially in organizations with multiple divisions or business units. This approach ensures that:

* Sensitive information is protected from unauthorized access
* Users can focus on the data and tools relevant to their roles
* Operational efficiency is maintained across different divisions and business units

This document outlines how GPS devices and information created by users are secured and shared with other employees within the organization.

## GPS devices

When a user adds a GPS device, it is effectively created under the owner’s account, so even after the user is deleted, the GPS device remains in the organization’s account. The owner can specify which users can view GPS device data, such as trips or sensor data, ensuring that only authorized individuals can view and manage the device.

![](<../../../.gitbook/assets/image-20240718-040427 (1).png>)

## Associated artifacts

* **Notification rules**: Notification rules allow users to set up alerts based on various criteria, such as speeding or leaving a geofence. When a user creates a rule, it is effectively created in the owner’s account. Other users with access to the relevant device can also view and use these rules.
* **Places and geofences**: Users can create Places and geofences to define specific areas of interest. While these artifacts are managed by the user who created them, they are effectively linked to the owner’s account. The owner can grant access to these geofences to other users within the organization. Only users with the appropriate permissions can view and modify these geofences.
