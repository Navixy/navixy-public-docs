---
title: Avatar
description: Upload the image shown for a tracker in the interface.
---

# Avatar

A tracker's avatar is the image the interface shows for it in lists and on the map. This operation uploads one, replacing whatever the tracker had before.

The upload is a multipart request rather than JSON, and the accepted image formats are `image/jpeg`, `image/pjpeg`, `image/png`, `image/gif`, and `image/webp`.

## API actions

API base path: `/tracker/avatar`.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/avatar/upload" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 233 - No file part was sent.
* 234 - The file has an unaccepted MIME type.
* 254 - The file could not be saved.
