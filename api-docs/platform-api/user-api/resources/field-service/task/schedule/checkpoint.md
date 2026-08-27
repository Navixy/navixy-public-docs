---
title: Task schedule checkpoints
description: Delete or convert a checkpoint on a scheduled route.
---

# Task schedule checkpoints

These operations work on the checkpoints of a [scheduled route](route.md), the same way [checkpoints](../checkpoint.md) work on an ordinary one.

A route schedule must keep at least two checkpoints. When only two remain, convert the other checkpoint with `transmute` rather than deleting this one.

## API actions

API base path: `/task/schedule/checkpoint`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

A route schedule must keep at least two checkpoints. If the route has only two left, use transmute on the other checkpoint rather than deleting this one.

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/schedule/checkpoint/delete" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/schedule/checkpoint/transmute" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
