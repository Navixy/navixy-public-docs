---
title: Push token
description: Bind a device's push notification token to the current session, and filter what it receives.
---

# Push token

A push token identifies one installation of a mobile application to its push service. Binding it to the current session is what lets the Navixy platform deliver notifications to that device.

A binding belongs to the session, so signing out and back in means binding again. For a walkthrough, see [how to receive push notifications in your app](../../../../guides/rules-notifications/get-push-notifications.md).

## Category filter

`category_filter` decides which notification categories reach the device:

* `*` allows every category.
* A comma-separated list allows only the categories named, for example `chat_message,history_rule`.
* A comma-separated list prefixed with a minus allows every category except those named, for example `-history_task,history_rule`.

The categories are:

* `chat_message` - a new chat message.
* `history_rule` - a rule firing.
* `history_task` - anything about a task.
* `history_info` - service information.
* `history_service_task` - a service task.
* `history_work_status` - a working status change.

## API actions

API base path: `/user/session/push_token`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/user/session/push_token/bind" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/user/session/push_token/delete" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the session has no token bound.
