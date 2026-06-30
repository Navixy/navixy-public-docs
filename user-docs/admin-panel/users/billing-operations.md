---
description: View the full transaction history for any user account and manually adjust their balance and bonus credits directly from Admin Panel
---

# Billing operations

## User's transactions

The list of transactions allows you to check operations that affect a user's balance. To view it, select a user and click **User's transactions** in the right side panel. This window shows all of the user's billing operations, along with a short description of each transaction.

<figure><img src="../.gitbook/assets/image (88).png" alt="User&#x27;s transactions window"><figcaption><p>User's transactions window</p></figcaption></figure>

To access detailed information about a transaction, select it and click <img src="../.gitbook/assets/image (95).png" alt="" data-size="line"> in the rightmost column. This opens the **Transaction** window:

<figure><img src="../.gitbook/assets/{5E363FC7-C9C5-483A-A3D4-9A8FF5C91EB7}.png" alt="Transaction details window" width="269"><figcaption><p>Transaction details</p></figcaption></figure>

## Changing user balance

To manually update the balance of a specific user, click **Change balance** located in [User details](user-details.md).

<figure><img src="../.gitbook/assets/{6E1075EB-FA83-4D59-90B5-C780FF71F568}.png" alt="Change balance window" width="322"><figcaption><p>Change balance window</p></figcaption></figure>

You'll be redirected to a new page where you can view the current balance of the selected user and enter the following transaction details:

- **Transaction description**: The description of the transaction. You may use any description or choose a naming system to follow.
- **Balance changes**: The amount you wish to add to the current balance. Set as negative ("-") to withdraw money.
- **Bonus changes**: The sum of bonuses to add to the balance of your user. Set as negative ("-") to withdraw money.

There are two types of user balances: the actual balance and the bonus balance.

- The actual balance displays the amount of money that a user actually has in their account. The system automatically deducts money from this balance for subscriptions and services, as this is set by the user's pricing plan.
- The bonus balance is an additional optional balance that allows you to gift users bonuses they can use for any services except subscription, such as SMS (e.g., sending out free SMS commands during the IoT device activation) or other services your company provides to customers.

{% hint style="warning" %}
Money is debited from the bonus balance first, followed by the actual balance.
{% endhint %}
