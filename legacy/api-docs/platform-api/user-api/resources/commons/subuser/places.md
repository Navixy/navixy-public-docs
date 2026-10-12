---
title: Sub-user places
description: Which of the master account's places a sub-user can see.
---

# Sub-user places

These operations set which places a [sub-user](README.md) can see. Places are scoped separately from trackers, so a sub-user's place list is bound here rather than following from [their trackers](tracker.md).

Two list operations exist because they answer different questions: `list_ids` returns only the IDs, which is what a permissions screen needs, and `list` returns the place objects themselves.

## API actions

API base path: `/subuser/places`.

Every operation on this page needs the `multilevel_access` tariff feature on **all** trackers, and the `admin` right, which is available only to master users. Failing either returns error 13 for the right and error 236 for the feature, so those two are not repeated per operation below.

Every operation here also returns error 201, Not found in the database, when the sub-user does not exist or belongs to another master user.

***

{% openapi-operation spec="navixy-platform" path="/subuser/places/bind" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.

***

{% openapi-operation spec="navixy-platform" path="/subuser/places/unbind" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.

***

{% openapi-operation spec="navixy-platform" path="/subuser/places/list_ids" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.

***

{% openapi-operation spec="navixy-platform" path="/subuser/places/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.
