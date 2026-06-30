---
title: How to troubleshoot Jimi JC400 video streaming and playback
description: "Fix JC400 video streaming: verify IMEI, SIM connectivity, firmware version, APN settings, and server configuration. Includes manual command setup for EU and US"
---

# How to troubleshoot Jimi JC400 video streaming and playback

## Question

How to set up and troubleshoot Jimi JC400 video streaming and playback?

## Answer

Follow these steps in order:

1.  Check that the IMEI is correct.

    ![](<../.gitbook/assets/Unknown image (145)>)
2.  Check that the SIM supports SMS and has working GPRS/LTE.

    Make sure that:

    * the device can receive SMS (if you plan to configure via SMS)
    * GPRS/LTE traffic service is available
    * GSM coverage is sufficient for RTMP traffic

    <div data-gb-custom-block data-tag="hint" data-style="info" class="hint hint-info"><p>SMS service is often not available on M2M SIM cards.</p></div>
3.  Check firmware with `VERSION`.

    Supported examples:

    * JC400 (JC400A): `[VERSION]KMC28_JC400_WABA_STD_V4.3.2_220328.1750,[BUILD]2022-03-28 14:50`
    * JC400D: `[VERSION]JC400D_WAVA_DMS_V4.2.13_210716.2013_BUILD_2021-07-16`
4. If the firmware is old, update via the SD card `update.zip`.
   * [Download](https://drive.google.com/file/d/1Y56Tk5Xv3KGh-7J6XPdzs6tQUKqEglca/view?usp=sharing) and copy `update.zip` to the device SD card (don't unpack it).
   * Insert the SD card into the device and power it on.
   * The device updates firmware automatically.
5.  Configure the timezone UTC+0 and the APN + server settings.

    EU server IP: `52.57.1.136`

    US server IP: `13.52.37.2`

    Send commands one by one.

    Substitute `{apn}`, `{apn_user}`, `{apn_pass}` (without braces).

    EU:

    ```
    COREKITSW,0#
    SERVER,0,52.57.1.136,47755#
    APN,{apn},{apn},,,,,,{apn_user},,{apn_pass},,,,#
    ```

    US / Latin America:

    ```
    COREKITSW,0#
    SERVER,0,13.52.37.2,47755#
    APN,{apn},{apn},,,,,,{apn_user},,{apn_pass},,,,#
    ```

    If APN user/pass must be blank, leave fields empty:

    ```
    APN,{apn},{apn},,,,,,,,,,,,,#
    ```
6.  Retry activation in the UI when online.

    ![](<../.gitbook/assets/Unknown image (146)>)
7. If the device is offline unexpectedly, verify it is powered on and has sufficient battery charge.

## If default commands are disabled

If "send default settings" is disabled in Admin Panel, or activation retry doesn't push settings, send the following commands one by one.

Substitute `{IMEI}` with your device IMEI (no braces).

EU:

```
COREKITSW,0
UPLOAD,http://52.57.1.136:7514/upload/{IMEI}
FILELIST,http://52.57.1.136:7514/filelist/{IMEI}
RSERVICE,rtmp.navixy.com:1935/encoder
TIMER,ON,60
ANGLEREP,ON,10
SOSALM,ON,0
UPLOADSW,SOS,ON
UPLOADSW,CRASH,ON
UPLOADSW,RAPIDACC,ON
UPLOADSW,RAPIDDEC,ON
UPLOADSW,RAPIDTURN,ON
SERVER,0,52.57.1.136,47755#
```

US / Latin America:

```
COREKITSW,0
UPLOAD,http://13.52.37.2:7514/upload/{IMEI}
FILELIST,http://13.52.37.2:7514/filelist/{IMEI}
RSERVICE,rtmp.navixy.com:1935/encoder
TIMER,ON,60
ANGLEREP,ON,10
SOSALM,ON,0
UPLOADSW,SOS,ON
UPLOADSW,CRASH,ON
UPLOADSW,RAPIDACC,ON
UPLOADSW,RAPIDDEC,ON
UPLOADSW,RAPIDTURN,ON
SERVER,0,13.52.37.2,47755#
```

## Links

* [Air Console](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/devices/air-console)
