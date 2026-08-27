---
title: Manufacturer-specific settings
description: Special settings objects that exist only for ATrack, Yatut, and Suntech device models.
---

# Manufacturer-specific settings

Three [special settings](README.md) objects exist only for one manufacturer's models each. They are documented here rather than with the general special settings because nothing else accepts them.

* [`engine_control_atrack`](#engine_control_atrack) sets how ATrack models decide the engine changed state.
* [`guard_mode_yatut`](#guard_mode_yatut) configures the motion sensor and perimeter guard on Yatut models.
* [`harsh_behavior_suntech`](#harsh_behavior_suntech) sets the harsh driving thresholds on Suntech models.

Each is sent as the settings object of [`tracker/settings/special/update`](README.md#post-tracker-settings-special-update).

## `engine_control_atrack`

Engine event behaviour for ATrack models. The device reports the engine as on once the supply voltage stays above `power_voltage_high_level` for `on_duration_seconds`, and as off once it stays below `power_voltage_low_level` for `off_duration_seconds`.

```json
{
  "power_voltage_high_level": 13800,
  "on_duration_seconds": 120,
  "power_voltage_low_level": 12800,
  "off_duration_seconds": 300
}
```

* `power_voltage_high_level` - int. Voltage in 0.001 volts for detecting the engine ON state. Min 0, max 30000, default 13800 mV.
* `on_duration_seconds` - int. Seconds that must elapse before the engine state change is accepted. Min 0, max 600, default 1.
* `power_voltage_low_level` - int. Voltage in 0.001 volts for detecting the engine OFF state. Min 0, max 30000, default 12800 mV.
* `off_duration_seconds` - int. Seconds that must elapse before the engine state change is accepted. Min 0, max 600, default 5.

## `guard_mode_yatut`

Guard settings for Yatut models. Two independent guards are configured here, each with its own mode: a motion sensor, and a perimeter set by diameter.

```json
{
  "motion_sensor_mode": "double_period",
  "motion_sensor_first_period": "23:00-07:00",
  "motion_sensor_second_period": "10:00-17:00",
  "motion_sensor_amplitude": 10,
  "motion_sensor_duration": 30,
  "motion_sensor_ignore_time": 50,
  "motion_sensor_double_check": false,
  "perimeter_mode": "once_triggering",
  "perimeter_diameter": 1
}
```

* `motion_sensor_mode` - [enum](../../../../../../general/api-conventions.md#data-types). One of `off`, `permanent`, `single_period`, `double_period`. Default `off`.
* `motion_sensor_first_period` - string time in `HH:mm-HH:mm` format, default `23:00-07:00`. Required when `motion_sensor_mode` is `single_period` or `double_period`.
* `motion_sensor_second_period` - string time in `HH:mm-HH:mm` format, default `10:00-17:00`. Required when `motion_sensor_mode` is `double_period`.
* `motion_sensor_amplitude` - int. Min 1, max 255, default 5. Required unless `motion_sensor_mode` is `off`.
* `motion_sensor_duration` - int seconds. Min 1, max 255, default 5. Required unless `motion_sensor_mode` is `off`.
* `motion_sensor_ignore_time` - int minutes. Min 5, max 99, default 5. Required unless `motion_sensor_mode` is `off`.
* `motion_sensor_double_check` - boolean. Default `false`. Required unless `motion_sensor_mode` is `off`.
* `perimeter_mode` - [enum](../../../../../../general/api-conventions.md#data-types). One of `off`, `once_triggering`, `permanent`, `point_displacement`. Default `off`.
* `perimeter_diameter` - int kilometres. Min 1, max 999, default 1. Required unless `perimeter_mode` is `off`.

## `harsh_behavior_suntech`

Harsh driving thresholds for Suntech models. Each force is the limit above which the device reports a harsh event.

```json
{
  "mode": "enable",
  "max_acceleration_force": 1.5,
  "max_braking_force": 0.05,
  "max_cornering_force": 3,
  "type": "harsh_behavior_suntech"
}
```

* `mode` - string. Either `enable` or `disable`.
* `max_acceleration_force` - double. From 0.05 to 3.0 g.
* `max_braking_force` - double. From 0.05 to 3.0 g.
* `max_cornering_force` - double. From 0.05 to 3.0 g.
