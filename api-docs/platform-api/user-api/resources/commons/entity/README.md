---
title: Entity
description: Classes of object whose fields and layout an account can customize.
---

# Entity

An entity is a class of object whose presentation and editable fields an account can customize. Adding a custom field to places, or reordering the fields already there, is done through this resource.

More than one entity type exists. A plain account lists both `place` and `task`. Reading or updating one needs the right mapped to that type, so an account can see an entity in the list and still get error 13 when it tries to read it.

## Entity object

{% openapi-schemas spec="navixy-platform" schemas="CustomizableEntity" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

The `place` type is the same object the [place API](../../field-service/place/work-with-poi.md) works with. Its built-in fields are `label`, `location`, `tags`, and `description`.

## API actions

API base path: `/entity`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/entity/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

Give exactly one of `id` and `type`. Both null or both set is rejected. The `fields` that come back are described in the [field object](fields.md#field-object).

{% openapi-operation spec="navixy-platform" path="/entity/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no entity has the given ID.

***

The entity must carry a valid ID.

{% hint style="warning" %}
`entity.settings.layout.sections` must contain the IDs of every built-in and custom field associated with the entity. No field can be left out of the layout, only reordered, and no field can appear twice even in different sections.
{% endhint %}

_Required sub-user rights:_ `places_custom_fields_update` for entities with type `place`.

{% openapi-operation spec="navixy-platform" path="/entity/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 7 - Invalid parameters, when the entity object violates the layout restrictions above.
* 201 - Not found in the database, when no entity has the given ID.

## More in this section

<!-- endpoint-reference:start -->

#### Entity fields

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/entity/fields/read`](fields.md#post-entity-fields-read) | POST | Read entity fields |
| [`/entity/fields/update`](fields.md#post-entity-fields-update) | POST | Update entity fields |

<!-- endpoint-reference:end -->
