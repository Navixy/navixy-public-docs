---
title: Notification
description: Platform announcements shown to the current user.
---

# Notification

This operation lists the Navixy platform's own messages to the user, such as service announcements.

These are not tracker events. Anything a device did, or a rule raised, is [event history](history/README.md) instead.

## API actions

API base path: `/notification`.

Operations that list no errors of their own return only the [general error codes](../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/notification/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
