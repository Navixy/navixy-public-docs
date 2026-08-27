---
title: Sub-user session
description: Open a session as one of your sub-users, without knowing their password.
---

# Sub-user session

This operation opens a session as one of the master account's [sub-users](README.md) and returns its hash. It is how a master user acts on a sub-user's behalf, for support or for an integration that needs a sub-user's restricted view, without knowing their password.

The hash that comes back is used exactly like one from [`user/auth`](../user/README.md#post-user-auth), and carries that sub-user's rights and scoping.

## API actions

API base path: `/subuser/session`.

Every operation on this page needs the `multilevel_access` tariff feature on **all** trackers, and the `admin` right, which is available only to master users. Failing either returns error 13 for the right and error 236 for the feature, so those two are not repeated per operation below.

***

{% openapi-operation spec="navixy-platform" path="/subuser/session/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.
