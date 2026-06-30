---
title: Idling alert is not triggering
description: Set the idling duration threshold and verify an ignition input sensor exists; check manufacturer documentation for ignition pin mapping
---

# Idling alert is not triggering

## Question

What could be the reason why the alert is not popping up and there is no mention in the reports?

## Answer

To activate the alert, you must set the duration after which idling will be considered excessive. The rule monitors when a vehicle is parked (as determined by the Parking Detection settings) while the ignition is **ON**. If the vehicle remains in this state longer than the specified duration, a notification is triggered.

Customers often assume that the ignition input is created on the platform by default, but this is not the case. The platform doesn't know where the ignition is connected unless the manufacturer has clearly documented that input.

![](<../.gitbook/assets/Unknown image (30)>)

## Links

[Excessive idling](https://app.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/s/446mKak1zDrGv70ahuYZ/guide/events-and-notifications/vehicle-efficiency/excessive-idling)