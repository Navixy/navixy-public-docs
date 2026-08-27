---
title: Sensor calibration data
description: The curve that converts a sensor's raw readings into meaningful units.
---

# Sensor calibration data

Calibration data converts what a sensor actually reports into what a reader wants to see. An analogue fuel sensor reports volts, and the calibration curve turns those volts into litres. Without it a reading is a raw number with no useful meaning.

Updating replaces the whole curve rather than adding points to it. For Omnicomm sensors a third operation takes the manufacturer's own calibration file directly.

## API actions

API base path: `/tracker/sensor/calibration_data`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/sensor/calibration_data/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 228 - The sensor does not support calibration.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/sensor/calibration_data/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 219 - The tracker is a clone.
* 228 - The sensor does not support calibration.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/sensor/calibration_data/upload_omnicomm" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 219 - The tracker is a clone.
* 228 - The sensor does not support calibration.
* 233 - No file part was sent.
* 234 - The file is not a valid LLS monitor XML file.
* 235 - The file holds no calibration data for that sensor number.
