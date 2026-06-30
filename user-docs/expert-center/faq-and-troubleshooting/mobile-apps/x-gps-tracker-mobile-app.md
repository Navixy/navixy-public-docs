---
description: Configure X-GPS Tracker app permissions for GPS, battery, network, and background access to ensure reliable location reporting from your phone.
---

# X-GPS Tracker mobile app

X-GPS Tracker application requires several permissions to operate correctly.

95% of issues with tracks or work in the background are a result of incorrect setup of the phone.\
Privacy is one of the most precious things in the current world so it's not unusual for companies to prevent apps from actively recording data.\
Running an open app is one set of permissions, running in the background is another one, running when the screen is locked is one more, and so on.

Systems might limit the usage of resources, especially in the background. For these reasons, you need to ensure your phone is setup correctly.

The generic settings are configured during [initial activation](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/x-gps-mobile-apps/x-gps-tracker/first-start) process. However, the availability of settings depends on the OS version and phone manufacturer, and model. So your phone might have settings that are model-specific and aren't set by default.

### What settings do I need to set up?

In general, permissions can be set into several groups:

#### GPS location

Most phones allow user to set custom location permissions for each app. To ensure correct work of X-GPS Tracker, check that location is enabled for the app and is allowed always, not only when the app is open.

#### Battery usage

Most phones offer battery optimization settings and white lists.\
Make sure your X-GPS Tracker app is exempt from battery optimization. If there is such feature, the app has to be white-listed for low battery modes.

#### Network access

App must have access to Cellular data with no data caps. This will ensure your app will be able to transfer data.

#### Background work

Some manufacturers limit which apps can and can't work in the background. If your phone has this feature, whitelist it. Huawei is the most notorious for this.

{% hint style="info" %}
Site [dontkillmyapp.com](https://dontkillmyapp.com/) has recommendations for various models. There is also an [app version](https://play.google.com/store/apps/details?id=com.urbandroid.dontkillmyapp) to check settings.
{% endhint %}

### Which phones are recommended?

Our teams test on:

Samsung Galaxy A01, Android 10.0\
Samsung A520F/DS, Android 8.0.0\
Xiaomi Redmi 4X, Android 7.1.2\
Xiaomi Redmi 7, Android 9.0

You can check the list of phones on this site:\
[dontkillmyapp.com](https://dontkillmyapp.com/)

The more thumbs down the phone has, the more likely it will shutdown the app.

### What information do I need to provide to support?

If you are sure that all settings are correct, we will need the following information:

- Phone manufacturer and model
- OS version
- App version
- ID of the GPS device on the Navixy platform
- Exact description of the issue you're having with screenshots, if applicable

If your issue is connected with tracking and quality of tracks additional information is required:

- Screenshots of tracking mode settings
- Screenshots of advanced tracking settings (battery and charging)
- Screenshots of permissions in the phone's settings.

If your question is connected with work on schedule additional information is required:

- Screenshots of schedule configuration in the app

{% hint style="info" %}
If you have several phones, each of them must have a separate list of settings and screenshots provided.

Tickets with incomplete information can't be passed to developers, so provide all of the necessary data listed.
{% endhint %}
