---
title: Task form values
description: Correct the values of a form that has already been submitted from the mobile app.
---

# Task form values

This operation corrects a form that was already filled. It exists for fixing data submitted by mistake, not for filling an empty form: values can only be updated when the form was previously submitted from the Mobile Tracker App, for [Android](https://play.google.com/store/apps/details?id=com.navixy.xgps.tracker\&hl=ru) or [iOS](https://apps.apple.com/us/app/x-gps-tracker/id802887190).

When it can be called depends on the task's state. Any caller can update values while the task is `unassigned`, `assigned`, or `arrived`, and the device must not be deleted. A sub-user holding the `completed_form_update` right can also update values once the task is `done`, `failed`, or `delayed`.

## API actions

API base path: `/task/form/values`.

The `values` object maps each form field ID, as a string, to a value object:

```json
{
    "1223344": {
      "type": "text",
      "value": "text field value"
    }
}
```

For **value** object description, see [Form fields and values](../../form/field-types.md).

***

_Required sub-user rights:_ `task_update`.

```json
{
    "success": false,
    "status": {
        "code": 242,
        "description": "There were errors during content validation"
    },
    "errors": [
        {
            "field_id": "111-aaa-whatever",
            "code": 5,
            "error": "text length constraints are not met"
        }
    ]
}
```

Validation error codes:

* 1 - field required but has no value.
* 2 - field value type doesn't match field type.
* 3 - field value is null.
* 4 - value index out of bounds.
* 5 - invalid value size.
* 6 - value less than minimum.
* 7 - value more than maximum.
* 8 - field contains invalid references.
* 9 - invalid file type.
* 10 - invalid file state.

{% openapi-operation spec="navixy-platform" path="/task/form/values/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 101 - In demo mode this function disabled - if current user has "demo" flag.
* 201 - Not found in the database - if task with the specified ID does not exist.
* 255 - Invalid task state - if current task state is not `unassigned`, `assigned` or `arrived` (plus `done`, `failed`, and `delayed`  for users with `completed_form_update` right).
* 242 - There were errors during content validation - if given values are invalid for the form. Example:

