---
title: Form fields and values
description: The field types a form can hold, with the parameters each takes and the shape of the value it produces.
---

# Form fields and values

A [form](README.md) and a [form template](template.md) both hold an ordered list of fields. A field's type decides what the person filling it sees, and how what they enter is validated.

Every field carries the same common parameters, plus type-specific ones that set its style and its validation rules. Both sit as keys of one JSON object.

The values of a submitted form are stored separately, and their shape is entirely type-specific. Each entry below gives three things: the type identifier to send, the type-specific parameters that configure the field, and the value object it produces once filled.

**Common field parameters:**

```json
{
   "id": "Text-1",
   "label": "Name",
   "description": "Your full name",
   "required": true,
   "type": "text"
}
```

* `id` - arbitrary alphanumeric string (1 to 19 characters). Unique across current form's fields, used to link with values and its "parent" in template form.
* `label` - string. User-defined label, shown as field header, 1 to 100 printable characters.
* `description` - string. Field description, shown in smaller text under the header, 1 to 512 printable characters.
* `required` - boolean. If `true`, form cannot be submitted without filling this field with valid value.
* `type` - string. Determines field type.

### Text field

**type**: `text`.

Multiline auto-expanding text field.

**Note 1:** when value contains empty string, it's considered empty, and thus valid when `required: false, min_length != 0`. **Note 2:** combination `required: true, min_length: 0` is not allowed.

**Type-specific parameters:**

```json
{
    "min_length": 5,
    "max_length": 255
}
```

* `min_length` - int. Minimum allowed length, from 0 to 1024.
* `max_length` - int. Maximum allowed length 1 to 1024.

**Value object:**

```json
{
    "type": "text",
    "value": "text field value"
}
```

* `value` - string. What was entered the text field.

### Checkbox group

**type**: `checkbox_group`.

Group of checkboxes.

**Note 1:** when zero checkboxes selected, values considered empty, and thus valid when `required: false, min_checked != 0`. **Note 2:** combination `required: true, min_checked: 0` is not allowed.

**Type-specific parameters:**

```json
{
    "min_checked": 0,
    "max_checked": 3,
    "group": [{
           "label" : "I agree to TOS"
    }]
}
```

* `min_checked` - int. Minimum allowed checked positions, 0 to "group".size - 1.
* `max_checked` - int. Maximum allowed checked positions, 1 to "group".size - 1.

**Value object:**

```json
{
    "type": "checkbox_group",
    "values": [true]
}
```

* `values` - array of boolean. They are in the same order as fields in `group`.

### Dropdown field

**type**: `dropdown`.

Dropdown menu for choosing one option.

**Type-specific parameters:**

```json
{
    "options": [
        {
           "label" : "John"
        },
        {
           "label" : "Alice"
        }
    ]
}
```

**Value object:**

```json
{
    "type": "dropdown",
    "value_index": 1
}
```

* `value_index` - int. Zero-based index of value from "options".

### Radio button group

**type**: `radio_group`.

A group of radio buttons. Only one option is selectable.

**Type-specific parameters:**

```json
{
    "options": [
        {
           "label" : "John"
        },
        {
           "label" : "Alice"
        }
    ]
}
```

**Value object:**

```json
{
    "type": "radio_group",
    "value_index": 1
}
```

* `value_index` - int. Zero-based index of value from "options".

### Date picker

**type**: `date`.

A date picker.

**Type-specific parameters:**

```json
{
    "disable_future": false,
    "disable_past": true
}
```

* `disable_future` - boolean. If `true`, date from the future cannot be selected.
* `disable_past` - boolean. If `true`, date from the past cannot be selected.

**Value object:**

```json
{
    "type": "date",
    "value": "2017-03-14"
}
```

* `value` - [date/time](../../../../general/api-conventions.md#data-types).

### Rating

**type**: `rating`.

Rating with "stars". Zero stars not allowed.

**Type-specific parameters:**

```json
{
  "max_stars": 5
}
```

* `max_stars` - int. Max number of stars to select from.

**Value object:**

```json
{
    "type": "rating",
    "value": 3
}
```

* `value` - int. Number of stars selected. Cannot be more than `max_stars`.

### File

**type**: `file`.

File attachment. For example, document or spreadsheet.

**Type-specific parameters:**

```json
{
    "max_file_size": 65536,
    "min_file_size": 128,
    "allowed_extensions": ["xls", "doc"]
}
```

* `max_file_size` - int. Max file size, bytes, no more than 16 Mb.
* `min_file_size` - int. Minimum file size, bytes.
* `allowed_extensions` - [enum](../../../../general/api-conventions.md#data-types) array. List of allowed file extensions, up to 16 items, cannot be empty, but can be null, which means "no extension limits".

**Value object:**

```json
{
    "type": "file",
    "file_ids": [3345345]
}
```

* `file_ids` - int array. IDs of the file which should be attached to this form field as value. Files must be uploaded before form submission.

### Photo

**type**: `photo`.

Photograph attachment.

**Type-specific parameters:**

```json
{
  "max_files": 2
}
```

* `max_files` - int. Maximum number of photos to attach, up to 6.

**Value object:**

```json
{
    "type": "photo",
    "file_ids": [3345345, 534534534]
}
```

* `file_ids` - int array. IDs of the files which should be attached to this form field as value. Files must be uploaded before form submission. Only image files allowed.

### Signature

**type**: `signature`.

A small image of customer's signature (usually obtained via writing on screen with a stylus).

**Type-specific parameters:**

* there are no type-specific parameters.

**Value object:**

```json
{
    "type": "file",
    "file_id": 3345345
}
```

* `file_id` - int. An ID of the file which should be attached to this form field as value. File must be uploaded before form submission.

### Separator

**type**: `separator`.

Cosmetic, just to show header. Doesn't contain any actual value. Always filled and valid. Cannot be required.
