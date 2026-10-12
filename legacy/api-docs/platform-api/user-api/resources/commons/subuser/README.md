---
title: Sub-user
description: Additional users with their own rights and their own subset of the account's trackers.
---

# Sub-user

A sub-user is an additional login on a master account, with its own rights and its own subset of the account's trackers. It is how a company gives several employees access to one monitoring account without giving them all the same powers.

Two mechanisms limit what a sub-user can do. **Scoping** limits what they can see: a sub-user is bound to a subset of the master account's trackers, and everything associated with a tracker outside that subset is hidden from them too. **Rights**, granted through a [security group](security_group.md), limit what they can change.

{% hint style="info" %}
A sub-user owns nothing exclusively. Every tracker, rule, and task belongs to the master account even when a sub-user created it. The one exception is reporting: each sub-user has its own reports pool and report schedule.
{% endhint %}

## Sub-user object

A sub-user object is almost identical to an ordinary user.

{% openapi-schemas spec="navixy-platform" schemas="Subuser" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/subuser`.

Every operation on this page needs the `multilevel_access` tariff feature on **all** trackers, and the `admin` right, which is available only to master users. Failing either returns error 13 for the right and error 236 for the feature, so those two are not repeated per operation below.

***

{% openapi-operation spec="navixy-platform" path="/subuser/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.

***

{% openapi-operation spec="navixy-platform" path="/subuser/register" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the given `security_group_id` does not exist.
* 206 - Login already in use.

***

{% openapi-operation spec="navixy-platform" path="/subuser/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no sub-user has the given ID or it belongs to another master user. Also when the given `security_group_id` does not exist.

***

{% openapi-operation spec="navixy-platform" path="/subuser/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no sub-user has the given ID or it belongs to another master user.

## More in this section

<!-- endpoint-reference:start -->

#### Sub-user places

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/subuser/places/bind`](places.md#post-subuser-places-bind) | POST | Bind places to sub-user |
| [`/subuser/places/unbind`](places.md#post-subuser-places-unbind) | POST | Unbind places from sub-user |
| [`/subuser/places/list_ids`](places.md#post-subuser-places-list_ids) | POST | List sub-user place IDs |
| [`/subuser/places/list`](places.md#post-subuser-places-list) | POST | List sub-user places |

#### Sub-user security group

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/subuser/security_group/create`](security_group.md#post-subuser-security_group-create) | POST | Create security group |
| [`/subuser/security_group/delete`](security_group.md#post-subuser-security_group-delete) | POST | Delete security group |
| [`/subuser/security_group/list`](security_group.md#post-subuser-security_group-list) | POST | List security groups |
| [`/subuser/security_group/update`](security_group.md#post-subuser-security_group-update) | POST | Update security group |
| [`/subuser/security_group/assign`](security_group.md#post-subuser-security_group-assign) | POST | Assign security group |

#### Sub-user session

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/subuser/session/create`](session.md#post-subuser-session-create) | POST | Create sub-user session |

#### Sub-user trackers

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/subuser/tracker/bind`](tracker.md#post-subuser-tracker-bind) | POST | Bind trackers to sub-user |
| [`/subuser/tracker/unbind`](tracker.md#post-subuser-tracker-unbind) | POST | Unbind trackers from sub-user |
| [`/subuser/tracker/list`](tracker.md#post-subuser-tracker-list) | POST | List sub-user trackers |

#### Sub-user geofences

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/subuser/zones/bind`](zones.md#post-subuser-zones-bind) | POST | Bind geofences to sub-user |
| [`/subuser/zones/unbind`](zones.md#post-subuser-zones-unbind) | POST | Unbind geofences from sub-user |
| [`/subuser/zones/list_ids`](zones.md#post-subuser-zones-list_ids) | POST | List sub-user geofence IDs |
| [`/subuser/zones/list`](zones.md#post-subuser-zones-list) | POST | List sub-user geofences |

<!-- endpoint-reference:end -->
