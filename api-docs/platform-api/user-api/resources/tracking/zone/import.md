---
title: Geofence import
description: Create many circle geofences at once from a spreadsheet, through a background import process.
---

# Geofence import

Geofence import creates many circle geofences at once from a spreadsheet. Only circles can be imported this way.

The import runs as a background process across five operations. Convert the spreadsheet first with [`data/spreadsheet/parse`](../../commons/data.md#post-data-spreadsheet-parse), which stores it as a tab-separated file and returns its name. Pass that name to `zone/import/start` as `filename`, follow the process with `zone/import/read` or `zone/import/list`, collect the rows that failed validation with `zone/import/download_failed`, and close the finished process with `zone/import/finish`.

Address and coordinates complete each other. A row that gives only an address gets its coordinates by geocoding, and a row that gives only coordinates gets its address by reverse geocoding. Tags that do not exist yet are created, within the user's quota for tags.

## Import process object

{% openapi-schemas spec="navixy-platform" schemas="ImportProcess,ImportLineMessage" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/zone/import`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `zone_update`.

{% openapi-operation spec="navixy-platform" path="/zone/import/start" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 15 - Too many requests, when too many imports are already in progress.
* 233 - No data file, when the preloaded file is not found.
* 234 - Invalid data format, when the file is not a TSV.
* 247 - Entity already exists, when another import already uses the same file.

***

{% openapi-operation spec="navixy-platform" path="/zone/import/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the import process is not found.

***

{% openapi-operation spec="navixy-platform" path="/zone/import/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/zone/import/download_failed" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the import process is not found.
* 204 - Entity not found, when the file is not found.

***

{% openapi-operation spec="navixy-platform" path="/zone/import/finish" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the import process is not found.
* 280 - Invalid import request state, when the import process is still running.
