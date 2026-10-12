---
title: Map layer
description: User-supplied KML overlays drawn on top of the Navixy platform's map.
---

# Map layer

A map layer is a KML file a user uploads to draw their own geometry over the platform's map.

A layer is created by uploading its file and is read back as that same file. The layer object itself is metadata only, an ID and a name, so `map_layer/update` renames a layer and does nothing else. To replace the geometry, upload a new layer and delete the old one.

## Map layer object

{% openapi-schemas spec="navixy-platform" schemas="MapLayer" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/map_layer`.

Operations that list no errors of their own return only the [general error codes](../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/map_layer/read" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the current user has no map layer with the given ID.

***

{% openapi-operation spec="navixy-platform" path="/map_layer/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/map_layer/upload" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 233 - No data file, when the `file` part is missing.
* 234 - Invalid data format, when the file has the wrong MIME type.
* 242 - Validation error, when the uploaded file is not valid KML.
* 268 - Over quota, when the user's quota for map layers is exceeded.

***

{% openapi-operation spec="navixy-platform" path="/map_layer/update" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the current user has no map layer with the given ID.

***

{% openapi-operation spec="navixy-platform" path="/map_layer/delete" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the current user has no map layer with the given ID.
