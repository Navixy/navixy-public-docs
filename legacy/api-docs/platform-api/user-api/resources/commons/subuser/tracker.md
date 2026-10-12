---
title: Sub-user trackers
description: Which of the master account's trackers a sub-user can see.
---

# Sub-user trackers

These operations set a [sub-user's](README.md) scope: the subset of the master account's trackers they can see. Everything associated with a tracker outside that subset is hidden from them too, so binding and unbinding a tracker changes far more than one entry in a list.

## API actions

API base path: `/subuser/tracker`.

Every operation on this page needs the `multilevel_access` tariff feature on **all** trackers, and the `admin` right, which is available only to master users. Failing either returns error 13 for the right and error 236 for the feature, so those two are not repeated per operation below.

Every operation here also returns error 201, Not found in the database, when the sub-user does not exist or belongs to another master user.

***

{% openapi-operation spec="navixy-platform" path="/subuser/tracker/bind" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.
* 262 - The trackers cannot be bound as requested.

***

{% openapi-operation spec="navixy-platform" path="/subuser/tracker/unbind" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.
* 262 - The trackers cannot be unbound as requested.

***

{% openapi-operation spec="navixy-platform" path="/subuser/tracker/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.
