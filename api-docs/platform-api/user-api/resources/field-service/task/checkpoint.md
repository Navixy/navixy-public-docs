---
title: Checkpoints
description: The individual stops that make up a route, each essentially a task.
---

# Checkpoints

A checkpoint is one stop on a [route](route/README.md). It is essentially a [task](README.md) with an extra link to the parent route, so it carries the same fields and completes the same way.

These operations work on checkpoints individually, which is what changing one stop on an existing route needs. Creating a route creates its checkpoints with it.

## Checkpoint object

{% openapi-schemas spec="navixy-platform" schemas="TaskCheckpoint" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

For the attached `form`, see the [form object](../form/README.md#form-object).

## API actions

API base path: `/task/checkpoint`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

For the `external_id_counts` field in the response, see the `task/route/create` [method description](route/README.md#post-task-route-create).

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/checkpoint/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if task.tracker\_id is not null and belongs to nonexistent tracker.
* 236 - Feature unavailable due to tariff restrictions - if device's tariff does not allow usage of tasks.

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/checkpoint/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no checkpoint with such an ID.

***

{% openapi-operation spec="navixy-platform" path="/task/checkpoint/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/task/checkpoint/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no checkpoint with such an ID.

***

If the converted checkpoint was the only one in its route, the route is deleted.

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/checkpoint/transmute" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no checkpoint with such an ID, or tracker to which checkpoint assigned is unavailable to current sub-user.
* 255 - Invalid task state - if any of checkpoints are not in unassigned or assigned state.

***

Changing `order` reorders all other checkpoints.

For the `external_id_counts` field in the response, see the `task/route/create` [method description](route/README.md#post-task-route-create).

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/checkpoint/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no task with such an ID.
* 255 - Invalid task state - if current task state is not "unassigned" or "assigned".

