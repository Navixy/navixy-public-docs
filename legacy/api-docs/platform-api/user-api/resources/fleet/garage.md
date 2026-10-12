---
title: Garage (depot)
description: The premises a fleet is maintained from, with its address and the people responsible.
---

# Garage (depot)

A garage is the premises a fleet is maintained from. It records the address, the mechanic and dispatcher responsible, and the other details that make maintenance and task assignment easier to organise.

{% hint style="info" %}
**Garage and depot are the same thing.** The Navixy interface calls it a depot, and the API calls it a garage: the path is `/garage` and the object is `Garage`. This page uses garage, because that is what the API says.
{% endhint %}

## Garage object

{% openapi-schemas spec="navixy-platform" schemas="Garage" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/garage`.

Operations that list no errors of their own return only the [general error codes](../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/garage/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `vehicle_update`.

{% openapi-operation spec="navixy-platform" path="/garage/create" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `vehicle_update`.

{% openapi-operation spec="navixy-platform" path="/garage/update" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database, if there is no depot with such an ID.

***

_Required sub-user rights:_ `vehicle_update`.

{% openapi-operation spec="navixy-platform" path="/garage/delete" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database, if there is no depot with such an ID.

