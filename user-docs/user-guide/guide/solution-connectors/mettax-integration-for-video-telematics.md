---
description: Integrate MettaX dashcam video telematics with Navixy to use third-party video features alongside Navixy's GPS tracking and telematics functionality.
---

# MettaX

Video telematics is a vital functionality for many businesses. There are proprietary solutions that handle video feed from dashcams but offer limited possibilities for telematic readings of such devices. For this reason, we broadened our approach to integrations so our clients can make use of existing 3rd-party applications for video and benefit from Navixy’s extended telematics features at the same time.

Mettax is a great example of this scenario. Let’s see how easy it is to integrate with its capabilities right inside Navixy UI.

{% stepper %}
{% step %}
**Establishing integration**

First, you need to make the necessary preparations on the Mettax side.

{% hint style="danger" %}
In Mettax, device data is stored under individual user accounts. If your organization uses a parent account structure, **you will need to perform the following steps for each user account that has devices**. While this may require logging in and out of different accounts, it guarantees comprehensive and targeted coverage across your device fleet.
{% endhint %}

1. Log in to a user account that contains actual devices.
2. Turn on **Developer** mode to enable the necessary settings for our integration:![Mettax-UI.png](../../.gitbook/assets/Mettax-UI.png)
   1. Click your account name in the upper-right corner of the window.
   2. Go to **Customer Settings**.
   3. Open the **App Settings** tab.
   4. Enable the **Developer** option.
3. Install **Webhooks** to establish a connection between the two solutions:
   1. Click the apps icon and select **Developer**.\
      ![image-20250428-102043.png](../../.gitbook/assets/image-20250428-102043.png)
   2. Go to **Details**.
   3. Open the **WEBHOOK** tab.
   4. Find these three webhooks, you will need to edit them:\
      ![Mettax-webhooks.png](../../.gitbook/assets/Mettax-webhooks.png)
      1. **pushDeviceInfo**
      2. **pushAlarm**
      3. **pushGpsInfo**
   5. Click **Edit** in the needed webhook’s row to open its parameters.
   6. Fill in the URL field with your Navixy instance address, depending on the region: 1. EU - https://mettax-tracker.navixy.com 2. US - https://mettax-tracker.us.navixy.com
   7. Repeat the URL step for all 3 webhooks.
4. (optional) Repeat the steps above for all required user accounts.

{% hint style="success" %}
Your Mettax account is ready for the integration!
{% endhint %}
{% endstep %}

{% step %}
**Adding a Mettax device to Navixy**

Navixy platform handles the GPS functionality of video devices and their telematic abilities. To make the most of the integration, you need to add your Mettax device to the platform, following the usual procedure:

1. Go to **Device activation**.
2. Select your Mettax device from the list.
3. Enter a correct **Device ID**
4. Complete the device configuration

For detailed instructions on how to activate a device in Navixy, see [Activate GPS device](../quick-start/activate-gps-device.md).

{% hint style="success" %}
Your device and Navixy account are ready for the integration!
{% endhint %}
{% endstep %}

{% step %}
**Embedding Mettax in Navixy UI**

At this step, we perform the actual integration.\
Navixy offers [User applications](../account/user-applications/) functionality that allows embedding 3rd-party apps directly in the platform’s interface. We will use it to embed Mettax.

{% hint style="info" %}
**Navigation**

**User applications** section is accessible to account **Owners** in the **Account Settings** section. To find it:

* Click the profile icon in the top-left corner of the screen to open your account settings
* In the settings sidebar, select **User applications**
{% endhint %}

1. Create new application\
   Start by clicking the <img src="../../.gitbook/assets/1ed57d4def9c49d4937b1a90e4678765.jpg" alt="chrome_py0qhiu5p8.webp" data-size="line"> button in the **User applications** list.
2. Configure the new application
3. Put [https://www.mettaxiot.com/#/dashboard](https://www.mettaxiot.com/#/dashboard) in the **App URL** field.
4. Enter a **Label** for the application (e.g., Mettax dashboard).
5. Select **Embedded** in the **Show as** field to display Mettax functionality within Navixy.
6. Click **Save** to complete the configuration.

{% hint style="success" %}
Your new application appears automatically in Navixy’s left sidebar. Open it and log in with your Mettax credentials.

<img src="../../.gitbook/assets/image-20250428-113317.png" alt="image-20250428-113317.png" data-size="original">
{% endhint %}
{% endstep %}
{% endstepper %}
