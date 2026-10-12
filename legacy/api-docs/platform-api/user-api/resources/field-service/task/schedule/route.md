---
title: Route schedules
description: Scheduled routes, created on a repeating basis like recurring tasks.
---

# Route schedules

These operations schedule [routes](../route/README.md) the same way [recurring tasks](README.md) schedule single tasks: the Navixy platform creates the route and its checkpoints on each day the schedule is due.

## Route schedule entry

{% openapi-schemas spec="navixy-platform" schemas="RouteScheduleEntry" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## Checkpoint schedule entry

{% openapi-schemas spec="navixy-platform" schemas="CheckpointScheduleEntry" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

The `parameters` of a route schedule are the [schedule parameters](README.md#task-schedule-entry-object) described on the recurring tasks page: `weekdays` for creation on days of the week, where 1 is Monday and 7 is Sunday, or `month_days` for creation on days of the month, from 1 to 31.

## API actions

API base path: `/task/schedule/route`.

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/schedule/route/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* [General](../../../../../general/errors.md#error-codes) types.

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/schedule/route/delete" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* [General](../../../../../general/errors.md#error-codes) types.

***

If a checkpoint is being created, then it should have no id. If a checkpoint is being updated, then it should have an ID. If an old checkpoint is not present in the request, then it will be deleted.

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/schedule/route/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* [General](../../../../../general/errors.md#error-codes) types.

