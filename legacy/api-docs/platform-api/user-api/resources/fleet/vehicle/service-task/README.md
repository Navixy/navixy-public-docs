---
title: Service task
description: Scheduled maintenance on a vehicle, due by date, mileage, or engine hours.
---

# Service task

A service task is a piece of maintenance due on a vehicle: an oil change, a brake replacement, an inspection. It is what turns tracking data into a maintenance schedule, because the Navixy platform can watch a vehicle's mileage and engine hours and raise the task when either passes its threshold.

A task carries a status through its life, changed with `set_status`, and can carry [files](file.md) such as an invoice or a photograph of the work. For a walkthrough, see [how to manage vehicles and service works](../../../../guides/fleet-management/service-works.md).

## Service task object

{% openapi-schemas spec="navixy-platform" schemas="ServiceTask,ServiceTaskConditions,ServiceTaskNotifications" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## Task status

Task **status** may be one of:

* `created` - initial state of task.
* `notified` - one of conditions exceed notification limit.
* `expired` - one of conditions exceeded.
* `done` - user [set](./#post-vehicle-service_task-set_status) task as "done".

## API actions

API base path: `/vehicle/service_task`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

The `conditions` parameter of `service_task/list` and `service_task/download` filters the task list and takes [entity search conditions](../../../commons/entity/search_conditions.md). It is not the task's own `conditions` object, which holds the thresholds that make a task due.

***

Creates the same service work for a batch of vehicles. At least one of `conditions.mileage`, `conditions.date` or `conditions.engine_hours` must be passed.

{% openapi-operation spec="navixy-platform" path="/vehicle/service_task/batch/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

Creates a service work for one vehicle. At least one of `conditions.mileage`, `conditions.date` or `conditions.engine_hours` must be passed.

{% openapi-operation spec="navixy-platform" path="/vehicle/service_task/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201, 214.

***

Deletes a service work.

{% openapi-operation spec="navixy-platform" path="/vehicle/service_task/delete" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

Downloads a report of service works, using the same filters as list.

{% openapi-operation spec="navixy-platform" path="/vehicle/service_task/download" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

Lists service works across all of the user's vehicles.

{% openapi-operation spec="navixy-platform" path="/vehicle/service_task/list" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

Reads one service work by ID.

{% openapi-operation spec="navixy-platform" path="/vehicle/service_task/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

Updates a task status. On `done`, the current date and the counter values used in its conditions are saved, which freezes the wear percentage.

{% openapi-operation spec="navixy-platform" path="/vehicle/service_task/set_status" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201, 204.

***

Updates a service work. Pass a full task object including its `id`. `completion` is not editable.

{% openapi-operation spec="navixy-platform" path="/vehicle/service_task/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 204, 214.

## More in this section

<!-- endpoint-reference:start -->

#### Service task file

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/vehicle/service_task/file/create`](file.md#post-vehicle-service_task-file-create) | POST | Create service task file |

<!-- endpoint-reference:end -->
