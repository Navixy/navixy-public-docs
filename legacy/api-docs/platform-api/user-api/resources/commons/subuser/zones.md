---
title: Sub-user geofences
description: Which of the master account's geofences a sub-user can see.
---

# Sub-user geofences

These operations set which [geofences](../../tracking/zone/README.md) a [sub-user](README.md) can see. Geofences are scoped separately from trackers, so a sub-user's geofence list is bound here rather than following from [their trackers](tracker.md).

Two list operations exist because they answer different questions: `list_ids` returns only the IDs, which is what a permissions screen needs, and `list` returns the geofence objects themselves.

## API actions

API base path: `/subuser/zones`.

Every operation on this page needs the `multilevel_access` tariff feature on **all** trackers, and the `admin` right, which is available only to master users. Failing either returns error 13 for the right and error 236 for the feature, so those two are not repeated per operation below.

Every operation here also returns error 201, Not found in the database, when the sub-user does not exist or belongs to another master user.

***

{% openapi-operation spec="navixy-platform" path="/subuser/zones/bind" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.

***

{% openapi-operation spec="navixy-platform" path="/subuser/zones/unbind" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.

***

{% openapi-operation spec="navixy-platform" path="/subuser/zones/list_ids" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.

***

{% openapi-operation spec="navixy-platform" path="/subuser/zones/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.
