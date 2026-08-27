---
title: LBS settings
description: The maximum radius accepted for a tracker's cell-tower and Wi-Fi positions.
---

# LBS settings

LBS, or location-based service, positions a device from cell towers and Wi-Fi access points instead of from GNSS such as GPS, GLONASS, Galileo, or BeiDou. It works where satellites do not, indoors or in dense cities, but its points carry far more uncertainty than a satellite fix.

That uncertainty is what these operations control. The setting is the maximum radius accepted for this tracker's LBS points, and it backs the LBS detection radius setting on the device's settings tab in the interface.

## API actions

API base path: `/tracker/settings/lbs`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/settings/lbs/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/settings/lbs/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
