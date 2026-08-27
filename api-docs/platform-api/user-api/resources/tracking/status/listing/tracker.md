---
title: Tracker's working status list
description: Assign a working status list to a tracker, or remove the assignment.
---

# Tracker's working status list

This operation links a tracker to a [working status list](README.md), which is what makes any working status available to that tracker at all. A tracker with no list assigned has no statuses it can be set to.

Omit `listing_id` entirely to remove the assignment rather than change it.

## API actions

API base path: `/status/listing/tracker`.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/status/listing/tracker/assign" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the authorized user has no tracker with the given ID.
* 204 - Entity not found, when no working status list has the given ID.
* 208 - Device blocked, when the tracker exists but is blocked, whether by tariff restrictions or for another reason.
* 219 - Not allowed for clones of the device, when the given tracker is a clone.
* 236 - Feature unavailable due to tariff restrictions, when no tracker on the account has the `statuses` tariff feature.
