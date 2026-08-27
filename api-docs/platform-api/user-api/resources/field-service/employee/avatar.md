---
title: Employee avatar
description: The icon shown for an employee, assigned from the built-in set or uploaded.
---

# Employee avatar

An employee's avatar is the icon shown for them in the interface. It cannot be set through `employee/update`: use `avatar/assign` to pick one of the built-in icons, or `avatar/upload` to supply an image.

An uploaded avatar is served from `/employee/avatars/`, for example `https://api.eu.navixy.com/v2/static/employee/avatars/abcdef123456789.png`. The upload returns `avatar_file_name`, which [`employee/list`](README.md#post-employee-list) also returns.

The upload is a multipart request rather than JSON, with the image in a part named `file`, and its MIME type must be one of `image/jpeg`, `image/pjpeg`, `image/png`, `image/gif`, or `image/webp`.

## API actions

API base path: `/employee/avatar`.

***

_Required sub-user rights:_ `employee_update`.

{% openapi-operation spec="navixy-platform" path="/employee/avatar/assign" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database - when employee/driver with `employee_id` not found.

***

Uploaded avatars become available from /employee/avatars/ e.g. `https://api.eu.navixy.com/v2/static/employee/avatars/abcdef123456789.png`.

**avatar\_file\_name** returned in response and will be returned from [/employee/list](README.md#post-employee-list).

**MUST** be a POST multipart request (multipart/form-data), with one of the parts being an image file upload (with the name `file`).

File part **mime** type must be one of:

* `image/jpeg`
* `image/pjpeg`
* `image/png`
* `image/gif`
* `image/webp`

_Required sub-user rights:_ `employee_update`.

{% openapi-operation spec="navixy-platform" path="/employee/avatar/upload" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database - when employee/driver with `employee_id` not found.
* 233 - No data file - if `file` part not passed.
* 234 - Invalid data format - if passed `file` with unexpected `mime` type.
* 254 - Cannot save file - on some file system errors.

