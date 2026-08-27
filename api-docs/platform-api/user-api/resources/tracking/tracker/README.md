---
title: Tracker
description: The device registered in the Navixy platform, and the operations that register, read, configure, and command it.
---

# Tracker

A tracker is a device registered in the Navixy platform: the physical unit fitted to a vehicle or carried by a person, together with everything the platform knows about it. It is the central entity of this API, and most other resources hang off it.

The operations here cover the tracker as a whole. Registering one and replacing its hardware, reading its current state and last readings, sending commands to it, and deleting it. Everything narrower lives on its own page: [sensors](sensor/README.md), [counters](counter.md), [rules](rules/rule.md), [settings](settings/README.md), [groups](group.md), and the rest of this section.

{% include "https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/~/reusable/nl6xxobcEsdA5WkJrhd8/" %}

## Tracker object structure

{% openapi-schemas spec="navixy-platform" schemas="Tracker,TrackerSource,TagBinding" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

### Tracker output info

{% openapi-schemas spec="navixy-platform" schemas="TrackerOutput" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

### Tracker input types

A discrete input on a tracker carries one of these types:

* `ignition` - the vehicle's ignition. A tracker can have only one sensor of this type.
* `engine` - the engine's working status.
* `mass` - the vehicle's ground.
* `car_alarm` - expected to be on when the car alarm has triggered.
* `sos_button` - an emergency red button.
* `hood` - on when the engine hood is open.
* `door` - on when a door is open.
* `car_lock` - on when the central lock is open.
* `custom` - a user-defined type. It should normally carry a non-empty `name`.

## API actions

API base path: `/tracker`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/tracker/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.

***

This call is walked through in [how to get a list of GPS trackers](../../../guides/data-retrieval/get-tracker-list.md).

{% openapi-operation spec="navixy-platform" path="/tracker/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `tracker_register`.

{% openapi-operation spec="navixy-platform" path="/tracker/corrupt" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The tracker is already connected to the server, or the user lacks the right.
* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 219 - The tracker is itself a clone.
* 243 - The device is already connected.
* 252 - The device is already corrupted.

***

_Required sub-user rights:_ `admin`, available only to master users.

{% openapi-operation spec="navixy-platform" path="/tracker/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 249 - The tracker is not a clone. This call deletes clones only.
* 203 - Delete entity associated with. The response body carries the blocking references, so a caller can detach them and retry:

```json
{
  "success": false,
  "status": {
    "code": 203,
    "description": "Delete entity associated with"
  },
  "rules": [10]
}
```

or

```json
{
  "success": false,
  "status": {
    "code": 203,
    "description": "Delete entity associated with"
  },
  "vehicles": [11]
}
```

`rules` lists the associated rule IDs and `vehicles` the associated vehicle IDs.

***

_Required sub-user rights:_ `tracker_configure`.

{% openapi-operation spec="navixy-platform" path="/tracker/change_phone" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - The device has no GSM module.
* 219 - The tracker is a clone.
* 223 - The phone number is already used by another device.
* 241 - The number belongs to a SIM bundled with the device, which cannot be changed here.

***

{% openapi-operation spec="navixy-platform" path="/tracker/get_diagnostics" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

#### Input names

These can appear in `inputs`:

* `obd_consumption`
* `obd_rpm`
* `obd_fuel`
* `obd_coolant_t`
* `obd_intake_air_t`
* `obd_throttle`
* `obd_speed`
* `obd_engine_load`
* `obd_absolute_load_value` (normalised air mass per intake stroke, in percent)
* `obd_control_module_voltage` (in volts)
* `obd_time_since_engine_start` (run time since engine start, in seconds)
* `obd_mil_run_time` (in minutes)
* `can_engine_temp`
* `can_engine_hours`
* `can_mileage`
* `can_throttle`
* `can_consumption`
* `can_rpm`
* `can_speed`
* `can_r_prefix`
* `can_coolant_t`
* `can_intake_air_t`
* `can_engine_load`
* `can_adblue_level`
* `can_fuel_rate` (instant fuel consumption, in litres per hour)
* `raw_can_x`, where x is 1 to 16
* `can_axle_load_x`, where x is 1 to 15

#### State names

These can appear in `states`:

* `obd_vin` (string)
* `obd_dtc_number` (integer, the number of DTC codes)
* `obd_dtc_codes` (string, the DTC codes)
* `obd_dtc_cleared_distance` (double, distance travelled since codes were cleared, in km)
* `obd_mil_activated_distance` (double, distance travelled with MIL on, in km)
* `hardware_key` (string, driver identification key)
* `external_power_state` (string, connected or disconnected)
* `driver_ident_state` (string, identified or not identified)
* `tacho_vin` (string)
* `tacho_card1_sn` (string)
* `tacho_card2_sn` (string)
* `tacho_vin_last_download` (string)
* `tacho_card1_last_download` (string)
* `tacho_card2_last_download` (string)
* `can_hood_state` (string, 0 for close or 1 for open)
* `can_airbag_state` (string, 0 for normal or 1 for malfunction)
* `can_trunk_state` (string, 0 for close or 1 for open)
* `can_seat_belt_driver_state` (string, 0 for untied or 1 for tied)
* `can_seat_belt_passenger_state` (string, 0 for untied or 1 for tied)
* `can_door_state` (string, 0 for close or 1 for open)
* `can_door_driver_state` (string, 0 for close or 1 for open)
* `can_door_passenger_state` (string, 0 for close or 1 for open)

Every input, state, and definition can also be looked up with [`tracker/sensor/input_name/list`](sensor/input_name.md#post-tracker-sensor-input_name-list).

***

{% openapi-operation spec="navixy-platform" path="/tracker/get_fuel" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

#### Input names

These can appear in `inputs`:

* `fuel_level`
* `fuel_frequency`
* `lls_level_x`, where x is 1 to 16
* `fuel_consumption`
* `rs232_x`, where x is 1 to 6
* `can_fuel` (fuel level in percent or in unknown units)
* `can_fuel_2` (fuel level in percent or in unknown units)
* `can_fuel_litres` (fuel level in litres)
* `can_fuel_economy` (fuel economy in km per litre)

***

{% openapi-operation spec="navixy-platform" path="/tracker/get_inputs" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

{% openapi-operation spec="navixy-platform" path="/tracker/batch_get_inputs" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 217 - One of the listed trackers does not exist or is blocked.
* 221 - Too many IDs were passed.

***

{% openapi-operation spec="navixy-platform" path="/tracker/get_outputs" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

{% openapi-operation spec="navixy-platform" path="/tracker/batch_get_outputs" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 217 - One of the listed trackers does not exist or is blocked.
* 221 - Too many IDs were passed.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/output/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

{% openapi-operation spec="navixy-platform" path="/tracker/get_last_gps_point" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

{% openapi-operation spec="navixy-platform" path="/tracker/get_readings" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

#### Input names

These can appear in `inputs`:

* `composite`
* `input_status`
* `analog_x`, where x is 1 to 8
* `freq_x`, where x is 1 to 8
* `impulse_counter_x`, where x is 1 to 8
* `fuel_temperature`
* `lls_temperature_x`, where x is 1 to 16
* `rs232_x`, where x is 1 to 6
* `board_voltage`
* `temp_sensor`
* `ext_temp_sensor_x`, where x is 1 to 10

***

{% openapi-operation spec="navixy-platform" path="/tracker/get_state" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

{% openapi-operation spec="navixy-platform" path="/tracker/get_states" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - A tracker is corrupted and `allow_not_exist` is `false`.
* 208 - A tracker is blocked and `list_blocked` is `false`.
* 217 - The list names trackers that do not exist and `allow_not_exist` is `false`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/list_models" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

A model's `has_custom_fields` flag matters when configuring measurement: when it is `true` the protocol transmits field names, which is what allows a custom `input_name` to be set on the tracker's [sensors](sensor/README.md).

#### ID type

An ID type says what identifier a model uses, which is what [`tracker/register`](#post-tracker-register) needs in order to register the device.

Possible values are:

* `imei` - the device uses its IMEI, for example `356938035643809`. See the [IMEI article on Wikipedia](https://en.wikipedia.org/wiki/International_Mobile_Equipment_Identity). Pass digits only, with no spaces or hyphens.
* `meid` - the device uses a MEID of 14 hexadecimal digits, for example `A10000009296F2`. See the [MEID article on Wikipedia](https://en.wikipedia.org/wiki/Mobile_equipment_identifier).
* `id,n` - the device uses an n-digit factory identifier. For example `id,7` means a 7-digit number such as `1234567`.
* `n,m` - an n-digit generated identifier starting with m. The device has a configurable ID that the platform generates and configures itself, so no identifier is passed at registration.
* `ascii,min,max` - the device uses a text identifier of Latin letters, digits, and hyphens, for example `fleet-sensor-0042`. The two numbers are the minimum and maximum accepted length in characters, so `ascii,12,17` means 12 to 17 characters. Punctuation, underscores, spaces, and non-Latin characters are rejected.

{% hint style="warning" %}
Models reporting `ascii,6,64`, `navixy_ngp` among them, reject any identifier longer than 32 characters. The declared maximum of 64 is not applied. Registration errors always name the range that is enforced, for example `must consist of 6-32 alphanumeric symbols or hyphens`, so treat the error message as authoritative when it disagrees with the model metadata.
{% endhint %}

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/tags/set" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/tracker/location_request" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 213 - The device is offline.
* 214 - The device does not support this request type.
* 256 - The location is already current.

***

_Required sub-user rights:_ `tracker_register`.

Registration is walked through in [how to activate a device](../../../guides/device-management/activate-device.md).

`group_id` must name a group that exists. List them with [`group/list`](group.md#post-tracker-group-list), or pass `0` for no group.

{% hint style="info" %}
Because of the variety of tracker models and business applications, there are different ways to register a tracker. They are called [registration plugins](../../commons/plugin/README.md), and each has its own set of additional parameters. Alongside the parameters the operation lists, pass everything the plugin you chose requires.
{% endhint %}

{% openapi-operation spec="navixy-platform" path="/tracker/register" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

Registering a Queclink GV55Lite through plugin 37 into the default group, with the device identified by IMEI, also needs `phone`, `apn_name`, `apn_user`, `apn_password`, and `activation_code`, because that plugin requires them:

{% code overflow="wrap" %}
```sh
curl -L \
  --request POST \
  --url 'https://api.eu.navixy.com/v2/tracker/register' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: NVX your_api_key' \
  --data '{"label": "Courier", "group_id": 0, "plugin_id": 37, "model": "qlgv55lite", "phone": "79123122312", "activation_code": "123123123", "device_id": "123451234512346", "apn_name": "fast.tmobile.com", "apn_user": "tmobile", "apn_password": "tmobile"}'
```
{% endcode %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 7 - Invalid parameters. A rejected device ID names the offending parameter and the rule it broke:

```json
{
  "success": false,
  "status": {
    "code": 7,
    "description": "Invalid parameters"
  },
  "errors": [
    {
      "parameter": "device_id",
      "error": "must consist of 6-32 alphanumeric symbols or hyphens"
    }
  ]
}
```

***

_Required sub-user rights:_ `tracker_register`.

{% openapi-operation spec="navixy-platform" path="/tracker/register_retry" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The user lacks the right.
* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - The device has no GSM module.
* 219 - The tracker is a clone.
* 242 - The device is already connected.

***

_Required sub-user rights:_ `tracker_register`.

{% openapi-operation spec="navixy-platform" path="/tracker/register_quick" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The user lacks the right.
* 201 - No bundle with this IMEI.
* 204 - The group does not exist.
* 220 - Unknown device model.
* 221 - The dealer's device limit is exceeded.
* 222 - The plugin was not found or does not support this model.
* 223 - The phone number is already in use.
* 224 - The device ID is already registered.
* 225 - The tariff is not compatible with the account's legal type.
* 226 - Unknown ICCID.
* 227 - The activation code is unknown or already used.

***

_Required sub-user rights:_ `tracker_configure`.

{% hint style="info" %}
Replacement uses the same [registration plugins](../../commons/plugin/README.md) as registration, so pass whatever extra parameters your plugin requires alongside the ones the operation lists. An activation code is not used when replacing a device.
{% endhint %}

{% openapi-operation spec="navixy-platform" path="/tracker/replace" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The user lacks the right.
* 204 - The group does not exist.
* 220 - Unknown device model.
* 221 - The dealer's device limit is exceeded.
* 222 - The plugin was not found or does not support this model.
* 223 - The phone number is already in use.
* 224 - The device ID is already registered.
* 225 - The tariff is not compatible with the account's legal type.
* 226 - Unknown ICCID, plugin specific.
* 258 - No bundle for this device ID, plugin specific.
* 266 - The device is not activated yet.

***

_Required sub-user rights:_ `tracker_configure`.

{% openapi-operation spec="navixy-platform" path="/tracker/replace_quick" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The user lacks the right.
* 201 - No bundle with this IMEI.
* 204 - The group does not exist.
* 220 - Unknown device model.
* 221 - The dealer's device limit is exceeded.
* 222 - The plugin was not found or does not support this model.
* 223 - The phone number is already in use.
* 224 - The device ID is already registered.
* 225 - The tariff is not compatible with the account's legal type.
* 226 - Unknown ICCID.
* 266 - The device is not activated yet.

***

_Required sub-user rights:_ `tracker_configure`.

{% openapi-operation spec="navixy-platform" path="/tracker/replace_retry" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The user lacks the right.
* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - The device has no GSM module.
* 219 - The tracker is a clone.
* 242 - The device is already connected.
* 266 - The old device is not activated yet.

***

_Required sub-user rights:_ `tracker_configure`, `tracker_set_output`.

Which commands a device accepts follows from the `special_control` field of its [model](#post-tracker-list_models):

| Special control | Available commands |
| --------------- | ------------------ |
| `jointech_lock_password` | `electronic_lock_command`, `set_special_settings_command` |
| `hhd_lock_password` | `electronic_lock_command`, `set_special_settings_command` |
| `vg_lock_password` | `electronic_lock_command`, `set_special_settings_command` |
| any other special control | `set_special_settings_command` |

`electronic_lock_command` seals or unseals an electronic lock:

```json
{
  "name": "electronic_lock_command",
  "command_code": "unseal",
  "special_settings": {<special settings JSON object>}
}
```

`set_special_settings_command` carries only the settings object:

```json
{
  "name": "set_special_settings_command",
  "special_settings": {<special settings JSON object>}
}
```

In both cases `special_settings` is equivalent to the body of [`tracker/settings/special/update`](settings/special/README.md#post-tracker-settings-special-update). See the [special settings JSON object](settings/special/README.md#post-tracker-settings-special-read) for its structure.

{% openapi-operation spec="navixy-platform" path="/tracker/send_command" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `tracker_configure`, `tracker_set_output`.

This call is walked through in [how to send commands to a GPS tracker](../../../guides/device-management/send-commands.md).

{% openapi-operation spec="navixy-platform" path="/tracker/raw_command/send" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 7 - Invalid parameters. A malformed command names the parameter:

```json
{
  "success": false,
  "status": {
    "code": 7,
    "description": "Invalid parameters"
  },
  "errors": [
    {
      "parameter": "command",
      "error": "Non-hex string"
    }
  ]
}
```

## More in this section

<!-- endpoint-reference:start -->

#### Alarm mode

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/alarm_mode/read`](alarm_mode.md#post-tracker-alarm_mode-read) | POST | Read alarm mode |
| [`/tracker/alarm_mode/set`](alarm_mode.md#post-tracker-alarm_mode-set) | POST | Turn alarm mode on or off |

#### APN settings by tracker

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/apn_settings/read`](apn_settings.md#post-tracker-apn_settings-read) | POST | Read APN settings by tracker |

#### Avatar

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/avatar/upload`](avatar.md#post-tracker-avatar-upload) | POST | Upload a tracker avatar |

#### Chat

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/chat/list`](chat.md#post-tracker-chat-list) | POST | List chat messages |
| [`/tracker/chat/mark_read_all`](chat.md#post-tracker-chat-mark_read_all) | POST | Mark all messages read |
| [`/tracker/chat/mark_read`](chat.md#post-tracker-chat-mark_read) | POST | Mark messages read |
| [`/tracker/chat/send`](chat.md#post-tracker-chat-send) | POST | Send a chat message |
| [`/tracker/chat/broadcast`](chat.md#post-tracker-chat-broadcast) | POST | Send a chat message to several trackers |
| [`/tracker/chat/updated/list`](chat.md#post-tracker-chat-updated-list) | POST | Read last message times |
| [`/tracker/chat/unread/count`](chat.md#post-tracker-chat-unread-count) | POST | Count unread messages |

#### Contact

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/contact/list`](contact.md#post-contact-list) | POST | List contacts and their shared trackers (deprecated) |

#### Counters

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/counter/read`](counter.md#post-tracker-counter-read) | POST | Read a counter |
| [`/tracker/counter/update`](counter.md#post-tracker-counter-update) | POST | Update a counter |
| [`/tracker/get_counters`](counter.md#post-tracker-get_counters) | POST | Read all counter values |
| [`/tracker/counter/value/get`](counter.md#post-tracker-counter-value-get) | POST | Read one counter value |
| [`/tracker/counter/value/list`](counter.md#post-tracker-counter-value-list) | POST | Read one counter across several trackers |
| [`/tracker/counter/value/set`](counter.md#post-tracker-counter-value-set) | POST | Set a counter value |
| [`/tracker/counter/data/read`](counter.md#post-tracker-counter-data-read) | POST | Read counter history |

#### Datalogger

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/datalogger/upload`](datalogger.md#post-tracker-datalogger-upload) | POST | Upload datalogger track data |

#### Employee

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/employee/assign`](employee.md#post-tracker-employee-assign) | POST | Assign or unassign an employee |
| [`/tracker/employee/read`](employee.md#post-tracker-employee-read) | POST | Read the assigned employee |

#### Engine immobilizer

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/engine_immobilizer/read`](engine_immobilizer.md#post-tracker-engine_immobilizer-read) | POST | Read immobiliser state |
| [`/tracker/engine_immobilizer/set`](engine_immobilizer.md#post-tracker-engine_immobilizer-set) | POST | Engage or release the immobiliser |

#### Group

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/group/assign`](group.md#post-tracker-group-assign) | POST | Assign trackers to a group |
| [`/tracker/group/create`](group.md#post-tracker-group-create) | POST | Create a group |
| [`/tracker/group/delete`](group.md#post-tracker-group-delete) | POST | Delete a group |
| [`/tracker/group/list`](group.md#post-tracker-group-list) | POST | List tracker groups |
| [`/tracker/group/update`](group.md#post-tracker-group-update) | POST | Update a group |

#### LED

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/led/read`](led.md#post-tracker-led-read) | POST | Read LED state |
| [`/tracker/led/update`](led.md#post-tracker-led-update) | POST | Switch the LED |

#### Mobile app register

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/mobile/register`](mobile.md#post-tracker-mobile-register) | POST | Register a mobile application (deprecated) |

#### Digital output

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/output/set_all`](output.md#post-tracker-output-set_all) | POST | Switch all outputs |
| [`/tracker/output/set`](output.md#post-tracker-output-set) | POST | Switch one output |

#### Sensor readings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/readings/list`](readings.md#post-tracker-readings-list) | POST | Read all sensor values |
| [`/tracker/readings/batch_list`](readings.md#post-tracker-readings-batch_list) | POST | Read all sensor values for several trackers |

#### Retranslator binding

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/retranslator/bind`](retranslator.md#post-tracker-retranslator-bind) | POST | Bind a tracker to a retranslator |
| [`/tracker/retranslator/list`](retranslator.md#post-tracker-retranslator-list) | POST | List a tracker's retranslator bindings |
| [`/tracker/retranslator/unbind`](retranslator.md#post-tracker-retranslator-unbind) | POST | Unbind a tracker from a retranslator |

#### Trusted number

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/trusted_number/list`](trusted_number.md#post-tracker-trusted_number-list) | POST | List trusted numbers |
| [`/tracker/trusted_number/update`](trusted_number.md#post-tracker-trusted_number-update) | POST | Replace the trusted numbers |

#### Unconfirmed commands

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/command/unconfirmed/count`](command/README.md#post-tracker-command-unconfirmed-count) | POST | Count queued SMS commands |
| [`/tracker/command/unconfirmed/reset`](command/README.md#post-tracker-command-unconfirmed-reset) | POST | Clear the SMS command queue |

#### Rule

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/rule/bind`](rules/rule.md#post-tracker-rule-bind) | POST | Bind a rule to trackers |
| [`/tracker/rule/create`](rules/rule.md#post-tracker-rule-create) | POST | Create a rule |
| [`/tracker/rule/delete`](rules/rule.md#post-tracker-rule-delete) | POST | Delete a rule |
| [`/tracker/rule/list`](rules/rule.md#post-tracker-rule-list) | POST | List rules |
| [`/tracker/rule/unbind`](rules/rule.md#post-tracker-rule-unbind) | POST | Unbind a rule from trackers |
| [`/tracker/rule/update`](rules/rule.md#post-tracker-rule-update) | POST | Update a rule |

#### Sensor

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/sensor/batch_list`](sensor/README.md#post-tracker-sensor-batch_list) | POST | List sensors for several trackers |
| [`/tracker/sensor/create`](sensor/README.md#post-tracker-sensor-create) | POST | Create a sensor |
| [`/tracker/sensor/delete`](sensor/README.md#post-tracker-sensor-delete) | POST | Delete a sensor |
| [`/tracker/sensor/list`](sensor/README.md#post-tracker-sensor-list) | POST | List a tracker's sensors |
| [`/tracker/sensor/update`](sensor/README.md#post-tracker-sensor-update) | POST | Update a sensor |
| [`/tracker/sensor/batch_copy`](sensor/README.md#post-tracker-sensor-batch_copy) | POST | Copy sensors between trackers |
| [`/tracker/sensor/data/read`](sensor/README.md#post-tracker-sensor-data-read) | POST | Read sensor history |

#### Sensor calibration data

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/sensor/calibration_data/read`](sensor/calibration_data.md#post-tracker-sensor-calibration_data-read) | POST | Read calibration data |
| [`/tracker/sensor/calibration_data/update`](sensor/calibration_data.md#post-tracker-sensor-calibration_data-update) | POST | Replace calibration data |
| [`/tracker/sensor/calibration_data/upload_omnicomm`](sensor/calibration_data.md#post-tracker-sensor-calibration_data-upload_omnicomm) | POST | Upload an Omnicomm calibration file |

#### Sensor inputs

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/sensor/input_name/list`](sensor/input_name.md#post-tracker-sensor-input_name-list) | POST | List all sensor inputs and state fields |

#### Settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/read`](settings/README.md#post-tracker-settings-read) | POST | Read label and group |
| [`/tracker/settings/update`](settings/README.md#post-tracker-settings-update) | POST | Change label and group |

#### LBS settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/lbs/read`](settings/lbs.md#post-tracker-settings-lbs-read) | POST | Read the LBS radius limit |
| [`/tracker/settings/lbs/update`](settings/lbs.md#post-tracker-settings-lbs-update) | POST | Change the LBS radius limit |

#### Tracking mode

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/tracking/read`](settings/tracking.md#post-tracker-settings-tracking-read) | POST | Read tracking mode settings |
| [`/tracker/settings/tracking/update`](settings/tracking.md#post-tracker-settings-tracking-update) | POST | Change tracking mode settings |

#### Parking detection

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/trip_detection/read`](settings/trip_detection.md#post-tracker-settings-trip_detection-read) | POST | Read parking detection settings |
| [`/tracker/settings/trip_detection/update`](settings/trip_detection.md#post-tracker-settings-trip_detection-update) | POST | Change parking detection settings |

#### Special

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/special/read`](settings/special/README.md#post-tracker-settings-special-read) | POST | Read special settings |
| [`/tracker/settings/special/update`](settings/special/README.md#post-tracker-settings-special-update) | POST | Change special settings |

#### Engine hours

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/stats/engine_hours/read`](stats/stats_engine_hours.md#post-tracker-stats-engine_hours-read) | POST | Read engine hours |

#### Mileage

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/stats/mileage/read`](stats/stats_mileage.md#post-tracker-stats-mileage-read) | POST | Read mileage per day |

#### Commands

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/command/create`](commands.md#post-tracker-command-create) | POST | Create a command |
| [`/tracker/command/update`](commands.md#post-tracker-command-update) | POST | Update a command |
| [`/tracker/command/execute`](commands.md#post-tracker-command-execute) | POST | Execute a command |
| [`/tracker/command/delete`](commands.md#post-tracker-command-delete) | POST | Delete a command |
| [`/tracker/batch_get_commands`](commands.md#post-tracker-batch_get_commands) | POST | Read commands for several trackers |

<!-- endpoint-reference:end -->
