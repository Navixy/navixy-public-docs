---
title: Set up a fuel sensor in Navixy
description: "Create a measurement sensor in Devices and Settings, then select fuel as the parameter type, set units, and choose the sensor category"
---

# Set up a fuel sensor in Navixy

## Question

How do I set up a fuel sensor in Navixy?

## Answer

Before reading these instructions, make sure to read about fuel management system in Navixy. Also, if you have a Teltonika tracker, review the following guidance before continuing with this article: [Configuring sensors on Teltonika trackers](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/sensors/configuring-sensors-on-teltonika-trackers).

1. Go to the **Devices and settings** menu section, find your tracker, and select it.
2. Find the **Sensors and buttons** block. This is where you set up sensors in Navixy.
3. Create a measurement sensor. The screenshot below shows how to add a measurement sensor. To find out more about measurement sensors, refer to documentation.

    ![](<../.gitbook/assets/Unknown image (18)>)
4. Set an arbitrary label for the sensor. Select the Parameter which should deliver the fuel information from the tracker. Also, fill in the input number if needed. Select the sensor type as Fuel. Select desirable units. You can skip the accuracy parameter in this case, it affects only some alerts and fuel reports.

    ![](<../.gitbook/assets/Unknown image (19)>)
5. Press **Save** and return to the **Tracking** tab.
6.  Find the tracker in the list and wait for some time. After data packets from the tracker are received, you should see the sensor appearing in the tracker's widgets as on the screenshot below:

    ![](<../.gitbook/assets/Unknown image (107)>)

## Links

* [Fuel management system](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/vehicle-telematics-technology/fuel-management)
* [Configuring sensors on Teltonika trackers](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/sensors/configuring-sensors-on-teltonika-trackers)
* [Measurement sensors](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/vehicle-sensors/measurement-sensors)