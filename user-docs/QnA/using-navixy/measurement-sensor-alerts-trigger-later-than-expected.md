---
title: Measurement sensor alerts trigger later than expected
description: Switch measurement sensors to virtual sensors to prevent data thinning from delaying alert triggers; thinned data skips intermediate values
---

# Measurement sensor alerts trigger later than expected

## Question

Why does the feature related to measurement sensors provide inaccurate results? The **Parameter in range** alert triggers later than it should.

## Answer

In some cases, the **Parameter in range** alert notifications may appear with a delay or trigger later than expected. It may happen in cases where the rule is tied to a measurement sensor as a source for values.

The reason why this may happen is that all measurement sensors are optimised in terms of data thinning. To resolve the data thinning situation, you can use Virtual sensors, which are not optimised the same way and will be triggered by a 'State field value' rule right away.

1.  Create a virtual sensor and specify the same source parameter for data as in your old measurement sensor. Select 'Calculation method' as Value in range.

    ![](<../.gitbook/assets/Unknown image (25)>)
2.  Create a 'State field value' rule and specify the virtual sensor for state source data:

    ![](<../.gitbook/assets/Unknown image (113)>)

## Links

* [Measurement sensors](https://app.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/vehicle-sensors/measurement-sensors)
* [Virtual sensors](https://app.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/vehicle-sensors/virtual-sensors)
* [State field value](https://app.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/s/446mKak1zDrGv70ahuYZ/guide/events-and-notifications/inputs-and-outputs/state-field-value)
* [Parameter in range](https://app.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/s/446mKak1zDrGv70ahuYZ/guide/events-and-notifications/inputs-and-outputs/parameter-in-range)