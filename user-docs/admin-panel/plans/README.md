---
description: "Create and manage pricing plans in Admin Panel: define billing cycles, fees, data rates, device limits, map access, and which features are available to users"
---

# Plans

The **Plans** page is used to view, create, and edit new pricing plans for your Navixy account. The pricing plans allow you to manage features available to your end users and set up billing. You can create any number of pricing plans.

<figure><img src="../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

It consists of three main sections:

* **Plan toolbar:** A toolbar used to easily create, find, and edit plans
* **Plan list:** A list of existing plans formatted as a table
* **Plan details:** Information about the selected plan

{% hint style="warning" %}
The billing system uses an advance payment method, where users can only access the services when they have enough funds in their personal account.
{% endhint %}

## Plan settings

A pricing plan is a set of commercial terms that defines the structure of the service packages and their prices available to a user. Each tracker can be assigned its own pricing plan, allowing users to track assets with different pricing plans.

When a user creates an account, Navixy server automatically creates a test Demo pricing plan, which is recommended for use only during the test period.

## How to create a plan

Click <img src="../.gitbook/assets/image (87).png" alt="" data-size="line"> to create a new plan. This opens the **New plan** window:

<figure><img src="../.gitbook/assets/image (91).png" alt="New plan window"><figcaption></figcaption></figure>

This window contains the following settings:

* **Label:** The plan's name.
* **Billing cycle:** Payment frequence. There are four billing cycle types to choose from:
  * **Monthly:** The user is charged on the first day of the month at 00:05 UTC or immediately after applying the pricing plan. If you check **Monthly fee proportional charge**, the user is charged for the number of days left in the month. Otherwise, they are charged for the full month.
  * **Monthly (daily debit):** The user is charged each day at 00:05 UTC proportionally to the monthly fee.
  * **Daily:** The user is charged each day at 00:05 UTC. This is a post-paid plan, meaning the user is charged only if the tracker connected to the server at least once in the last 24 hours.
  * **Annual:** The user is charged on the first day of the year at 00:05 UTC. This is a pre-paid plan, meaning the user is charged for the year ahead regardless of whether the device is used or not.
* **Fee:** Your fee.
* **Use this plan as default**: Check if you want to set the created plan as default. The default pricing plan is automatically assigned to all new devices in the account and applied to trackers when transferring trackers between user accounts.
* **Rates:** Rates for various services used by your users. Includes the option to forbid some of them.
* **Plan options:**
  * **Maximum number of devices:** If a user reaches this limit, they aren't able to add or track any additional assets. If a user has assets under different plans, the plan with the minimum value is applied.
  * **Store history:** Users can also set up a period for which data is stored in the system, ranging from one or several days to months or years.
* **Available maps:** Select which maps are available to users who track devices under this plan.
* **Available features:** Plan settings also include the range of additional applications, such as Reports, Field service, and Fleet, as well as other features and miscellaneous options.
* **Plan availability:** Group similar plans to organize your offerings and enable user self-service. Plans within the same group allow users to switch between them independently, provided the **User are allowed to select this plan** checkbox is enabled. Nesting similar plans into groups ensures account compatibility and prevents users from mixing incompatible plans on a single account, which can cause map or feature errors, while maintaining control over your pricing structure.

## How to edit a plan

To edit an existing plan, double-click its entry in the list, select it and click <img src="../.gitbook/assets/image (92).png" alt="" data-size="line"> on the toolbar, or click **Edit plan** in the right sidebar. This opens the **Edit plan** window, which is identical to the **New plan** window. You can change all the settings except for the billing cycle.

## Trackers on plan

To learn which trackers are listed under the selected plan, select the plan and click **Trackers on plan** in the right sidebar. This opens the **Trackers on plan** window describing all trackers under the selected plan:

<figure><img src="../.gitbook/assets/image (99).png" alt="Trackers on plan"><figcaption><p>Trackers on plan</p></figcaption></figure>

To learn more about a tracker, select it in the list and click <img src="../.gitbook/assets/image (95).png" alt="Information" data-size="line">.
