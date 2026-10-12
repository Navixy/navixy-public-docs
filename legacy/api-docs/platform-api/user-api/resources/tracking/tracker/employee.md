---
title: Employee
description: Assign an employee as the driver of a tracker, and read who is assigned now.
---

# Employee

Assigning an employee to a tracker records who is driving it. That link is what lets reports attribute trips, mileage, and behaviour to a person rather than only to a device.

Reading the assignment returns more than the name: it also reports the hardware key used to identify the driver, and when and where the assignment was made.

## API actions

API base path: `/tracker/employee`.

***

_Required sub-user rights:_ `employee_update`.

_Required tariff feature:_ `app_fleet`.

{% openapi-operation spec="navixy-platform" path="/tracker/employee/assign" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker or employee with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 263 - No change needed, because the tracker already has this assignment state.

***

{% openapi-operation spec="navixy-platform" path="/tracker/employee/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
