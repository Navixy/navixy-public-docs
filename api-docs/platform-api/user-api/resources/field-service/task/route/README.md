---
title: Route
description: A named, ordered set of checkpoints that must be visited in sequence.
---

# Route

A route is a named, ordered set of [checkpoints](../checkpoint.md#checkpoint-object), each of which is essentially a [task](../README.md) with a link back to the route.

Order is what separates a route from a set of separate tasks. A route completes only when every checkpoint has completed **and** they were visited in the specified sequence. Otherwise it completes with warnings, or fails.

## Route object

{% openapi-schemas spec="navixy-platform" schemas="TaskRoute" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/task/route`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/route/assign" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no task with such an ID.
* 204 - Entity not found - if there is no tracker with such ID belonging to authorized user.
* 208 - Device blocked - if tracker exists but was blocked due to tariff restrictions or some other reason.
* 255 - Invalid task state - if current task state is not "unassigned" or "assigned".
* 236 - Feature unavailable due to tariff restrictions - if device's tariff does not allow usage of tasks.

***

Give the checkpoints in order of execution. Their `from` and `to` must be agreed with each other, so a checkpoint's `to` cannot be before the `from` of a preceding one.

One of the checkpoints can have an ID, in which case it must be a task, and it will be transmuted from task to checkpoint.

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/route/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if task.tracker\_id is not null and belongs to nonexistent tracker.
* 236 - Feature unavailable due to tariff restrictions - if device's tariff does not allow usage of tasks.

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/route/delete" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no route with such an ID.

***

{% openapi-operation spec="navixy-platform" path="/task/route/list" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/task/route/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no route with such an ID.

***

Note that you cannot change task owner using this method. Reordering checkpoint IDs in the `checkpoint_ids` array changes order of execution.

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/route/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no task with such an ID.
* 255 - Invalid task state - if current task state is not "unassigned" or "assigned".

## More in this section

<!-- endpoint-reference:start -->

#### Route optimization

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/route/points/optimize`](optimize.md#post-task-route-points-optimize) | POST | Optimize route points |

<!-- endpoint-reference:end -->
