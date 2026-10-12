---
title: Settings
description: A tracker's display label and the group it belongs to.
---

# Settings

These two operations cover the tracker's own presentation: the label shown for it in the interface, and the [group](../group.md) it sits in.

Everything else under settings is about the device rather than the record: [tracking mode](tracking.md), [parking detection](trip_detection.md), [LBS](lbs.md), and the model-specific [special settings](special/README.md).

## API actions

API base path: `/tracker/settings`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/settings/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/settings/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 204 - No group with this ID.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

## More in this section

<!-- endpoint-reference:start -->

#### LBS settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/lbs/read`](lbs.md#post-tracker-settings-lbs-read) | POST | Read the LBS radius limit |
| [`/tracker/settings/lbs/update`](lbs.md#post-tracker-settings-lbs-update) | POST | Change the LBS radius limit |

#### Tracking mode

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/tracking/read`](tracking.md#post-tracker-settings-tracking-read) | POST | Read tracking mode settings |
| [`/tracker/settings/tracking/update`](tracking.md#post-tracker-settings-tracking-update) | POST | Change tracking mode settings |

#### Parking detection

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/trip_detection/read`](trip_detection.md#post-tracker-settings-trip_detection-read) | POST | Read parking detection settings |
| [`/tracker/settings/trip_detection/update`](trip_detection.md#post-tracker-settings-trip_detection-update) | POST | Change parking detection settings |

#### Special

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/special/read`](special/README.md#post-tracker-settings-special-read) | POST | Read special settings |
| [`/tracker/settings/special/update`](special/README.md#post-tracker-settings-special-update) | POST | Change special settings |

<!-- endpoint-reference:end -->
