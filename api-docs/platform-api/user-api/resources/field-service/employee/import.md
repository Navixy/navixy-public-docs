---
title: Employee import
description: Create many employees at once from a spreadsheet, through a background import process.
---

# Employee import

Employee import creates many [employees](README.md) at once from a spreadsheet, rather than one call per person.

Convert the spreadsheet first with [`data/spreadsheet/parse`](../../commons/data.md#post-data-spreadsheet-parse), which stores it as a tab-separated file and returns its name. Pass that name to `employee/import/start` as `filename`, follow the process with `read` or `list`, and collect the rows that failed validation with `download_failed`.

## API actions

API base path: `/employee/import`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

Create the TSV file first with [/data/spreadsheet/parse](../../commons/data.md#post-data-spreadsheet-parse), which returns its name as `file_id`. Pass that name to start as `filename`.

Available fields for `headers`:

* `first_name`
* `middle_name`
* `last_name`
* `email`
* `phone`
* `driver_license_number`
* `driver_license_cats`
* `driver_license_issue_date`
* `driver_license_valid_till`
* `hardware_key`
* `address`
* `lat`
* `lng`
* `radius`
* `personnel_number`
* `ssn`
* `tracker_label`
* `tags`
* `undefined` (if a meaning of a field is not known)

***

_Required sub-user rights:_ `employee_update`.

{% openapi-operation spec="navixy-platform" path="/employee/import/start" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 15 - Too many requests (rate limit exceeded) - if too many imports in progress
* 233 - No data file
* 234 - Invalid data format
* 247 - Entity already exists - there is another identical import with the same file

***

{% openapi-operation spec="navixy-platform" path="/employee/import/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in database (if import is not found)

***

{% openapi-operation spec="navixy-platform" path="/employee/import/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/employee/import/download_failed" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in database (if import is not found)
* 204 - Entity not found (if file is not found)

