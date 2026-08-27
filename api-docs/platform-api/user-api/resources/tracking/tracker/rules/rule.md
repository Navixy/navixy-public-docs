---
title: Rule
description: Conditions that make the Navixy platform log an event and notify someone when a tracker meets them.
---

# Rule

A rule is a condition attached to trackers. When a tracker meets it, the platform logs an event and can notify a user about it, which is how speeding, geofence entry, low fuel, or an offline device turn into something a person sees.

A rule exists once and is bound to as many trackers as needed, so `rule/create` defines the condition and `rule/bind` decides which trackers it applies to. For a walkthrough, see [how to create and use rules](../../../../guides/rules-notifications/use-rules.md).

## Rule object

{% openapi-schemas spec="navixy-platform" schemas="Rule,RuleScheduleInterval" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

`type`, `param`, and `extended_params` all depend on which rule is being created. [Rule types](rule_types.md) documents them per type, along with the meaning of `primary_text` and `secondary_text`. Date, time, and `local_time` values follow the [data types](../../../../../general/api-conventions.md#data-types).

{% hint style="warning" %}
`create` and `update` take the rule **wrapped in a `rule` object**, not as top-level fields. An earlier version of this page listed the fields flat, which the API rejects with error 7.
{% endhint %}

## API actions

API base path: `/tracker/rule`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `tracker_rule_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/rule/bind" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No rule with this ID, or it belongs to another user.

***

_Required sub-user rights:_ `tracker_rule_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/rule/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 204 - An associated geofence does not exist.

***

_Required sub-user rights:_ `tracker_rule_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/rule/delete" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No rule with this ID, or it belongs to another user.

***

{% openapi-operation spec="navixy-platform" path="/tracker/rule/list" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `tracker_rule_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/rule/unbind" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No rule with this ID, or it belongs to another user.

***

_Required sub-user rights:_ `tracker_rule_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/rule/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No rule with this ID, or it belongs to another user.
* 204 - A newly associated geofence does not exist.
