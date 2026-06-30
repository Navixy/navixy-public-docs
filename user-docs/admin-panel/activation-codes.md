---
description: Manage activation codes that restrict tracker registration and link codes to device plans for automated setup
---

# Activation codes

The **Activation codes** page allows you to view, find, create, and edit activation codes for activating your trackers in the device activation wizard available either in [User details](users/user-details.md) or in the Navixy platform. For more information about this process, see [Activate GPS device](https://www.navixy.com/docs/user/guide/quick-start/activate-gps-device).

<figure><img src=".gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

The page consists of three main sections:

* **Code toolbar:** A toolbar used to easily create, find, and edit activation codes
* **Codes list:** A list of existing activation codes formatted as a table
* **Code details:** Information about the selected activation code

## What are activation codes?

Activation codes are unique 10-digit random numbers that restrict the process of adding a new tracking device to a user's account. This process is typically used to prevent users from activating devices purchased from sources outside of your organization.

<figure><img src=".gitbook/assets/1 234 567 890 (1)-20230811-205456.png" alt="Activation code explanation"><figcaption><p>Activation code explanation</p></figcaption></figure>

## How to create an activation code

To create a new activation code, click <img src=".gitbook/assets/image (69).png" alt="" data-size="line"> on the toolbar. This opens the **Create activation codes** window:

<figure><img src=".gitbook/assets/image (68).png" alt="Create activation code window" width="324"><figcaption><p>Create activation codes window</p></figcaption></figure>

Here, you can create any number of codes, set the bonus and free days for them, and select the tariff plan you want to use.

## Using activation codes

If you enforce the usage of activation codes for your users, the activation process is generally the same as for other methods. The only difference is the user has to enter the provided code into the **Activation code** field during step 3 of the device activation process:

<figure><img src=".gitbook/assets/image (6).png" alt="Activation code in device activation wizard" width="335"><figcaption><p>Activation code in the device activation wizard</p></figcaption></figure>

You can link activation codes directly to specific [device plans](plans/) to automate the onboarding process. When a user activates a device using a code, the system automatically configures the appropriate access levels and features.

This feature is disabled by default. To enable it for your Navixy instance, contact the Navixy customer success team.
