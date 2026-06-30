---
description: Integrate Howen video telematics with Navixy to combine AI-enhanced video surveillance, facial recognition, and driver monitoring with fleet management.
---

# Howen

Howen Technologies specializes in Vehicle Service Systems (VSS) with a focus on cost-effective, AI-enhanced video telematics solutions. Their devices feature expandable capabilities including facial recognition, driver monitoring, and advanced driver assistance systems.

Integrating Howen VSS with Navixy combines video surveillance with fleet management in one interface. Let's explore how to implement this integration and embed the Howen VSS dashboard into your Navixy interface.

{% stepper %}
{% step %}
**Establishing integration**

To establish the integration, you'll need to provide your Howen account credentials and request integration setup from our support team.

1. **Contact Navixy**: Get in touch with your Customer Success Manager or use [this form](https://www.navixy.com/contact/) to send a request for Howen integration with your Navixy account. Include the following information:
   1. **Your Howen account information**
   2. **Request for Howen integration activation**
2. **Wait for Confirmation**: Our specialists will configure the integration in 1-3 days and confirm when it's ready for use.

{% hint style="success" %}
After you receive confirmation from our support team, your Howen account is ready for the integration!
{% endhint %}
{% endstep %}

{% step %}
**Adding a Howen device to Navixy**

After receiving confirmation from our support team that the integration is ready, you can add your Howen device to the platform. To do this, follow the usual device activation procedure:

1. Go to **Device activation**
2. Select your Howen device from the list
3. Enter the correct **Device ID** (device IMEI)
4. Complete the device configuration

For detailed instructions on how to activate a device in Navixy, see [Activate GPS device](../quick-start/activate-gps-device.md).

{% hint style="danger" %}
Your device and Navixy account are now ready for the integration!
{% endhint %}
{% endstep %}

{% step %}
**Embedding Howen VSS in Navixy UI**

At this step, we perform the actual integration by embedding the Howen VSS dashboard into your Navixy interface.

Navixy offers [User applications](../account/user-applications/) functionality that allows embedding 3rd-party apps directly in the platform's interface. We will use it to embed Howen VSS.

{% hint style="info" %}
**Navigation**

**User applications** section is accessible to account **Owners** in the **Account Settings** section. To find it:

1. Click the profile icon in the top-left corner of the screen to open your account settings
2. In the settings sidebar, select **User applications**
{% endhint %}

1. Create new application\
   Start by clicking the button in the **User applications** list.
   1. Put the Howen VSS dashboard link in the **App URL** field:\
      `https://vss.howentech.com/vss/?tk=hw`
   2. Enter a **Label** for the application (e.g., "Howen VSS Dashboard")
   3. Select **Embedded** in the **Show as** field to display Howen functionality within Navixy
2. Click **Save** to complete the configuration.

{% hint style="success" %}
Your new application appears automatically in Navixy's left sidebar. Open it and log in with your Howen credentials to access your comprehensive AI-powered video telematics dashboard with advanced driver monitoring, intelligent event detection, multi-channel video feeds, and seamless fleet surveillance - all integrated with your existing Navixy fleet management tools.

<img src="../../.gitbook/assets/image (8) (1) (1).png" alt="" data-size="original">
{% endhint %}
{% endstep %}
{% endstepper %}
