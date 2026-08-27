---
title: Group
description: A visual grouping of trackers in the interface, with a title and a colour.
---

# Group

A tracker group organises trackers in the interface. Its function is purely visual: a group carries a title and a colour, and grants nothing and restricts nothing.

Groups are a master-user feature. Every operation here except listing requires the `admin` right, which cannot be given to a sub-user. A tracker belongs to at most one group, and assigning it to group `0` takes it out of any group.

## Group object

{% openapi-schemas spec="navixy-platform" schemas="TrackerGroup" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/tracker/group`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `admin`, available only to master users.

{% openapi-operation spec="navixy-platform" path="/tracker/group/assign" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No group with this ID, or it belongs to another user.
* 217 - The list names trackers that do not exist or belong to another user.

***

_Required sub-user rights:_ `admin`, available only to master users.

{% openapi-operation spec="navixy-platform" path="/tracker/group/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `admin`, available only to master users.

{% openapi-operation spec="navixy-platform" path="/tracker/group/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No group with this ID, or it belongs to another user.

***

{% openapi-operation spec="navixy-platform" path="/tracker/group/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `admin`, available only to master users.

{% openapi-operation spec="navixy-platform" path="/tracker/group/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No group with this ID, or it belongs to another user.
