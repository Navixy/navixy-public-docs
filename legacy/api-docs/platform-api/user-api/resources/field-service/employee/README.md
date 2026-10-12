---
title: Employee
description: The people working for an account, linked to the trackers, vehicles, and places they work with.
---

# Employee

An employee represents a person working for the account. Employees are what make tracking data about people rather than only about hardware: an employee linked to a [tracker](../../tracking/tracker/employee.md) is the driver of whatever that tracker is fitted to, and reports can then attribute trips and behaviour to them.

Employees also link to vehicles and [places](../place/work-with-poi.md), belong to [departments](../department.md), and carry the [avatar](avatar.md) shown for them in the interface.

## Employee object

{% openapi-schemas spec="navixy-platform" schemas="Employee" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

The avatar fields are set elsewhere: `icon_id` through [avatar/assign](avatar.md#post-employee-avatar-assign), and `avatar_file_name` through [avatar/upload](avatar.md#post-employee-avatar-upload).

## API actions

API base path: `/employee`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/employee/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `employee_update`.

{% openapi-operation spec="navixy-platform" path="/employee/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 247 - Entity already exists.

***

{% openapi-operation spec="navixy-platform" path="/employee/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

_Required sub-user rights:_ `employee_update`.

{% openapi-operation spec="navixy-platform" path="/employee/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.
* 247 - Entity already exists.

***

_Required sub-user rights:_ `employee_update`.

{% openapi-operation spec="navixy-platform" path="/employee/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

_Required sub-user rights:_ `employee_update`.

{% openapi-operation spec="navixy-platform" path="/employee/batch_convert" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 234 - Invalid data format.

## More in this section

<!-- endpoint-reference:start -->

#### Employee avatar

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/employee/avatar/assign`](avatar.md#post-employee-avatar-assign) | POST | Assign employee icon |
| [`/employee/avatar/upload`](avatar.md#post-employee-avatar-upload) | POST | Upload employee avatar |

#### Employee import

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/employee/import/start`](import.md#post-employee-import-start) | POST | Start employee import |
| [`/employee/import/read`](import.md#post-employee-import-read) | POST | Read employee import |
| [`/employee/import/list`](import.md#post-employee-import-list) | POST | List employee imports |
| [`/employee/import/download_failed`](import.md#post-employee-import-download_failed) | POST | Download failed employee rows |

<!-- endpoint-reference:end -->
