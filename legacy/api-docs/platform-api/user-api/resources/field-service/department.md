---
title: Departments
description: A group of employees, used to organise them into teams.
---

# Departments

A department is a group of [employees](employee/README.md). An employee joins one by carrying its ID in `department_id`, and a null value leaves them in no department.

## Department object

{% openapi-schemas spec="navixy-platform" schemas="Department" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/department`.

***

{% openapi-operation spec="navixy-platform" path="/department/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 7 - Invalid parameters.
* 217 - The list contains non-existent entities.
* 221 - Device limit exceeded - if device limit set for the user's dealer has been exceeded.

***

_Required sub-user rights:_ `employee_update`.

{% openapi-operation spec="navixy-platform" path="/department/create" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 7 - Invalid parameters.
* 217 - The list contains non-existent entities.
* 221 - Device limit exceeded - if device limit set for the user's dealer has been exceeded.

***

_Required sub-user rights:_ `employee_update`.

{% openapi-operation spec="navixy-platform" path="/department/update" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

_Required sub-user rights:_ `employee_update`.

{% openapi-operation spec="navixy-platform" path="/department/delete" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database.

