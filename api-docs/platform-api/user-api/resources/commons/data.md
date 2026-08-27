---
title: Data
description: Turn a spreadsheet into the tab-separated file every import operation starts from.
---

# Data

Every import in this API starts here. `data/spreadsheet/parse` takes an uploaded spreadsheet, converts it to a tab-separated file on the server, and returns that file's name along with a preview of the parsed rows.

The returned name is what the import operations take as `filename`: [geofence import](../tracking/zone/import.md), place import, employee import, vehicle import, and the rest. The two remaining operations here read the import processes that follow.

## API actions

API base path: `/data`.

Operations that list no errors of their own return only the [general error codes](../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/data/spreadsheet/parse" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 234 - Invalid data format.

***

{% openapi-operation spec="navixy-platform" path="/data/import/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/data/import/read" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database.
