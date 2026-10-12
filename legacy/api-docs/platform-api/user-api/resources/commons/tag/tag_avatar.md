---
title: Tag avatar
description: The icon shown for a tag, uploaded as an image or assigned from the built-in set.
---

# Tag avatar

A tag's avatar is the icon the interface shows for it. There are two ways to set one, and this page has an operation for each: assign one of the Navixy platform's built-in icons, or upload an image of your own.

An uploaded avatar is served from `[api_base_url]/[api_static_path]/tag/avatars/<file_name>`, for example `https://api.eu.navixy.com/v2/static/tag/avatars/abcdef123456789.png`. The upload returns `avatar_file_name`, which [`tag/list`](README.md#post-tag-list) also returns.

The upload is a multipart request rather than JSON, with the image in a part named `file`, and its MIME type must be one of `image/jpeg`, `image/pjpeg`, `image/png`, `image/gif`, or `image/webp`.

## API actions

API base path: `/tag/avatar`.

Every operation on this page requires the `tag_update` sub-user right.

***

{% openapi-operation spec="navixy-platform" path="/tag/avatar/assign" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no tag has the given `tag_id`.

***

{% openapi-operation spec="navixy-platform" path="/tag/avatar/upload" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no tag has the given `tag_id`.
* 233 - No data file, when the `file` part is missing.
* 234 - Invalid data format, when the `file` part has an unexpected MIME type.
* 254 - Cannot save file, on a file system error.
