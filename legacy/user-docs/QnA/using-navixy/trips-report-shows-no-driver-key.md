---
title: Trips report shows No driver key
description: "The Trips report shows No driver key for trips made while no driver was assigned to the object. A key touch assigns a driver until the next change."
tags:
  - legacy
---

# Trips report shows No driver key

## Question

Why does the Trips report show "No driver key" when the driver is already identified by RFID or iButton?

## Answer

The **Trips** report shows **No driver key** for trips made while no driver was assigned to the object. The report doesn't check the key at the start of each trip. It follows the history of driver assignments, so a trip without a key touch goes to the last assigned driver.

When the device sends a key that matches the **Hardware key** of a driver in **Drivers**, the platform assigns that driver to the object. Switching the ignition off and records without a key don't remove the driver. The driver stays assigned until one of these happens:

* another key is read on the object;
* the same key is read on another object;
* a user assigns another driver or removes the driver manually.

To see when the driver changed, build the [**Driver shift change**](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/reports/report-types#business-reports) report for the object. If a trip shows **No driver key** after a key touch, open **Raw data** for the time of the touch. Check that a record has the key and that it matches the driver's **Hardware key**.

{% hint style="warning" %}
The platform has no setting that removes the driver when the ignition is switched off. To get this, remove the driver through the API call [`tracker/employee/assign`](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/resources/tracking/tracker/employee#post-tracker-employee-assign) without `new_employee_id` when the ignition switches off.
{% endhint %}

## Links

* [Drivers](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/fleet-management/drivers)
* [Set up driver identification](set-up-driver-identification.md)
* [Report types](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/reports/report-types#business-reports)
