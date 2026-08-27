---
title: Delivery info
description: Delivery states for courier apps, carrying the task being delivered, the tracker carrying it, and the driver.
---

# Delivery info

Delivery info is what a delivery or courier application needs to follow a job in progress: the task or checkpoint being delivered, the tracker carrying it, and the driver. Only tasks and checkpoints whose start date has already passed and whose status is `assigned` or `arrived` are searched.

Both operations return three composite objects. `task` is a [task object](../field-service/task/README.md), `tracker` is a [tracker object](tracker/README.md), and `restrictions` is the tariff restrictions object that [`user/get_tariff_restrictions`](../commons/user/README.md#post-user-get_tariff_restrictions) returns.

Besides the standard user session, both operations accept the special `DELIVERY` session type.

## API actions

API base path: `/delivery`.

***

{% openapi-operation spec="navixy-platform" path="/delivery/read" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no task or checkpoint matches the given conditions.

***

{% openapi-operation spec="navixy-platform" path="/delivery/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no task or checkpoint matches the given conditions.
