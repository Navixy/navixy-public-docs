---
title: Get dashcam video
description: How to get event clips and memory card recordings from a dashcam through the API, and what limits apply.
---

# Get dashcam video

A dashcam records to its own memory card. Navixy holds a copy of a recording only after the camera uploads it. This guide shows how to get the two kinds of video through the API:

* **Event clips.** Short clips that the camera records and uploads by itself when an event happens, such as a crash or an SOS alarm.
* **Memory card recordings.** The continuous recording on the camera's memory card. Navixy asks the camera to upload the part you request.

The operations used here are described on the [Video](../../resources/tracking/tracker/multimedia.md) page. They work with Jimi JC400 and JC261 dashcams and most Howen video recorders. To check a model, look for `multimedia` in its entry in [`tracker/list_models`](../../resources/tracking/tracker/README.md#post-tracker-list_models).

## Prerequisites

* **An API key.** See [Authentication](../../authentication.md). The examples send it in the `Authorization` header.
* **The `video_monitoring` right.** Master users have it. A sub-user needs a [security group](../../resources/commons/subuser/security_group.md) that grants it. Without the right, the video operations return error 13, and the history operations leave out the files.
* **The tracker ID** of the camera. See [Getting list of GPS trackers](get-tracker-list.md).
* **The camera names.** A Jimi dashcam has `front_camera` and `inward_camera`. A Howen video recorder has `channel_1`, `channel_2` and so on, up to its number of channels.

## Get event clips

An event clip is attached to the event it was recorded for. Request the event history with `add_tracker_files` set to `true`:

{% code title="cURL" overflow="wrap" %}
```shell
curl --request POST \
  --url 'https://api.eu.navixy.com/v2/history/tracker/list' \
  --header 'Authorization: NVX 22eac1c27af4be7b9d04da2ce1af111b' \
  --header 'Content-Type: application/json' \
  --data '{"trackers": [123456], "from": "2026-11-02 00:00:00", "to": "2026-11-02 23:59:59", "add_tracker_files": true}'
```
{% endcode %}

Each event has a `files` list when an uploaded clip starts within 10 seconds before or after it. Each file has a `download_url`:

```json
{
  "success": true,
  "list": [
    {
      "id": 987654321,
      "type": "tracker",
      "event": "crash_alarm",
      "time": "2026-11-02 09:15:07",
      "tracker_id": 123456,
      "message": "Truck 12: Crash",
      "files": [
        {
          "id": 556677,
          "type": "file",
          "name": "2026_11_02_09_15_00_01_861234567890123.mp4",
          "size": 2457600,
          "mime_type": "video/mp4",
          "state": "uploaded",
          "file_source_key": "front_camera",
          "download_url": "https://files.example.com/a1b2c3/2026_11_02_09_15_00_01_861234567890123.mp4"
        }
      ]
    }
  ]
}
```

Download the file from `download_url`. The link is valid for 7 days. After that, request the event again for a fresh link.

To list the clips without the events, call `tracker/multimedia/video/list` with `file_types` set to `["alarm_video"]`. A clip that the camera hasn't uploaded yet is listed without a `file`. The API can't ask the camera to upload such a clip: only the camera's own clip upload sends it.

{% hint style="info" %}
A Jimi dashcam uploads a clip only for the event types that have clip upload turned on in its settings. Navixy turns it on for SOS alarms when it sets up the dashcam. A Jimi clip is 15 seconds long by default.
{% endhint %}

## Get a memory card recording

A recording on the memory card reaches Navixy only when you request it. Use three steps: find what the camera has, request the recording, then download it.

{% stepper %}
{% step %}
### Find the periods with recordings

Call `tracker/multimedia/video/interval/search` for one camera and a period. Set `group_by` to `hour` to see the hours that have video:

{% code title="cURL" overflow="wrap" %}
```shell
curl --request POST \
  --url 'https://api.eu.navixy.com/v2/tracker/multimedia/video/interval/search' \
  --header 'Authorization: NVX 22eac1c27af4be7b9d04da2ce1af111b' \
  --header 'Content-Type: application/json' \
  --data '{"tracker_id": 123456, "cameras": ["front_camera"], "from": "2026-11-02 00:00:00", "to": "2026-11-02 23:59:59", "group_by": "hour"}'
```
{% endcode %}

```json
{
  "success": true,
  "list": [
    {
      "start": "2026-11-02 09:00:12",
      "end": "2026-11-02 09:59:59",
      "group_start": "2026-11-02 09:00:00",
      "files_locations": ["device"]
    }
  ]
}
```

`device` in `files_locations` means the recordings are on the camera only. `local_storage` means some are already uploaded to Navixy.

The list comes from the file list that the camera reports. Navixy asks for it every 12 hours. To get recordings newer than that, call `tracker/multimedia/file/search/create` first, while the camera is online, then repeat this step. A Howen video recorder reports only the last 13 hours on its own, so for an older period, pass that period as `start_date` and `end_date`.
{% endstep %}

{% step %}
### Request the recording

Call `tracker/multimedia/video/search` with a moment inside a period from the previous step:

{% code title="cURL" overflow="wrap" %}
```shell
curl --request POST \
  --url 'https://api.eu.navixy.com/v2/tracker/multimedia/video/search' \
  --header 'Authorization: NVX 22eac1c27af4be7b9d04da2ce1af111b' \
  --header 'Content-Type: application/json' \
  --data '{"tracker_id": 123456, "cameras": ["front_camera"], "date_time": "2026-11-02 09:15:30"}'
```
{% endcode %}

The first call asks the camera to upload the recording and returns at once:

```json
{
  "success": true,
  "data": [],
  "has_files_in_progress": true
}
```

The camera must be online, so check its connection with [`tracker/get_state`](../../resources/tracking/tracker/README.md#post-tracker-get_state) first. If it is offline, nothing reaches the camera. A Jimi dashcam's response then has `has_files_in_progress` set to `false`. A Howen video recorder's response has it set to `true` for the next 20 minutes, although the recorder received nothing.
{% endstep %}

{% step %}
### Wait for the upload and download the file

Repeat the same request every few seconds until `has_files_in_progress` is `false`. Stop after a few minutes if it stays `true`, and check that the camera is online. The response then has the recording for each camera:

```json
{
  "success": true,
  "data": [
    {
      "camera": "front_camera",
      "intervals": [
        {
          "start": "2026-11-02 09:15:00",
          "end": "2026-11-02 09:16:00",
          "file": {
            "id": 556690,
            "name": "2026_11_02_08_15_00_22_861234567890123_HVIDEO_HD.mp4",
            "mime_type": "video/mp4",
            "state": "uploaded",
            "file_source_key": "front_camera",
            "download_url": "https://files.example.com/d4e5f6/2026_11_02_08_15_00_22_861234567890123_HVIDEO_HD.mp4"
          }
        }
      ]
    }
  ],
  "has_files_in_progress": false
}
```

Download each file from `download_url`. If an upload gets no answer from the camera within 20 minutes, the next request asks again.
{% endstep %}
{% endstepper %}

Recordings come in 1-minute parts. For a longer period, request one moment in each minute you need. For a Jimi dashcam, use a moment inside the minute, such as `09:15:30`: a moment at second 00 also matches the previous recording, which ends at that second. For a Howen video recorder, use second 00, such as `09:15:00`, because it uploads from the moment you give to the end of that minute.

## Limits

* **Mobile data.** Every upload uses the camera's mobile data. Request the minutes you need rather than whole days.
* **How far back.** A Jimi dashcam with newer firmware, whose model code ends in `_fw163`, reports the recordings from the last 30 days. Other Jimi dashcams report the last 5 days. A Howen video recorder reports the period you ask for with `tracker/multimedia/file/search/create`. In both cases, only recordings still on the memory card can be uploaded. The camera overwrites its oldest recordings as it records, so how far back it reaches depends on the card's size and on how much the vehicle runs.
* **Download links last 7 days.** Request the file again for a fresh link.
* **Navixy keeps uploaded files for a limited time.** To keep recordings longer, download them to your own storage.
* **Request rate.** The API limits requests to 50 per second, per user and per IP address. See [API conventions](../../../general/api-conventions.md#request-rate-limits).

## Watch live video

To watch the camera live, call `tracker/multimedia/video/live_stream/create`. The response has a playlist address for each camera in [HLS](https://datatracker.ietf.org/doc/html/rfc8216) (HTTP Live Streaming) format, which any HLS player can open.

Stop the stream with `tracker/multimedia/video/live_stream/delete` when you no longer need it, because it uses the camera's mobile data while it runs. A Jimi dashcam stops both cameras, whatever the request lists. A Howen video recorder receives no stop request, although the call succeeds, so its stream runs until the recorder ends it.

## Errors

These are the errors you are most likely to meet in this guide. The [Video](../../resources/tracking/tracker/multimedia.md) page lists them per operation.

* **13**: the user lacks the `video_monitoring` right.
* **211**: the period is longer than 120 days.
* **213**: the camera is offline. `tracker/multimedia/file/search/create` and the live stream operations return it; `tracker/multimedia/video/search` doesn't.
* **214**: the model has no video support, or lacks one of the requested cameras.

## See also

* [Video](../../resources/tracking/tracker/multimedia.md): every video operation and object.
* [History](../../resources/commons/history/README.md): the event history and its `files`.
* [Security group](../../resources/commons/subuser/security_group.md): granting `video_monitoring` to a sub-user.
