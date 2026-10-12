---
title: Vehicle avatar
description: The icon shown for a vehicle, assigned from the built-in set or uploaded.
---

# Vehicle avatar

A vehicle's avatar is the icon shown for it in the interface. As with [places](../../field-service/place/avatar.md) and [employees](../../field-service/employee/avatar.md), it cannot be set through `vehicle/update`: use `avatar/assign` to pick one of the built-in icons, or `avatar/upload` to supply an image.

## API actions

API base path: `/vehicle/avatar`.

***

_Required sub-user rights:_ `vehicle_update`.

`icon_id` can be null, which means the uploaded avatar should be used instead of an icon.

{% openapi-operation spec="navixy-platform" path="/vehicle/avatar/assign" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

Uploads avatar image for specified vehicle.Then it will be available from `[api_base_url]/<api_static_path>/vehicle/avatars/<file_name>`e.g. `https://api.eu.navixy.com/v2/static/vehicle/avatars/abcdef123456789.png`.

**avatar\_file\_name** returned in response and will be returned from [/vehicle/list](README.md#post-vehicle-list).

**MUST** be a POST multipart request (multipart/form-data),with one of the parts being an image file upload (with the name "file").

File part **mime** type must be one of :

* `image/jpeg`
* `image/pjpeg`
* `image/png`
* `image/gif`
* `image/webp`

If `redirect_target` passed a return redirect to `response=<urlencoded response json>`.

_Required sub-user rights:_ `vehicle_update`.

{% openapi-operation spec="navixy-platform" path="/vehicle/avatar/upload" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201, 233, 234, 254.

