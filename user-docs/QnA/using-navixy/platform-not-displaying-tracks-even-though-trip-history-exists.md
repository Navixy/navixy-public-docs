---
title: Platform not displaying tracks even though trip history exists
description: Disable Split by stops and Smart filter settings in History to remove all filters and display the raw location data from the device
---

# Platform not displaying tracks even though trip history exists

## Question

Why does Navixy show an empty trip history even though the device had some real trip history?

## Answer

First thing to keep in mind is that the platform usually shows what it has been provided with. However, there are some scenarios that prevent some tracker trip history data from being displayed in tracking history sections (widgets).

Usually, it is enough to disable the **Split by stops** and **Smart filter** options to show "raw trip data" without filters. These two filters can drastically change the way trip history is displayed in the tracking module. However, if you see no points after disabling both options, you might want to review and analyze raw data using the [Raw Data](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/access-iot-data) feature. There may be no points provided by the device for the given time span.

First, the Parking Detection system is applied to the points delivered by the tracker. The Parking Detection system is a very important part of the trip history logic. It may filter a big part of trips in cases of bad Parking Detection configuration or inconsistent data provided by a tracker. To find out more about the logic, refer to the documentation on [Parking Detection](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/location-and-movement/parking-detection-block). In short, the logic is as follows.

There are parameters defined in the **Parking detection** block (Devices & Settings):

* Minimum idle detection time (**min\_parking**): the minimum amount of time that the object must remain stationary before it is considered parked.
* Maximum idle speed (**min\_speed**): the speed threshold under which the object must stay to be recognized as parked.

By default, these parameters are set to 5 minutes and 3 km/h.

Parking status is detected when the object's speed drops below the defined min\_speed and stays there for longer than min\_parking. Stops shorter than min\_parking are not considered parking and will not interrupt the trip.

Based on the logic above, trip history can be displayed differently (difference in tracking history gaps up to complete absence of the trip in the widget).

The second thing to keep in mind is Smart Filter. It filters (drops) points from trips formed by Parking Detection which:

* are shorter than 300 meters
* have fewer than 4 points
* have a small radius (the tracker drives around one point, so all points are close to each other)

To summarize:

At the first step, the **Split by stops** option is used to form trips. Then, if both options are selected in the widget (see the screenshot below), the **Smart Filter** logic is applied to the resulting trips. ![](<../.gitbook/assets/Unknown image (17)>)

Disable both options to see raw trip data.

If you need to manually verify all algorithm calculations and ensure the described processes work as expected, you can use the Raw Data feature.

Learn more about [exporting](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/access-iot-data/save-iot-data-to-csv-file) and [interpreting](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/access-iot-data/save-iot-data-to-csv-file/columns-in-csv-file) Raw Data.

## Links

* [Raw data block](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/object-management/raw-data-block)
* [Exporting Raw Data](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/access-iot-data/save-iot-data-to-csv-file)
* [Interpreting Raw Data](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/access-iot-data/save-iot-data-to-csv-file/columns-in-csv-file)
