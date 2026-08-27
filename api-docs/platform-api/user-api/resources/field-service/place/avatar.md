---
title: Place avatar
description: The icon shown for a place, assigned from the built-in set or uploaded.
---

# Place avatar

A place's avatar is the icon shown for it on the map and in lists. It cannot be set through `place/update`: use `avatar/assign` to pick one of the built-in icons, or `avatar/upload` to supply an image.

An uploaded avatar is served from `[api_base_url]/<api_static_uri>/place/avatars/<file_name>`, for example `https://api.eu.navixy.com/v2/static/place/avatars/abcdef123456789.png`. The upload returns `avatar_file_name`, which [`place/list`](work-with-poi.md#post-place-list) also returns.

The upload is a multipart request rather than JSON, with the image in a part named `file`, and its MIME type must be one of `image/jpeg`, `image/pjpeg`, `image/png`, `image/gif`, or `image/webp`.

## API actions

API base path: `/place/avatar`.

Uploaded avatars become available from `[api_base_url]/<api_static_uri>/place/avatars/<file_name>` e.g. `https://api.eu.navixy.com/v2/static/place/avatars/abcdef123456789.png`.

**avatar\_file\_name** returned in response and will be returned from [place/list](work-with-poi.md#post-place-list).

**MUST** be a POST multipart request (multipart/form-data), with one of the parts being an image file upload (with the name "file").

File part **mime** type must be one of:

* `image/jpeg`
* `image/pjpeg`
* `image/png`
* `image/gif`
* `image/webp`

***

_Required sub-user rights:_ `place_update`.

{% openapi-operation spec="navixy-platform" path="/place/avatar/upload" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no place has the given `place_id`.
* 233 - No data file, when the `file` part is missing.
* 234 - Invalid data format, when the `file` part has an unexpected MIME type.
* 254 - Cannot save file, on a file system error.

***

Assigns `icon_id`, one of the standard icon set, to this place. `icon_id` can be null, meaning the uploaded avatar should be used instead of icon.

_Required sub-user rights:_ `place_update`.

{% openapi-operation spec="navixy-platform" path="/place/avatar/assign" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no place has the given `place_id`.

