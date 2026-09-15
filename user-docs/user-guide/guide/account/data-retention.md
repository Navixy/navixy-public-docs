---
description: >-
  How long Navixy keeps your tracking history, events, and video recordings, and
  how far back you can request a recording from a camera.
---

# Data retention

This page explains how long Navixy keeps the data that your GPS devices produce, and how far back you can look. It covers tracking history, events, and video recordings. It applies to every user of the Navixy platform, whatever devices you use.

## How long Navixy keeps your data

Navixy keeps your stored history for 3 years. The period is the same for every kind of data that your devices produce:

| Data | Retention period |
| ---- | ---------------- |
| Tracking history, including positions and trips | 3 years |
| Sensor readings | 3 years |
| Counters, such as odometer and engine hours | 3 years |
| Event history, including the events behind your alerts | 3 years |

Navixy measures the period from the moment each record arrives, not from the date when you look at it. A trip recorded 2 years ago is still available. The same trip stops being available 3 years after it happened.

Video recordings follow different rules because a recording starts on the camera rather than on the Navixy platform. For details, see [Video recordings](data-retention.md#video-recordings).

## How your plan affects what you can see

Your plan sets how far back you can look in the platform, and this period can be shorter than 3 years. Your service provider chooses it for each plan. If the earliest date you can open in a report or a history view is less than 3 years back, your plan sets that limit.

To find out the period that applies to you, or to ask for a longer period, contact your service provider.

{% hint style="info" %}
The period on your plan changes what you can open in the platform. It doesn't change how long Navixy keeps the data.
{% endhint %}

## Video recordings

A video recording can be in two places, and each place has its own rules. The camera records to its own memory. Navixy holds a copy only after the recording is uploaded.

### Requesting a recording from a camera

You can request a recording from up to 30 days in the past, and only while the camera still stores it. Both conditions apply. A recording from last week is unavailable if the camera has already overwritten it, and a recording that the camera still holds is unavailable if it's older than 30 days.

Cameras overwrite their own memory as they record, so how far back a given camera reaches depends on the size of its storage and on how much it records. A camera in a vehicle that runs every day holds fewer days than a camera in a vehicle that runs twice a week.

To see what a camera still holds, open the **Video monitoring** window from the **Object widget**. The date selector marks the dates that hold video, and distinguishes recordings still on the camera from recordings already in Navixy. For the steps, see [Video playback](../tracking/objects-list/object-widget.md#video-playback).

### Recordings already uploaded to Navixy

A recording that you uploaded to Navixy no longer depends on the camera. You can open it after the camera overwrites its own copy, and after the 30-day request window passes.

An upload uses the mobile data of the camera. Request the fragments that you need rather than a whole day.

### Video events

A video event is an event record with a recording attached to it. The two parts are kept separately:

* The event record follows the retention period in [How long Navixy keeps your data](data-retention.md#how-long-navixy-keeps-your-data), the same as any other event.
* The recording attached to it follows the video rules in this section.

An event from a year ago is still in your event history. You can still watch the recording attached to it only if that recording reached Navixy. To open a recording from the event history, see [Event-triggered video monitoring](../tracking/history-view/event-history.md#event-triggered-video-monitoring).

## Data Stream Analyzer has a separate 30-day limit

Data Stream Analyzer keeps the last values of a device for up to 30 days after its most recent message, and this limit is separate from the retention of your history. Data Stream Analyzer shows a small cache for diagnostics. When Navixy drops that cache, your tracking history and events stay unchanged. For details, see [Data availability](iot-logic/data-stream-analyzer.md#data-availability).

## See also

* [Video playback in the Object widget](../tracking/objects-list/object-widget.md#video-playback)
* [Event history](../tracking/history-view/event-history.md)
* [Data Stream Analyzer](iot-logic/data-stream-analyzer.md)
