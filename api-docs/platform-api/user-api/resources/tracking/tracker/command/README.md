---
title: Unconfirmed commands
description: The SMS commands still queued for a tracker, and a way to clear the queue.
---

# Unconfirmed commands

A command sent to a device by SMS waits in a queue until the device confirms it. These two operations work on that queue: one counts what is still waiting, the other clears it.

Clearing discards the commands rather than sending them, so anything the device was waiting for never arrives.

## API actions

API base path: `/tracker/command/unconfirmed`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/command/unconfirmed/count" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/command/unconfirmed/reset" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
