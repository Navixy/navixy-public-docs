---
title: Device still connected after applying suspension plan
description: "Device suspension activates after daily billing at 00:05 UTC when the user balance is exceeded by the overcharge plan"
---

# Device still connected after applying suspension plan

## Question

Why is a device still showing connected after applying the suspension tracker plan?

## Answer

In order for a device to be suspended and no longer have the platform accept data from it (whether for billing or any other reason), the user must be **charged more than their balance amount** on their account. This is done by assigning a tracker plan configured to overcharge the user.

However, once this tracker plan is assigned, it doesn't mean the device is suspended instantly.

Billing runs every day at **00:05 UTC+0**, so even if you apply the plan and click **Charge user now**, the charge is applied at the next billing run.

Shortly after that billing run, the device begins to appear offline, as it is charged more than what the user has on file.

### Links

* [Plans](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/plans)
* [Suspending service for a device](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/plans/suspending-service-for-a-device)
