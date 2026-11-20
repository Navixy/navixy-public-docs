---
title: Tracker import
description: Activate many devices of one model at once from a spreadsheet, through a background import process.
---

# Tracker import

Tracker import activates many devices of one model at once from a spreadsheet. Each row of the file becomes a [tracker](README.md), registered the same way as [`tracker/register`](README.md#post-tracker-register). The model, the activation method, and the tracker group are the same for every row of the file.

Only a master user can start a tracker import. A sub-user gets error 13, even with the `tracker_register` right.

The import runs as a background process across five operations. Before you start it, make two calls:

1. Read the columns that the model and the activation method need with [`tracker/activation/schema/read`](README.md#post-tracker-activation-schema-read).
2. Convert the spreadsheet with [`data/spreadsheet/parse`](../../commons/data.md#post-data-spreadsheet-parse). It stores the spreadsheet as a tab-separated (TSV) file and returns the file name and the number of rows, `rows_count`.

Then pass the file name to `tracker/import/start` as `filename`, and follow the process with `tracker/import/read` or `tracker/import/list`. Collect the rows that weren't activated with `tracker/import/download_failed`, and close the finished process with `tracker/import/finish`.

## Limits of tracker import

* **At most 1000 rows per file.** Rows where every cell is empty aren't counted. A larger file returns error 7, so check `rows_count` before you start the import.
* **One tracker import at a time per account.** `tracker/import/start` returns error 15 while another tracker import is waiting to start, or has updated its progress in the last 30 minutes. To start a new import while one is still waiting, close the waiting one with `tracker/import/finish`. Error 15 also comes when the account already has as many imports of any type in progress as the server allows, for example zone or employee imports.
* **No mobile apps.** An activation method for a mobile app can't be imported. Its branch in the activation schema has `x-import: false`.

## Columns of the import file

The `headers` parameter of `tracker/import/start` names what each column of the file holds, in order. The accepted names come from the activation schema of the model:

| Column name | Required | Value |
| --- | --- | --- |
| `label` | No | Tracker label: 1 to 60 characters, without `<` or `>`. When the cell is empty, the label is the model name followed by the device ID, or by the line number when the model has no fixed device ID. |
| `device_id` | When the schema has `device_id` in `properties` | The device ID, such as the IMEI, in the format of the schema `pattern`. |
| `register_fields.<field name>` | When the field is in `register_fields.required` and has no `default` | The value of a model field. When the cell is empty, the field gets its `default` value. |
| `phone`, `iccid`, `activation_code`, `apn_name`, `apn_user`, `apn_password` | When the name is in the `required` list of the chosen `oneOf` branch | The parameters of the activation method. |

Some activation methods need at least one parameter of a group, for example `activation_code` or `iccid`. The schema branch of such a method has `anyOf`. The file must have a column for at least one parameter of the group, and each row must fill in at least one of them.

Columns named `undefined`, and columns with a name not listed in the table above, are ignored. When the file has no column for a required parameter, `tracker/import/start` returns error 7 and names the missing columns. The names of a group are joined with `/`, as in `activation_code/iccid`.

## How rows are checked and activated

The server processes the rows in file order. For each row, it does three things:

1. It checks the cells against the activation schema. Required cells must be filled in, and values must match `pattern` and `maxLength`. Model fields are checked the same way as in `tracker/register`: the field `pattern` is searched for inside the value, ignoring case.
2. It checks that the device ID and the phone number don't repeat a row of the same file that is already activated. Device IDs are compared ignoring case.
3. It registers the device the same way as `tracker/register`. When the activation method uses APN settings, the row has a phone number, and all three APN cells are empty, the server looks up the APN settings by the phone number.

A row that fails goes to the file of failed rows, with its errors. The first 25 errors of the import also appear in `progress.errors` of the import process.

Some errors stop the whole import, because every later row would fail in the same way. The import stops when a device limit is exceeded (code 221), when a tracker quota is used up (code 268), or when the tracker group is deleted during the import. The rows after that point aren't processed and go to the file of failed rows.

When the import can't run at all, its status becomes `failed`. For example, this happens when the activation method is no longer available after the import started.

### Row error messages

Row errors are in the user's language. The English texts are:

| Message | Cause |
| --- | --- |
| `<column>: Must not be empty` | A required cell is empty. |
| `<column>: wrong format` | The value doesn't match the format of the column. A model field that doesn't match its `pattern` gets the hint text of the field instead. |
| `<column>: <error>` | `tracker/register` rejected the value of a parameter, for example the label or the APN settings. `<error>` is the error text of the parameter. |
| `Fill in one of the fields: <columns>` | Every cell of an `anyOf` group is empty. |
| `Duplicates line <line>` | The device ID or the phone number repeats a row that this import has already activated. |
| `A device with this ID is already registered` | The device ID is registered already. |
| `The phone number is already in use` | The phone number belongs to another device. |
| `ICCID not found` | The ICCID is unknown to the activation method. |
| `Invalid or already used activation code` | The activation code is unknown or has been used. |
| `The tariff is not available for your customer type` | The plan of the new device doesn't match the legal type of the account. |
| `The device is not found among the dealer's bundles` | The activation method needs a bundle, and none has this device ID. |
| `No tariff found for registration` | No plan was found for the new device. |
| `Device limit exceeded, import stopped` | The device limit of the telematics service provider (TSP), or of the plan of the new device, is exceeded. The import stops. |
| `Devices limit exceeded, import stopped` | The tracker quota of the TSP or of the account is used up. The import stops. |
| `The tracker group was deleted, import stopped` | The group from `group_id` was deleted during the import. The import stops. |
| `Not processed: import stopped` | The row comes after the point where the import stopped. |
| `Failed to activate device` | Any other registration failure. |

`<column>` is the column label from `user_headers`, or the column name from `headers` when `user_headers` isn't given. `<error>`, `<columns>` and `<line>` are replaced with actual values in the message.

## Import process object

The import process object is the same for every import family. For tracker import, `type` is `tracker`, and `params.tracker` has the parameters that `tracker/import/start` received.

{% openapi-schemas spec="navixy-platform" schemas="ImportProcess,ImportLineMessage" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/tracker/import`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ none. Only master users can call this operation.

{% openapi-operation spec="navixy-platform" path="/tracker/import/start" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

The following request activates Queclink GV55 Lite devices from a file through activation method 52, without a tracker group. The file has five columns: label, IMEI, phone number, activation code, and ICCID.

{% code overflow="wrap" %}
```sh
curl -L \
  --request POST \
  --url 'https://api.eu.navixy.com/v2/tracker/import/start' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: NVX your_api_key' \
  --data '{"filename": "tmp-sheet640571613016981796.tsv", "headers": ["label", "device_id", "phone", "activation_code", "iccid"], "user_headers": ["Name", "IMEI", "Phone", "Activation code", "ICCID"], "model": "qlgv55lite", "plugin_id": 52, "group_id": 0}'
```
{% endcode %}

The response has the ID of the new import process:

```json
{
  "success": true,
  "id": 4821
}
```

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 7 - Invalid parameters. The `errors` array names the problem: `headers` has no columns for required parameters or repeats a column name, `filename` has more than 1000 rows, or `plugin_id` is a mobile app method.
* 13 - Operation not permitted, when a sub-user calls the operation.
* 15 - Too many requests, when another tracker import is active, or when the account already has as many imports of any type in progress as the server allows.
* 204 - Entity not found, when the tracker group doesn't exist.
* 220 - Unknown device model.
* 222 - Plugin not found, when the user can't register this model or the activation method isn't available for it.
* 233 - No data file, when the preloaded file is not found.
* 234 - Invalid data format, when the file is not a TSV.
* 247 - Entity already exists, when another import already uses the same file.

A file without a column for a required parameter returns error 7 with the missing column names:

```json
{
  "success": false,
  "status": {
    "code": 7,
    "description": "Invalid parameters"
  },
  "errors": [
    {
      "parameter": "headers",
      "error": "no columns for required fields: device_id, activation_code/iccid"
    }
  ]
}
```

To fix it, add the named columns to the file, or name the existing columns correctly in `headers`, and start the import again.

***

{% openapi-operation spec="navixy-platform" path="/tracker/import/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the import process is not found.

***

{% openapi-operation spec="navixy-platform" path="/tracker/import/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/tracker/import/download_failed" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - Entity not found, when the import hasn't started processing rows yet, so there is no file of failed rows.

An import with no failed rows returns a CSV file with the header row only.

***

{% openapi-operation spec="navixy-platform" path="/tracker/import/finish" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the import process is not found.
* 280 - Invalid import request state, when the import process is still running.
