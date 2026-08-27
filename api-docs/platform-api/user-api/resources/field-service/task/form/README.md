---
title: Form
description: Attach a form to a task, read it, and download it.
---

# Form

These operations attach a [form](../../form/README.md) to a [task](../README.md), read it back, and download it. A task carrying a form cannot be completed until the form is submitted, which is how a job is made to produce a record rather than just a completion.

Forms are filled by field employees in the Mobile Tracker App, for [Android](https://play.google.com/store/apps/details?id=com.navixy.xgps.tracker\&hl=ru) and [iOS](https://apps.apple.com/us/app/x-gps-tracker/id802887190). This API attaches and manages them.

For the field and value structures, see [Form fields and values](../../form/field-types.md#form-fields-and-values). `task/form/list` is the exception on this page: it returns forms of every kind, not only task forms.

## API actions

API base path: `/task/form`.

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/form/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no task or template with such an ID, or task has the "route" type.
* 247 - Entity already exists - if task already has form attached to it.
* 255 - Invalid task state - if current task state is not `unassigned`, `assigned` or `arrived` (plus `done`, `failed`, and `delayed`  for users with `completed_form_update` right).

***

{% hint style="warning" %}
All form data will be lost!
{% endhint %}

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/form/delete" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no task with such an ID, or task has the "route" type, or it has no form attached.
* 255 - Invalid task state - if current task state is not `unassigned`, `assigned` or `arrived`.

***

{% openapi-operation spec="navixy-platform" path="/task/form/download" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if task does not exist or does not have attached form.

***

In addition to the data on the forms, the list contains data on the objects related to each form: tracker, vehicle, employee and task.

{% openapi-operation spec="navixy-platform" path="/task/form/list" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 204 - Not found - if there is no form template with such ID belonging to authorized user.
* [General](../../../../../general/errors.md#error-codes) types of errors.

***

current sub-user.

{% openapi-operation spec="navixy-platform" path="/task/form/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no task with such an ID, or task assigned to tracker unavailable to\

## More in this section

<!-- endpoint-reference:start -->

#### Task form files

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/form/file/create`](file.md#post-task-form-file-create) | POST | Create form file |

#### Task form values

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/form/values/update`](values.md#post-task-form-values-update) | POST | Update form values |

<!-- endpoint-reference:end -->
