---
title: Task
description: A job assigned to a tracked device, completed by visiting a place and meeting its conditions.
---

# Task

A task is a job assigned to a tracked device. It completes when that device reaches the task's checkpoint at the right time and meets whatever else the task requires, such as filling a [form](../form/README.md) or staying in the task's zone for a set period. Otherwise it fails, or completes with warnings.

A task assigned to a device running the Mobile Tracker App, for [Android](https://play.google.com/store/apps/details?id=com.navixy.xgps.tracker\&hl=ru) or [iOS](https://apps.apple.com/us/app/x-gps-tracker/id802887190), is visible to the person carrying it, who is also notified when a task is assigned or changed.

Tasks that repeat are [scheduled](schedule/README.md) rather than created one by one, and tasks that must be done in sequence are grouped into a [route](route/README.md). For a walkthrough, see [how to create and assign tasks](../../../guides/field-service-management/manage-tasks.md).

## Task object

{% openapi-schemas spec="navixy-platform" schemas="Task" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

Custom field values are keyed by custom field ID as a string. See [entity/fields](../../commons/entity/fields.md). For the attached `form`, see the [form object](../form/README.md#form-object).

The `conditions` parameter of `task/list` takes [search conditions](../../commons/entity/search_conditions.md).

## API actions

API base path: `/task`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/assign" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database (if there is no task with such an ID).
* 204 - Entity not found (if there is no tracker with such ID belonging to authorized user).
* 208 - Device blocked (if tracker exists but was blocked due to tariff restrictions or some other reason).
* 255 - Invalid task state (if current task state is not "unassigned" or "assigned").
* 236 - Feature unavailable due to tariff restrictions (if device's tariff does not allow usage of tasks).

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/batch_convert" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/task/count" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% hint style="info" %}
`id` is assigned by the server and is always unique, so `task/create` called twice with identical parameters creates two tasks that differ only by their ID. There is no deduplication. If a task has to correspond to a record in an external system, store the ID this call returns against that record, because it is the only way to change or delete the right task later.
{% endhint %}

For the `external_id_counts` field in the response, see the `task/route/create` [method description](route/README.md#post-task-route-create).

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database (if task.tracker\_id is not null and belongs to nonexistent tracker).
* 236 - Feature unavailable due to tariff restrictions (if device's tariff does not allow usage of tasks).

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database (if there is no task with such an ID).

***

{% openapi-operation spec="navixy-platform" path="/task/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/task/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database (if there is no task with such an ID).

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/transmute" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database (if there is no task or route with such an ID, or tracker to which checkpoint assigned is unavailable to current sub-user).
* 255 - Invalid task state (if task or any of the checkpoints are not in unassigned or assigned state).

***

For the `external_id_counts` field in the response, see the `task/route/create` [method description](route/README.md#post-task-route-create).

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database (if there is no task with such an ID).
* 255 - Invalid task state (if current task state is not "unassigned" or "assigned").

## More in this section

<!-- endpoint-reference:start -->

#### Checkpoints

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/checkpoint/create`](checkpoint.md#post-task-checkpoint-create) | POST | Create checkpoint |
| [`/task/checkpoint/delete`](checkpoint.md#post-task-checkpoint-delete) | POST | Delete checkpoint |
| [`/task/checkpoint/list`](checkpoint.md#post-task-checkpoint-list) | POST | List checkpoints |
| [`/task/checkpoint/read`](checkpoint.md#post-task-checkpoint-read) | POST | Read checkpoint |
| [`/task/checkpoint/transmute`](checkpoint.md#post-task-checkpoint-transmute) | POST | Convert checkpoint to task |
| [`/task/checkpoint/update`](checkpoint.md#post-task-checkpoint-update) | POST | Update checkpoint |

#### Task history

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/history/list`](history.md#post-task-history-list) | POST | List task history |

#### Form

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/form/create`](form/README.md#post-task-form-create) | POST | Attach form to task |
| [`/task/form/delete`](form/README.md#post-task-form-delete) | POST | Delete task form |
| [`/task/form/download`](form/README.md#post-task-form-download) | POST | Download task form |
| [`/task/form/list`](form/README.md#post-task-form-list) | POST | List forms |
| [`/task/form/read`](form/README.md#post-task-form-read) | POST | Read task form |

#### Task form files

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/form/file/create`](form/file.md#post-task-form-file-create) | POST | Create form file |

#### Task form values

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/form/values/update`](form/values.md#post-task-form-values-update) | POST | Update form values |

#### Route

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/route/assign`](route/README.md#post-task-route-assign) | POST | Assign route |
| [`/task/route/create`](route/README.md#post-task-route-create) | POST | Create route |
| [`/task/route/delete`](route/README.md#post-task-route-delete) | POST | Delete route |
| [`/task/route/list`](route/README.md#post-task-route-list) | POST | List routes |
| [`/task/route/read`](route/README.md#post-task-route-read) | POST | Read route |
| [`/task/route/update`](route/README.md#post-task-route-update) | POST | Update route |

#### Route optimization

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/route/points/optimize`](route/optimize.md#post-task-route-points-optimize) | POST | Optimize route points |

#### Schedule

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/schedule/create`](schedule/README.md#post-task-schedule-create) | POST | Create task schedule |
| [`/task/schedule/delete`](schedule/README.md#post-task-schedule-delete) | POST | Delete task schedule |
| [`/task/schedule/list`](schedule/README.md#post-task-schedule-list) | POST | List task schedules |
| [`/task/schedule/read`](schedule/README.md#post-task-schedule-read) | POST | Read task schedule |
| [`/task/schedule/update`](schedule/README.md#post-task-schedule-update) | POST | Update task schedule |

#### Task schedule checkpoints

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/schedule/checkpoint/delete`](schedule/checkpoint.md#post-task-schedule-checkpoint-delete) | POST | Delete schedule checkpoint |
| [`/task/schedule/checkpoint/transmute`](schedule/checkpoint.md#post-task-schedule-checkpoint-transmute) | POST | Convert schedule checkpoint to task |

#### Schedule proposals

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/schedule/proposal/list`](schedule/proposal.md#post-task-schedule-proposal-list) | POST | List schedule proposals |

#### Route schedules

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/schedule/route/create`](schedule/route.md#post-task-schedule-route-create) | POST | Create route schedule |
| [`/task/schedule/route/delete`](schedule/route.md#post-task-schedule-route-delete) | POST | Delete route schedule |
| [`/task/schedule/route/update`](schedule/route.md#post-task-schedule-route-update) | POST | Update route schedule |

<!-- endpoint-reference:end -->
