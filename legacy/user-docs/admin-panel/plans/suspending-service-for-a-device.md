---
description: >-
  Suspend a tracker by switching it to a restricted pricing plan, stopping new
  tracking while keeping historical data
---

# Suspending a tracker

You might need to suspend service for a tracker in these cases:

* The customer missed a payment, and you want to stop the service until they pay.
* The customer doesn't use the device for a while, and you want to stop billing for it but keep its history.

To suspend a tracker, move it to a restricted pricing plan.

{% hint style="warning" %}
While a tracker is suspended, it doesn't track, and Navixy doesn't record any new data or history for it. Tracking and history recording start again when the suspension ends, but the history for the suspension period stays empty. History recorded before the suspension stays stored and available.
{% endhint %}

To create a pricing plan that suspends the service for a device, follow these steps:

{% stepper %}
{% step %}
## Open the Plans page

Open the Admin Panel and navigate to the **Plans** page.
{% endstep %}

{% step %}
## Create a new plan

Click <img src="../.gitbook/assets/image (7) (1).png" alt="Add plan" data-size="line"> to create a new pricing plan and give it a name (e.g., "Suspended Plan").

<figure><img src="../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
## Set up billing

Select **Monthly (daily debit)** as the billing cycle and set the monthly fee to a very high value, such as $999,999. This prevents the customer from being charged during the suspension period.<br>

<figure><img src="../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
## Configure the rest of the plan

In the pricing plan settings, disable the features of your choice or leave the default settings to restrict device use.
{% endstep %}

{% step %}
## Finish the creation process

Click **Create** to finish creating the plan.
{% endstep %}

{% step %}
## Assign the plan to a device

Assign the new pricing plan to the device that you want to suspend service for. The device is suspended starting the next day after the pricing plan change.
{% endstep %}
{% endstepper %}

You should check **Repay remainder of current pricing plan payment** and **Charge user now (according to the new plan)** when changing the pricing plan.

<figure><img src="../.gitbook/assets/image (11) (1).png" alt="Payment options when changing plan" width="365"><figcaption><p>Payment options when changing plan</p></figcaption></figure>
