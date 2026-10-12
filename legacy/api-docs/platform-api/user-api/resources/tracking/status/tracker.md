---
title: Tracker's working status
description: Read and set the working status a particular tracker is currently in.
---

# Tracker's working status

These operations work on the current status of a tracker, rather than on the [statuses](README.md) and [lists](listing/README.md) that define what it can be set to.

A tracker can only be set to a status from the list assigned to it, and only when that list is supervisor-controlled. A list that only its own employee may change returns error 13 to a supervisor attempting the change.

Setting a tracker to the status it already holds returns error 263 rather than succeeding silently.

## API actions

API base path: `/status/tracker`.

***

{% openapi-operation spec="navixy-platform" path="/status/tracker/assign" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - Operation not permitted, when the status list does not allow a supervisor to change the status.
* 201 - Not found in the database, when the authorized user has no tracker with the given ID.
* 204 - Entity not found, when the list assigned to the tracker holds no status with the given ID.
* 208 - Device blocked, when the tracker exists but is blocked, whether by tariff restrictions or for another reason.
* 219 - Not allowed for clones of the device, when the given tracker is a clone.
* 236 - Feature unavailable due to tariff restrictions, when no tracker on the account has the `statuses` tariff feature.
* 263 - No change needed, old and new values are the same, when the new status matches the tracker's current one.

***

{% openapi-operation spec="navixy-platform" path="/status/tracker/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 217 - List contains nonexistent entities, when one of the given tracker IDs belongs to no tracker, or to a tracker owned by another user.
* 221 - Device limit exceeded, when the device limit set for the user's dealer has been exceeded.
* 236 - Feature unavailable due to tariff restrictions, when no tracker on the account has the `statuses` tariff feature.

***

{% openapi-operation spec="navixy-platform" path="/status/tracker/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the authorized user has no tracker with the given ID.
* 208 - Device blocked, when the tracker exists but is blocked, whether by tariff restrictions or for another reason.
* 236 - Feature unavailable due to tariff restrictions, when no tracker on the account has the `statuses` tariff feature.
