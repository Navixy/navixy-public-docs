---
title: I can't see video events on my Jimi JC trackers
description: Send the UPLOADSW command to the device to configure event-based video uploads, then create a corresponding alert rule in the platform
---

# I can't see video events on my Jimi JC trackers

## Question

I can't see video events on my Jimi JC trackers

## Answer

To make sure important moments like crashes or fatigue warnings are captured and saved, you can configure your JC-series tracker to automatically upload video clips when certain events occur. Here’s how to set it up:

## Step 1: Send the video upload command

Use the following command format:

`UPLOADSW,,`

* The first value is the event type code (e.g., SOS, CRASH, DRIVE) — you’ll find the full list in the table below.
* The second value controls what kind of video is uploaded:
  * `ON` — upload both front and inner camera video
  * `1` — upload only front camera
  * `2` — upload only inner camera
  * `OFF` — don’t upload video for this event

**Example:** If you want to record both front and inner camera footage when the SOS button is pressed, the command would be:

`UPLOADSW,SOS,ON`

You can repeat this command for any event you want to track — like crashes, harsh driving, fatigue, or distractions.

## Step 2: Don’t forget the alert rule in the platform

Sending the command isn’t enough by itself. You also need to create a matching alert rule in the platform for the event.

If you skip this, the device will still upload video, but it won’t show up in the user interface — and you’ll lose access to those clips.

## Bonus tip: Save data by disabling video for less important alerts

You can reduce SIM data usage by turning off video uploads for alerts you don’t need. Send the same `UPLOADSW` command with `OFF` as the setting.

**Example:** This would disable video uploads triggered by the vibration sensor.

## Links

* [Jimi IoT dashcam configuration](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/vehicle-telematics-technology/video-telematics/configuration-guides/jimi-iot/jimi-iot-dashcam-configuration)
* [Rules and alerts](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/events-and-notifications)
