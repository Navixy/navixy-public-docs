---
title: My Jimi JC tracker is configured but I can't see video playback
description: Send three SMS commands to the Jimi JC tracker to enable video uploads, retrieve the list of stored recordings, and trigger the upload
---

# My Jimi JC tracker is configured but I can't see video playback

## Question

Why can't I see video playback from my Jimi JC tracker?

## Answer

If your JC-series tracker (like the JC261) is connected but you're not seeing any video playback, it's likely because the device hasn't been told to upload recorded videos yet. No worries — this is easy to fix.

To make videos visible on the platform, just send these three commands to your tracker (in this exact order) via SMS or AirConsole:

* `COREKITSW,0#` Enables video uploads to the platform.
* `FILELIST,http://{server}:7514/filelist` Lets the device send a list of videos stored on the SD card. These show up in gray on the calendar and timeline.
* `UPLOAD,http://{server}/upload` Tells the tracker to actually upload selected videos. These will appear in blue and stay available for 30 days.

Replace {server} with the IP address of your platform:

* US: 13.52.37.2
* EU: 52.57.1.136

Don't forget to add your device's IMEI number.

**Example:** For a tracker with IMEI 88887777 connected to the US server, the commands would be:

```
COREKITSW,0# FILELIST,http://13.52.37.2:7514/filelist/88887777 
UPLOAD,http://13.52.37.2/upload/88887777
```

Make sure your tracker is online to receive these commands over GPRS. If it's offline, SMS will work too as long as the SIM supports it.

## Links

* [Air Console](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/devices/air-console)
* [Jimi IoT dashcam configuration](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/vehicle-telematics-technology/video-telematics/configuration-guides/jimi-iot/jimi-iot-dashcam-configuration)
