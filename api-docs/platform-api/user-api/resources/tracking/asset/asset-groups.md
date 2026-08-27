---
title: Asset groups
description: Group assets of different types so they are tracked and reported on together.
---

# Asset groups

An asset group binds assets of different types together so they can be handled as one unit. A group holds at most one asset of each type.

Membership is exclusive: an asset already in a group cannot join another unless the request sets `force_reassign`. Deleting a group marks it deleted rather than erasing it, so its history stays intact.

Every operation here works only on assets the current user can reach. A request that names an asset outside that reach returns error 291, and a group whose assets have all become unreachable returns error 287.

## Asset group entry

{% openapi-schemas spec="navixy-platform" schemas="AssetGroup" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## Asset group object entry

{% openapi-schemas spec="navixy-platform" schemas="AssetGroupObject" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## Asset entry

{% openapi-schemas spec="navixy-platform" schemas="AssetId" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/asset_group`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/asset_group/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 290 - Asset already assigned to a group, when `force_reassign` is `false` and an asset in the request already belongs to one. The response carries `assigned_assets`, a list of the [asset group objects](asset-groups.md#asset-group-object-entry) already assigned.
* 291 - All assets in the request must be accessible, when the request names an asset the user cannot reach.

***

{% openapi-operation spec="navixy-platform" path="/asset_group/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/asset_group/set" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no such group exists.
* 290 - Asset already assigned to a group, when `force_reassign` is `false` and an asset in the request already belongs to one. The response carries `assigned_assets`, a list of the [asset group objects](asset-groups.md#asset-group-object-entry) already assigned.
* 291 - All assets in the request must be accessible, when the request names an asset the user cannot reach.

***

{% openapi-operation spec="navixy-platform" path="/asset_group/remove" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no such group exists.
* 286 - All assets must be present in the group, when the request names an asset the group does not hold.
* 287 - At least 1 asset in the group must be accessible, when the group holds no asset the user can reach.

***

{% openapi-operation spec="navixy-platform" path="/asset_group/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no such group exists.
* 287 - At least 1 asset in the group must be accessible, when the group holds no asset the user can reach.

***

The group is marked as deleted and its history stays intact.

{% openapi-operation spec="navixy-platform" path="/asset_group/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no such group exists.
* 289 - All assets in the group must be accessible, when the group holds an asset the user cannot reach.
