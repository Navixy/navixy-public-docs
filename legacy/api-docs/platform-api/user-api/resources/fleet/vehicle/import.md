---
title: Vehicle import
description: Create many vehicles at once from a spreadsheet, through a background import process.
---

# Vehicle import

Vehicle import creates many [vehicles](README.md) at once from a spreadsheet, rather than one call per vehicle.

Convert the spreadsheet first with [`data/spreadsheet/parse`](../../commons/data.md#post-data-spreadsheet-parse), which stores it as a tab-separated file and returns its name. Pass that name to `vehicle/import/start` as `filename`, follow the process with `read` or `list`, and collect the rows that failed validation with `download_failed`.

## API actions

API base path: `/vehicle/import`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

Starting the background process of importing vehicles.

***

_Required sub-user rights:_ `vehicle_update`.

Available fields:

* `label`
* `model`
* `max_speed`
* `type`
* `subtype`
* `garage`
* `trailer`
* `manufacture_year`
* `color`
* `additional_info`
* `trailer_reg_number`
* `reg_number`
* `chassis_number`
* `frame_number`
* `vin`
* `passengers`
* `payload_weight`
* `payload_height`
* `payload_width`
* `payload_length`
* `gross_weight`
* `fuel_grade`
* `fuel_tank_volume`
* `fuel_cost`
* `norm_avg_fuel_consumption`
* `fuel_type`
* `tyre_size`
* `tyres_number`
* `wheel_arrangement`
* `free_insurance_policy_number`
* `liability_insurance_policy_number`
* `free_insurance_valid_till`
* `liability_insurance_valid_till`
* `tracker_label`
* `tags`
* `undefined` (if a meaning of a field is not known)

**Response**

```json
{
  "success": true,
  "id": <int>
}
```

**Example**

cURL

```sh
curl --request POST \
  --url 'https://api.eu.navixy.com/v2/vehicle/import/start' \
  --header 'Content-Type: application/json' \
  --data-binary @- << EOF
{
    "hash": "a6aa75587e5c59c32d347da438505fc3",
    "filename": "tmp-sheet640571613016981796.tsv",
    "headers": ["label", "model", "max_speed", "type", "subtype", "reg_number", "fuel_grade", "fuel_tank_volume", "free_insurance_policy_number", "free_insurance_valid_till", "tracker_label", "tags"],
    "user_headers": [ "Model", "Max speed", "Type", "Subtype", "Reg. number", "Fuel grade", "Fuel tank volume", "Free insurance policy number", "Free insurance valid till", "Object", "Tags"]
}
EOF
```

**Errors**

* 15 - Too many requests (rate limit exceeded) - if too many imports in progress
* 233 - No data file
* 234 - Invalid data format
* 247 - Entity already exists - there is another identical import with the same file

{% openapi-operation spec="navixy-platform" path="/vehicle/import/start" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 15 (rate limit), 233, 234, 247.

***

Reads the state of one import process.

{% openapi-operation spec="navixy-platform" path="/vehicle/import/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

Lists the import processes of the current user.

{% openapi-operation spec="navixy-platform" path="/vehicle/import/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

Downloads the rows the import could not process, so they can be corrected and retried.

{% openapi-operation spec="navixy-platform" path="/vehicle/import/download_failed" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201, 204.

