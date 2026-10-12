---
title: Event type
description: Every event type the history can contain, with localized descriptions.
---

# Event type

This operation lists every event type an [event history](README.md) entry can carry, each with a human-readable description in the requested locale.

The values it returns are what the `events` filter on the history list operations accepts, and what the `event` field of a history entry holds. An integration that renders a history feed needs this list to label the entries it shows.

## API actions

API base path: `/history/type`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/history/type/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
