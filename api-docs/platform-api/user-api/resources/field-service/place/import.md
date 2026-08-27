---
title: Place import
description: Create many places at once from a spreadsheet, through a background import process.
---

# Place import

Place import creates many [places](work-with-poi.md) at once from a spreadsheet, rather than one call per place.

Convert the spreadsheet first with [`data/spreadsheet/parse`](../../commons/data.md#post-data-spreadsheet-parse), which stores it as a tab-separated file and returns its name. Pass that name to `place/import/start` as `filename`, follow the process with `read` or `list`, collect the rows that failed validation with `download_failed`, and close the finished process with `finish`.

Address and coordinates complete each other. A row that gives only an address gets its coordinates by geocoding, and a row that gives only coordinates gets its address by reverse geocoding. Tags that do not exist yet are created, within the user's quota for tags.

## Import process object

{% openapi-schemas spec="navixy-platform" schemas="ImportProcess,ImportLineMessage" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/place/import`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

Available fields for `headers`:

* `label`
* `address`
* `lat`
* `lng`
* `radius` (default is 100 if not specified)
* `description`
* `tags`
* custom field ID as a string, e.g. `"131312"` (see [entity/fields](../../commons/entity/fields.md))
* `undefined` (if a meaning of a field is not known)

For custom fields of type `employee` and `multi_employee`, values are matched with the user's employees by full name. For `multi_employee` fields, multiple names can be listed separated by commas or semicolons.

A place is not imported if a required custom field is missing or holds an invalid value. Invalid values of non-required custom fields are skipped with a warning.

***

_Required sub-user rights:_ `place_update`.

{% openapi-operation spec="navixy-platform" path="/place/import/start" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 15 - Too many requests (rate limit exceeded) - if too many imports in progress
* 233 - No data file - if the preloaded file is not found
* 234 - Invalid data format - if the file is not a TSV
* 247 - Entity already exists - there is another identical import with the same file

***

{% openapi-operation spec="navixy-platform" path="/place/import/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in database (if import is not found)

***

{% openapi-operation spec="navixy-platform" path="/place/import/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/place/import/download_failed" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in database (if import is not found)
* 204 - Entity not found (if file is not found)

***

{% openapi-operation spec="navixy-platform" path="/place/import/finish" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in database (if import is not found)
* 280 - Invalid import request state (if the import process is still in progress)

