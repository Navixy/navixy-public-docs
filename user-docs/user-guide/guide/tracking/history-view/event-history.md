---
description: >-
  View a detailed log of fleet events by object and date range. Filter by type,
  review timestamps and locations, and play back event-triggered video
  recordings.
---

# Event history

The **Event** tab in **History** provides a detailed log of all events related to selected objects over the specified period. These events vary depending on the type of GPS device and monitored activities, offering a comprehensive view of your fleet's operational status and any issues with the tracked objects.

{% hint style="info" %}
**Navigation**

To see the event history for an object, select it and click <img src="../../../.gitbook/assets/Untitled-20250414-131158.png" alt="Untitled-20250415-082401.png" data-size="line">. You are prompted to select a time and date range. After choosing the desired period, proceed to the **History** window and select the **Events** tab.
{% endhint %}

{% columns fullWidth="true" %}
{% column %}
![History date range selection](../../../.gitbook/assets/image-20240807-220921.png)
{% endcolumn %}

{% column %}
![Events list](../../../.gitbook/assets/image-20240808-192325.png)
{% endcolumn %}
{% endcolumns %}

To view events for all devices, click the Alerts tab on the sidebar:

![Alerts module](../../../.gitbook/assets/image-20241113-192802.png)

## Events list

Select a date range to see a summary of events for the chosen objects, grouped by object. The icons and numbers below each object represent the most common event types during the selected period. You can click the icons to filter the list by event type.

By default, each entry’s events are collapsed. Click <img src="../../../.gitbook/assets/image-20250415-083354.png" alt="image-20250415-083354.png" data-size="line"> to the right to expand the view.

## Event details

By clicking on an event in the events list, you will see the following information:

* **Event description**: The notification message created in the **Alert** module. It provides details about the specific event.
* **Location**: The place where the event occurred, if applicable.
* **Timestamp**: The date and time the event occurred.
* **Video monitoring:** A video recording of the event, if available and uploaded into the cloud. Only available for devices with this functionality.
* **Additional details:** Further information depending on the type of monitored event, such as a temperature threshold.

## Event types

The types of events displayed are determined by the events being monitored. Navixy can detect and log a wide range of events, from basic movement and stop notifications to more complex sensor alerts and maintenance reminders.\
For more details on setting up and managing these events, see [Rules and Alerts](../../events-and-notifications/).

## Event-triggered video monitoring

Video recording enhances the event history by providing visual context for significant incidents. When video-capable devices detect events such as accidents or harsh driving behavior, Navixy captures footage before, during, and after the occurrence.

To watch event recordings, follow these steps:

1. Click the video icon associated with the desired event.
2. Navixy launches the video player with the recorded content.
3. Use the player controls:

* **Start/Pause**: Initiate or pause video playback.
* **Restart**: Reset playback time across all camera channels.
* **Audio management**: Adjust the volume and toggle audio on and off.
* **Download options**: Access the download functionality.

During video playback, the device's movement pattern displays on the right side of the video module, providing comprehensive spatial context that combines visual events with corresponding vehicle location and movement data.

{% hint style="danger" %}
Video monitoring requires compatible devices with camera support and may consume significant bandwidth during streaming and download operations.
{% endhint %}
