---
title: Chat
description: Text messages exchanged between a supervisor and the person carrying a tracker.
---

# Chat

Chat carries text messages between a supervisor and whoever is holding the tracker, usually through the mobile application on the same device. It gives a dispatcher a way to reach a driver without a phone call, and leaves a record of what was said.

Each message records its direction, its delivery status, and the employee it is associated with.

## API actions

API base path: `/tracker/chat`.

Date and time values follow the [platform formats](../../../../general/api-conventions.md#date-time-formats).

***

{% openapi-operation spec="navixy-platform" path="/tracker/chat/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - The device does not support this operation.
* 236 - The tariff does not include the chat feature.

***

{% openapi-operation spec="navixy-platform" path="/tracker/chat/mark_read_all" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

{% openapi-operation spec="navixy-platform" path="/tracker/chat/mark_read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No such message.

***

{% openapi-operation spec="navixy-platform" path="/tracker/chat/send" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - The device does not support this operation.
* 236 - The tariff does not include the chat feature.

***

{% openapi-operation spec="navixy-platform" path="/tracker/chat/broadcast" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 217 - The list names trackers that do not exist, are blocked, or lack the required tariff feature.
* 221 - The dealer's device limit is exceeded.

***

{% openapi-operation spec="navixy-platform" path="/tracker/chat/updated/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 217 - The list names trackers that do not exist, are blocked, or lack the required tariff feature.
* 221 - The dealer's device limit is exceeded.

***

{% openapi-operation spec="navixy-platform" path="/tracker/chat/unread/count" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 236 - No tracker on the account has a tariff including the chat feature.
