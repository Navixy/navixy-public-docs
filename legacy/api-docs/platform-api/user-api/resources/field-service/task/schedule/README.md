---
title: Schedule
description: Schedule entries that create tasks automatically, each day they are due.
---

# Schedule

A task schedule creates [tasks](../README.md) automatically instead of someone creating the same task by hand every week. Shortly after midnight in the [user's timezone](../../../commons/user/settings/README.md), the Navixy platform checks the schedule, and any task due to start that day is created and assigned to its employee.

A schedule entry looks much like a task, with one difference that matters when writing one. A task carries `from` and `to` as specific dates and times; a schedule entry carries `from_time`, `duration`, and `parameters` instead, because it describes a shape rather than an occurrence.

## Task schedule entry object

{% openapi-schemas spec="navixy-platform" schemas="TaskScheduleEntry,ScheduleParameters" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/task/schedule`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/schedule/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if schedule.tracker\_id belongs to nonexistent tracker.
* 204 - Entity not found - if schedule.form\_template\_id belongs to nonexistent form template.
* 208 - Device blocked - if tracker exists but was blocked due to tariff restrictions or some other reason.
* 236 - Feature unavailable due to tariff restrictions - if device's tariff does not allow usage of tasks.

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/schedule/delete" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no task schedule with such an ID.

***

This call also returns all unassigned task schedules.

{% openapi-operation spec="navixy-platform" path="/task/schedule/list" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/task/schedule/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/schedule/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if schedule.tracker\_id belongs to nonexistent tracker.
* 204 - Entity not found - if there is no task schedule with specified ID.
* 208 - Device blocked - if tracker exists but was blocked due to tariff restrictions or some other reason.
* 236 - Feature unavailable due to tariff restrictions - if device's tariff does not allow usage of tasks.

## More in this section

<!-- endpoint-reference:start -->

#### Task schedule checkpoints

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/schedule/checkpoint/delete`](checkpoint.md#post-task-schedule-checkpoint-delete) | POST | Delete schedule checkpoint |
| [`/task/schedule/checkpoint/transmute`](checkpoint.md#post-task-schedule-checkpoint-transmute) | POST | Convert schedule checkpoint to task |

#### Schedule proposals

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/schedule/proposal/list`](proposal.md#post-task-schedule-proposal-list) | POST | List schedule proposals |

#### Route schedules

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/schedule/route/create`](route.md#post-task-schedule-route-create) | POST | Create route schedule |
| [`/task/schedule/route/delete`](route.md#post-task-schedule-route-delete) | POST | Delete route schedule |
| [`/task/schedule/route/update`](route.md#post-task-schedule-route-update) | POST | Update route schedule |

<!-- endpoint-reference:end -->
