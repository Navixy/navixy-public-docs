---
title: Sensor
description: The three kinds of sensor a tracker can carry, and the operations that manage them.
---

# Sensor

A sensor turns what a device reports into something meaningful. The device sends a raw input, such as a voltage on a wire or a value on the CAN bus, and the sensor says what that input means, what units it is in, and how to convert it.

Sensors come in three kinds, and which fields apply depends on which kind you are creating:

* A **metering** sensor reads a continuous quantity, such as fuel level or temperature.
* A **discrete input** reads a two-state input, such as ignition on or off.
* A **virtual** sensor derives its value from another input rather than from a wire of its own.

Two constraints apply across all of them. A discrete input number, and a metering sensor's input name, can each be used only once per tracker, and reusing one returns error 232. The number of sensors sharing a `sensor_type` is also capped, reported as error 270. For a walkthrough, see [how to retrieve sensor and counter data](../../../../guides/data-retrieval/sensor-data.md).

## Sensor sub-types

### Metering sensor

```json
{
  "type": "metering",
  "id": 860250,
  "sensor_type": "temperature",
  "name": "OBD Coolant temperature",
  "input_name": "obd_coolant_t",
  "divider": 1.0,
  "accuracy": 0.0,
  "units": "",
  "units_type": "celsius",
  "parameters": {
    "parent_ids": [123042, 123566],
    "volume": 0.7,
    "min": 0.0,
    "max": 12.0,
    "max_lowering_by_time": 120.0,
    "max_lowering_by_mileage": 120.0,
    "ignore_drains_in_move": true,
    "ignore_refuels_in_move": false,
    "refuel_gap_minutes": 11,
    "custom_field_name": false
  }
}
```

