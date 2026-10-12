---
title: Tag
description: Labels applied across entity types, used to group and find things that belong together.
---

# Tag

A tag is a label attached to an entity so that related things can be found together. One tag can span types, so a "north depot" tag can sit on places, geofences, employees, tasks, trackers, and vehicles at once, and [`tag/search`](README.md#post-tag-search) then returns all of them grouped by type.

For a walkthrough, see [how to use tags](../../../guides/fleet-management/use-tags.md).

## Tag object

{% openapi-schemas spec="navixy-platform" schemas="Tag" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

#### Tagged entity types

A tag can be bound to any of these:

* `place`
* `task`
* `task_schedule`
* `employee`
* `vehicle`
* `zone`
* `tracker`

## API actions

API base path: `/tag`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `tag_update`.

{% openapi-operation spec="navixy-platform" path="/tag/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `tag_update`.

{% openapi-operation spec="navixy-platform" path="/tag/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no tag has the given ID. This does not occur when `tag_ids` is given, because deletion is silent in that case.

***

{% openapi-operation spec="navixy-platform" path="/tag/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

The result is grouped by [tagged entity type](README.md#tagged-entity-types) rather than returned as one list.

{% openapi-operation spec="navixy-platform" path="/tag/search" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `tag_update`.

{% openapi-operation spec="navixy-platform" path="/tag/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no tag has the given ID.

## More in this section

<!-- endpoint-reference:start -->

#### Tag avatar

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tag/avatar/assign`](tag_avatar.md#post-tag-avatar-assign) | POST | Assign tag icon |
| [`/tag/avatar/upload`](tag_avatar.md#post-tag-avatar-upload) | POST | Upload tag avatar |

<!-- endpoint-reference:end -->
