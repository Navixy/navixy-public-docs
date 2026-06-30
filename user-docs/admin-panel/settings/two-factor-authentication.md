---
description: Enable two-factor authentication for all users or specific accounts, with inheritance to sub-users and API management options
---

# Security

The **Security** page allows you to improve the security of user accounts on the Navixy platform by enabling two-factor authentication (2FA). You can configure this security feature for all users or selectively enable it based on specific account needs.

## **How to enable two-factor authentication**

Two-factor authentication can be enabled either for all users in your system or for specific users via their settings.

* **For all users**: Configured in **Admin Panel → Settings → Security.** Ensures that all current and new users must authenticate using a one-time passcode in addition to their login credentials. All sub-users and imported users also inherit the 2FA settings.

{% hint style="info" %}
2FA settings don't apply to demo users
{% endhint %}

<figure><img src="../.gitbook/assets/image (53).png" alt="Two-factor authentication settings" width="378"><figcaption><p>Two-factor authentication settings</p></figcaption></figure>

* **For specific users**: For individual accounts that require personalized security, you can enable 2FA in **Admin Panel → Users → Edit user → Two-factor authentication.** All sub-users created in that account also inherit the 2FA settings.

<figure><img src="../.gitbook/assets/image (54).png" alt="Two-factor authentication for a specific user" width="563"><figcaption><p>Two-factor authentication for a specific user</p></figcaption></figure>

{% hint style="info" %}
Note that the current implementation only allows enabling two-factor authentication for all account users, including sub-users created in the main account.
{% endhint %}

To configure 2FA settings, you can use the Navixy Panel API, using the calls to read and update 2FA settings for a list of specified users or all users of the platform, as well as set default settings for new users. You can learn more about the Navixy Panel API in the [Developer Hub](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/panel-api/resources/user/mfa-settings).