* `type` - string. Always `metering`.
* `id` - int. Sensor ID.
* `sensor_type` - [metering sensor type](README.md#metering-sensor-type-values).
* `name` - string, max 100 characters.
* `input_name` - string, max 64 characters. The source input field the sensor reads.
* `divider` - double. Factor the raw value is divided by.
* `accuracy` - double. From `0.0` to `100.0` in steps of `0.25`.
* `units` - string. Free-text unit label.
* `units_type` - [enum](../../../../../general/api-conventions.md#data-types). Unit type for the sensor.
* `parameters` - optional object with additional parameters.
  * `parent_ids` - optional int array. Component sensors of a composite sensor.
  * `volume` - optional double. Volume for a composite sensor.
  * `min` - optional double. Lowest acceptable raw value.
  * `max` - optional double. Highest acceptable raw value.
  * `max_lowering_by_time` - optional double. Largest legitimate drop per hour.
  * `max_lowering_by_mileage` - optional double. Largest legitimate drop per 100 km.
  * `ignore_drains_in_move` - optional boolean, default `false`. When `true`, fuel drains are not detected while the vehicle moves.
  * `ignore_refuels_in_move` - optional boolean, default `false`. When `true`, refuels are not detected while the vehicle moves.
  * `refuel_gap_minutes` - optional int, default `5`. Minutes after movement starts during which refuels are still detected.
  * `custom_field_name` - optional boolean, default `false`. Whether `input_name` holds a value the user entered rather than a known field. This applies only when the [tracker model](../#post-tracker-list_models) reports the `has_custom_fields` feature.

#### Metering sensor type values

* `fuel`
* `temperature`
* `rpm`
* `custom`
* `fuel_consumption`
* `instant_consumption`
* `power`
* `speed`
* `flow_meter`
* `acceleration`

### Discrete input

```json
{
  "type": "discrete",
  "id": 888951,
  "sensor_type": "ignition",
  "name": "Ignition",
  "input_number": 4
}
```

* `type` - string. Always `discrete`.
* `id` - int. Sensor ID.
* `sensor_type` - [discrete sensor type](README.md#discrete-sensor-type-values).
* `name` - string, max 100 characters.
* `input_number` - int from 1 to 8. The physical input the sensor is wired to.

#### Discrete sensor type values

* `ignition`
* `sos_button`
* `power`
* `engine`
* `car_alarm`
* `door`
* `charge`
* `detach`
* `custom`

### Virtual sensor

```json
{
  "type": "virtual",
  "id": 1700049,
  "sensor_type": "virtual_ignition",
  "name": "Virtual Ignition",
  "input_name": "board_voltage",
  "custom_field_name": false,
  "parameters": {
    "calc_method": "in_range",
    "range_from": 13.4,
    "value_titles": [
      {
        "value": "0",
        "title": "Off"
      }, {
        "value": "1",
        "title": "On"
      }
    ]
  }
}
```

* `type` - string. Always `virtual`.
* `id` - int. Sensor ID.
* `sensor_type` - [virtual sensor type](README.md#virtual-sensor-type-values). Use `virtual_ignition` for virtual ignition and `state` for everything else.
* `name` - string, max 100 characters.
* `input_name` - string, max 64 characters. The source input field name.
* `custom_field_name` - optional boolean, default `false`. Whether `input_name` holds a value the user entered rather than a known field. This applies only when the [tracker model](../#post-tracker-list_models) reports the `has_custom_fields` feature.
* `parameters` - optional object with additional parameters.
  * `calc_method` - [enum](../../../../../general/api-conventions.md#data-types). How the value is calculated: `in_range`, `identity`, or `bit_index`.
  * `range_from` - double. Lower bound of the range. Used only with the `in_range` method.
  * `range_to` - double. Upper bound of the range. Used only with the `in_range` method.
  * `bit_index` - int from 1 upwards. Which bit of the source value to read. Used only with the `bit_index` method.
  * `value_titles` - optional mapping that gives sensor values readable titles.
    * `value` - string, max 64 characters. The sensor value.
    * `title` - string, max 64 characters. The title shown for that value.

#### Virtual sensor type values

* `state` - custom virtual sensor
* `virtual_ignition` - virtual ignition sensor

Virtual sensors carry their own rules:

* A tracker can have only one virtual sensor of type `virtual_ignition`.
* The `in_range` method needs `range_from`, `range_to`, or both.
* The `bit_index` method needs `bit_index`.
* There can be at most 100 value titles.
* Values must be unique within `value_titles`.

## API actions

API base path: `/tracker/sensor`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/sensor/batch_list" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 217 - One of the listed trackers does not exist or is blocked.
* 221 - Too many IDs were passed.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/sensor/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 219 - The tracker is a clone.
* 232 - That input number or input name is already used on this tracker.
* 270 - Too many sensors of this type.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/sensor/delete" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - The sensor does not exist or belongs to another user.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 219 - The tracker is a clone.

***

{% openapi-operation spec="navixy-platform" path="/tracker/sensor/list" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/sensor/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - The sensor does not exist or belongs to another user.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 219 - The tracker is a clone.
* 232 - That input number or input name is already used on this tracker.

***

{% hint style="danger" %}
**Important.** This operation deletes the sensors of the target trackers, and some sensor data can be lost.
{% endhint %}

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/sensor/batch_copy" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 272 - The base tracker and one of the targets have different models.

***

{% openapi-operation spec="navixy-platform" path="/tracker/sensor/data/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 211 - The period is longer than the Navixy platform allows.
* 228 - The sensor is neither metering nor virtual.

## More in this section

<!-- endpoint-reference:start -->

#### Sensor calibration data

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/sensor/calibration_data/read`](calibration_data.md#post-tracker-sensor-calibration_data-read) | POST | Read calibration data |
| [`/tracker/sensor/calibration_data/update`](calibration_data.md#post-tracker-sensor-calibration_data-update) | POST | Replace calibration data |
| [`/tracker/sensor/calibration_data/upload_omnicomm`](calibration_data.md#post-tracker-sensor-calibration_data-upload_omnicomm) | POST | Upload an Omnicomm calibration file |

#### Sensor inputs

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/sensor/input_name/list`](input_name.md#post-tracker-sensor-input_name-list) | POST | List all sensor inputs and state fields |

<!-- endpoint-reference:end -->
