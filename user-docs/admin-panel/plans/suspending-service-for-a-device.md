---
description: Suspend a tracker by switching it to a restricted pricing plan, stopping new tracking while keeping historical data
---

# Suspending a tracker

In certain situations, you might need to temporarily suspend service for a tracker, for example, due to a missed payment. In these cases, you can transition the tracker to a restricted pricing plan. While suspended, it stops tracking and recording new data until the restriction is lifted.

{% hint style="warning" %}
All historical data recorded before the suspension remains securely stored and accessible.
{% endhint %}

To create a pricing plan that suspends the service for a device, follow these steps:

{% stepper %}
{% step %}

## Open the Plans page

Open the Admin Panel and navigate to the **Plans** page.
{% endstep %}

{% step %}

## Create a new plan

Click <img src="../.gitbook/assets/image (7).png" alt="Add plan" data-size="line"> to create a new pricing plan and give it a name (e.g., "Suspended Plan").

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}

## Set up billing

Select **Monthly (daily debit)** as the billing cycle and set the monthly fee to a very high value, such as $999,999. This prevents the customer from being charged during the suspension period.<br>

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>
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

<figure><img src="../.gitbook/assets/image (11).png" alt="Payment options when changing plan" width="365"><figcaption><p>Payment options when changing plan</p></figcaption></figure>
