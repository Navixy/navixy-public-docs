# Troubleshooting Guide: X-GPS Tracker

This guide helps resolve common connection and tracking issues (missing points, signal loss). Before reporting an issue,

we suggest checking that the device has the required permissions and settings, since the operating system often restricts

the app to save power.

Quick Validation Checklist

Perform these initial checks:

• Update: Is the app up to date in Google Play or the App Store?

• Connection: Does the device have active mobile data? (Avoid Wi-Fi networks with captive portals or proxies.)

• Session: Has the user signed in correctly, and is the tracker activated?

• Phone status: Is the phone in airplane mode or extreme power-saving mode? (Disable these during testing.)

## **Android Settings**

For continuous background tracking, the system requires full permissions:

**1. Location:** Settings > Apps > X-GPS Tracker > Permissions > Location > "Allow all the time." (Enable "Use precise

location" if available).

<img src="../../.gitbook/assets/unknown (30).png" alt="" height="782" width="353">

**2. Background activity:** Check manufacturer-specific options such as "Autostart" or "Allow background activity"

(Samsung, Xiaomi, etc.).



<img src="../../.gitbook/assets/unknown (31).png" alt="" height="661" width="299">

## **iOS Settings**



For continuous tracking, iOS requires specific settings:

1\. Location: Settings > Privacy & Security > Location Services > X-GPS Tracker > "Always" (Enable "Precise location").



<img src="../../.gitbook/assets/unknown (32).png" alt="" height="618" width="284">



**Data**: Settings > Cellular > X-GPS Tracker > Enable. (Avoid "Low Data Mode").



<img src="../../.gitbook/assets/unknown (33).png" alt="" height="208" width="426">



3. **Background activity**: Settings > General > Background App Refresh > Enable for X-GPS Tracker

<img src="../../.gitbook/assets/unknown (34).png" alt="" height="158" width="421">

Note: "Low Power Mode" significantly restricts background activity; avoid using it during the workday.



## **Best Practices**

\
To review the case more accurately, we recommend first confirming that X-GPS Tracker has the required permissions\
and that no local restrictions are affecting its background operation.\
On some devices, the operating system, manufacturer, or corporate policies may restrict location, mobile data, or battery\
usage. Verifying these points helps rule out settings outside the platform and allows the technical review to focus more\
efficiently.\
If the behavior continues after reviewing the settings, please share the information listed in the previous section. With these details, we can analyze the case more thoroughly and provide better guidance.



## **Does the problem persist?**



If everything has been configured correctly and the problem persists, you can report the situation to support@navixy.con

with the following information:

• Device details: Make, model, and OS version.

• Tracker ID: Exact ID registered in the platform.

• Failure context: Exact date and time (including time zone) when the issue occurred.

• Description: What the customer expected to see vs. what actually happened.

• Evidence: Screenshots of permissions (location, battery, data) and the selected tracking mode.
