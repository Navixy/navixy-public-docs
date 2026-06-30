---
title: My video device isn't showing the livestream
description: Send configuration commands via Air Console to enable the livestream feature and set the correct streaming server address on the device
---

# My video device isn't showing the livestream

## Question

How do I get my video to display the livestream?

## Answer

If your device isn’t showing the livestream, the most likely case is that it’s not configured correctly. Follow the guide below for instructions. Depending on the server you’re pointing to, the commands specific to the livestream you would send to the device via the Air Console are as follows:

* COREKITSW,0
* RSERVICE, rtmp.navixy.com:1935/encoder
* VIDEORESOLUTION\_SUB,0
* SERVER,0,**{server}**,47755#

With **{server}** being the US server (13.52.37.2) or EU server (52.57.1.136) IP address.

Make sure all commands are sent _in order_ as the COREKITSW,0 command is required, and the SERVER,0,**{server}**,47755# command is the required finisher command.

If your issue is due to lack of network connectivity, we recommend lowering the video quality by sending the following command to the device:

VIDEORESOLUTION\_SUB,0

## Links

* [Air Console](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/devices/air-console)
* [Jimi JC400 troubleshooting](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/vehicle-telematics-technology/video-telematics/configuration-guides/jimi-iot/jimi-jc400-troubleshooting)
