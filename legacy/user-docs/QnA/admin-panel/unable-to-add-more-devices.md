---
title: Unable to add more devices
description: "Check device plan limits; the platform applies the most restrictive limit when a user has conflicting plans assigned"
---

# Unable to add more devices

## Question

Why am I unable to add a device when the user is paid up?

## Answer

If you're unable to add devices to a user, it may be related to your device plan limit, shown in Admin Panel → Plans → Edit plan → Plan options.

The platform uses the least-rights approach: if there is a conflict between two plans under a user, it applies the most restrictive values.

Example:

* Plan A: limit 100
* Plan B: limit 50

Result: effective limit is 50.

Fix: update the lowest plan limit to match the higher one.

It's usually best to keep this limit field maxed out to avoid hitting this issue.

## Links

* [Plans](https://www.navixy.com/docs/admin/plans)
