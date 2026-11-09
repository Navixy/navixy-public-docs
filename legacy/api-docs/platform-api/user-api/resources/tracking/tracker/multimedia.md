---
title: Video
description: "List, download and stream video from a tracker's cameras: event clips, memory card recordings, and live video."
---

# Video

Trackers with cameras record video to their own memory card. These operations list the recordings, upload the ones you need to Navixy, and download them. They also start and stop a live stream.

Two device families support video: Jimi JC400 and JC261 dashcams and most Howen video recorders. To check a model, look for `multimedia` in its entry in [`tracker/list_models`](README.md#post-tracker-list_models): it lists the model's cameras and video features. For other models, most of these operations return error 214.

Every operation needs the `video_monitoring` right. Master users have it. A sub-user needs a [security group](../../commons/subuser/security_group.md) that grants it.

For the steps to get event clips and memory card recordings, see [Get dashcam video](../../../guides/data-retrieval/dashcam-video.md).

{% hint style="warning" %}
Every upload and live stream uses the camera's mobile data. Request the recordings you need rather than whole days.
{% endhint %}

## Video object structure

{% openapi-schemas spec="navixy-platform" schemas="VideoFile,TrackerFile,VideoInterval,VideoCoverage,VideoStream" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/tracker/multimedia`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/multimedia/video/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The user lacks the `video_monitoring` right.
* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 211 - The period is longer than 120 days.
* 214 - The device model has no video support, or lacks one of the requested cameras.

***

{% hint style="info" %}
An upload takes time. Repeat the same request until `has_files_in_progress` is `false`.
{% endhint %}

{% openapi-operation spec="navixy-platform" path="/tracker/multimedia/video/search" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 8 - The request could not be passed to the device. Retry later.
* 13 - The user lacks the `video_monitoring` right.
* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - The device model has no video support, or lacks one of the requested cameras.

***

{% openapi-operation spec="navixy-platform" path="/tracker/multimedia/video/interval/search" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The user lacks the `video_monitoring` right.
* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 211 - The period is longer than 120 days.

***

{% openapi-operation spec="navixy-platform" path="/tracker/multimedia/file/search/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 8 - The request could not be passed to the device. Retry later.
* 13 - The user lacks the `video_monitoring` right.
* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 213 - The device is offline.
* 214 - The device model can't report its files.

***

{% openapi-operation spec="navixy-platform" path="/tracker/multimedia/video/live_stream/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 8 - The request could not be passed to the device. Retry later.
* 13 - The user lacks the `video_monitoring` right.
* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 213 - The device is offline.
* 214 - The device model has no live video, or lacks one of the requested cameras.

***

{% openapi-operation spec="navixy-platform" path="/tracker/multimedia/video/live_stream/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 8 - The request could not be passed to the device. Retry later.
* 13 - The user lacks the `video_monitoring` right.
* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 213 - The device is offline.
* 214 - The device model has no live video, or lacks one of the requested cameras.
