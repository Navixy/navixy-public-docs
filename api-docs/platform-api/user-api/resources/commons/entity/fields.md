---
title: Entity fields
description: Custom fields added to an entity, and the types they can take.
---

# Entity fields

A field adds custom information to an [entity](README.md). Each field belongs to exactly one entity, so a custom field defined on places exists only on places.

## Field object

{% openapi-schemas spec="navixy-platform" schemas="CustomField" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

### Field types without special parameters

* `text` - text of up to 700 Unicode characters.
* `bigtext` - text of up to 20,000 Unicode characters, with reduced search and sorting.
* `email` - an email address, validated as one.
* `phone` - a phone number, validated as one.
* `decimal` - a decimal from -999999999999.999999 to 999999999999.999999, stored to six decimal places.
* `integer` - an integer from `-2^63` to `2^63 - 1`.

### Field types with special parameters

`employee` links the entity to an employee. It takes one special parameter:

```json
{
  "responsible": true
}
```

* `responsible` - boolean. Only one employee field per entity can set this to `true`.

Setting `responsible` has an effect in the Mobile Tracker App, for [Android](https://play.google.com/store/apps/details?id=com.navixy.xgps.tracker\&hl=ru) and [iOS](https://apps.apple.com/us/app/x-gps-tracker/id802887190). When an [employee is assigned](../../tracking/tracker/employee.md#post-tracker-employee-assign) to a device running the app, and a [place](../../field-service/place/work-with-poi.md) carries a responsible-employee field naming them, that place becomes visible to them in the app. This is what lets an employee see the places they are expected to visit.

## API actions

API base path: `/entity/fields`.

The entity ID comes from [`entity/list`](README.md#post-entity-list).

***

{% openapi-operation spec="navixy-platform" path="/entity/fields/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no entity has the given ID.

***

A field sent with `id` set to `null` is created. A field sent with an existing `id` keeps its `type`, which cannot be changed. Every field on one entity must have a different `label`, and an `id` belonging to no field, or to a field on another entity, is rejected.

{% hint style="danger" %}
When `delete_missing` is `true`, every existing field left out of the `fields` list is **permanently deleted**. When it is `false`, those fields are untouched.
{% endhint %}

_Required sub-user rights:_ `places_custom_fields_update` for fields on the `place` entity.

{% openapi-operation spec="navixy-platform" path="/entity/fields/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 7 - Invalid parameters, when the fields violate the restrictions above.
* 201 - Not found in the database, when no entity has the given ID.
