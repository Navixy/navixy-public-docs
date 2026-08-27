---
title: Service task file
description: Reserve and upload a file to attach to a service task.
---

# Service task file

A [service task](README.md) can carry files: an invoice, a photograph of the work, a signed job sheet. This operation reserves one and returns the credentials for uploading it.

The exchange works the same way as for [task form files](../../../field-service/task/form/file.md). Reserving the file is the first step, uploading it to the returned URL is the second, and every file counts against the account's file storage quota.

## API actions

API base path: `/vehicle/service_task/file`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

Creates a file to specify its ID in service work later.

Upload with a POST multipart request to the returned `url`: send the values from `fields` first, then the file part last, named as `file_field_name` says. `token` authenticates the upload.

***

{% openapi-operation spec="navixy-platform" path="/vehicle/service_task/file/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
