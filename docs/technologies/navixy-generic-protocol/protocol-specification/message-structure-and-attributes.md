---
description: >-
  Look up the NGP 4.0 message definition, every batch and packet field, the
  rules for attribute names, the standard attribute names, and event IDs.
tags:
  - reference
---

# Message structure and attributes

This page describes what a Navixy Generic Protocol (NGP) batch contains. It's for developers who build NGP packets in device firmware or in a server. The page gives the `.proto` message definition, every batch field and packet field, the rules for attribute names, and event IDs. [Transport layer](transport-layer.md) describes how to send a batch.

The page uses the terms defined in [Key concepts](../overview.md#key-concepts), such as batch, packet, packet field, attribute, attribute name, channel, and value field.

## Message definition

The following `.proto` file defines all NGP messages. Generate code from it with a protobuf compiler that supports [editions](https://protobuf.dev/editions/overview/), the newer syntax for protobuf files. You need `protoc` 27 or later. The Python package `grpcio-tools` 1.66 or later has such a compiler.

{% code title="navixy3.proto" expandable="true" %}
```protobuf
// NGP 4.0 message definition.
//
// Frame: a 4-byte big-endian length, then a Navixy3Batch of at most 512 KiB.
// Navixy replies with a framed Navixy3Ack that has the same sequence.

edition = "2023";

option features.field_presence = IMPLICIT;
option features.enum_type = OPEN;
option features.utf8_validation = VERIFY;

package navixy.nvx3bridge.v1;

message Navixy3Batch {
  uint32 sequence = 1;
  repeated Navixy3Packet packets = 2;
}

message Navixy3Packet {
  // The device identifier, 1 to 64 characters.
  string device_id = 1;
  // Epoch milliseconds, UTC. 0 or less: Navixy uses the time it received the packet.
  int64 time_ms = 2;

  // Send latitude and longitude together, or neither.
  double latitude = 3 [features.field_presence = EXPLICIT];
  double longitude = 4 [features.field_presence = EXPLICIT];
  // Meters above sea level.
  float altitude = 5 [features.field_presence = EXPLICIT];
  // km/h.
  float speed = 6 [features.field_presence = EXPLICIT];
  // Degrees clockwise from north, 0 or more and less than 360.
  float heading = 7 [features.field_presence = EXPLICIT];
  uint32 satellites = 8 [features.field_presence = EXPLICIT];
  FixType fix_type = 9;
  PositionSource position_source = 10;

  // 0: no event.
  uint32 event_id = 11;
  repeated Attribute attributes = 12;

  float hdop = 13 [features.field_presence = EXPLICIT];
  // Meters.
  float accuracy = 14 [features.field_presence = EXPLICIT];
  // Epoch milliseconds, UTC: when the device got the position fix.
  int64 fix_time_ms = 15 [features.field_presence = EXPLICIT];
  // true: the device kept the packet and sends it later.
  bool buffered = 16 [features.field_presence = EXPLICIT];
}

enum FixType {
  FIX_TYPE_UNSPECIFIED = 0;
  FIX_TYPE_NO_FIX = 1;
  FIX_TYPE_FIX = 2;
  FIX_TYPE_2D = 3;
  FIX_TYPE_3D = 4;
  reserved 5;
  FIX_TYPE_LAST_KNOWN = 6;
}

enum PositionSource {
  POSITION_SOURCE_UNSPECIFIED = 0;
  POSITION_SOURCE_GNSS = 1;
  POSITION_SOURCE_DEVICE_LBS = 2;
}

message Attribute {
  string name = 1;
  oneof value {
    bool bool_value = 2;
    sint64 int_value = 3;
    double double_value = 4;
    string string_value = 5;
  }
}

message Navixy3Ack {
  uint32 sequence = 1;
}
```
{% endcode %}

If your protobuf compiler doesn't support editions, use the following proto3 version. For example, an older protobuf compiler for embedded devices may not support editions. The proto3 version needs `protoc` 3.15 or later and produces the same bytes as the editions version. Save it as `navixy3.proto`, so that the generated code has the same name as in the examples:

{% code title="navixy3.proto (proto3)" expandable="true" %}
```protobuf
// NGP 4.0 message definition in proto3 syntax, for compilers without editions support.
// It produces the same bytes as navixy3.proto.

syntax = "proto3";

package navixy.nvx3bridge.v1;

message Navixy3Batch {
  uint32 sequence = 1;
  repeated Navixy3Packet packets = 2;
}

message Navixy3Packet {
  string device_id = 1;
  int64 time_ms = 2;
  optional double latitude = 3;
  optional double longitude = 4;
  optional float altitude = 5;
  optional float speed = 6;
  optional float heading = 7;
  optional uint32 satellites = 8;
  FixType fix_type = 9;
  PositionSource position_source = 10;
  uint32 event_id = 11;
  repeated Attribute attributes = 12;
  optional float hdop = 13;
  optional float accuracy = 14;
  optional int64 fix_time_ms = 15;
  optional bool buffered = 16;
}

enum FixType {
  FIX_TYPE_UNSPECIFIED = 0;
  FIX_TYPE_NO_FIX = 1;
  FIX_TYPE_FIX = 2;
  FIX_TYPE_2D = 3;
  FIX_TYPE_3D = 4;
  reserved 5;
  FIX_TYPE_LAST_KNOWN = 6;
}

enum PositionSource {
  POSITION_SOURCE_UNSPECIFIED = 0;
  POSITION_SOURCE_GNSS = 1;
  POSITION_SOURCE_DEVICE_LBS = 2;
}

message Attribute {
  string name = 1;
  oneof value {
    bool bool_value = 2;
    sint64 int_value = 3;
    double double_value = 4;
    string string_value = 5;
  }
}

message Navixy3Ack {
  uint32 sequence = 1;
}
```
{% endcode %}

The message names start with `Navixy3` for a historical reason: Navixy first designed this format to move data from the previous Navixy platform. The names aren't part of the bytes that you send, so they don't affect the data. Keep the names and the package as they are, so that your code matches these pages.

## Batch fields

A `Navixy3Batch` has 2 fields:

| Field | Type | Description |
| --- | --- | --- |
| `sequence` | `uint32` | A number that you choose for the batch. The acknowledgment returns the same number. |
| `packets` | `Navixy3Packet`, repeated | The batch's packets. |

Follow these rules for `sequence`:

* Start from 1, because an acknowledgment of `sequence` 0 has no content. See [Acknowledgments and replies](transport-layer.md#acknowledgments-and-replies).
* Use a new value for each new batch. After 4,294,967,295, start again from 1.
* When you send a batch again, keep its value, so that the acknowledgment matches the batch.

For the size of a batch, see [Recommended batch size](transport-layer.md#recommended-batch-size).

## Packet fields

A `Navixy3Packet` has the following fields. Only `device_id` is required.

| Packet field | Type | Description |
| --- | --- | --- |
| `device_id` | `string` | The device identifier that you entered when you activated the device, 1 to 64 characters. Required. |
| `time_ms` | `int64` | When the device recorded the readings, in milliseconds since the Unix epoch, UTC. Navixy can use the receive time instead: see [Which time Navixy uses](how-navixy-saves-packets.md#which-time-navixy-uses). |
| `latitude` | `double` | Latitude in decimal degrees, from −90 to 90. |
| `longitude` | `double` | Longitude in decimal degrees, from −180 to 180. |
| `altitude` | `float` | Altitude above sea level, in meters. Any finite number. |
| `speed` | `float` | Speed in km/h, from 0 to 6,553.5. |
| `heading` | `float` | Direction of movement in degrees clockwise from north, 0 or more and less than 360. |
| `satellites` | `uint32` | The number of satellites that the device used for the position, at most 2,147,483,647. |
| `fix_type` | `FixType` | The kind of position fix. See [Fix types](#fix-types). |
| `position_source` | `PositionSource` | The position's source. See [Position sources](#position-sources). |
| `event_id` | `uint32` | The number of an event that the device reports. 0 means no event. See [Event IDs](#event-ids). |
| `attributes` | `Attribute`, repeated | Sensor readings and device states. See [Attributes](#attributes). |
| `hdop` | `float` | Horizontal dilution of precision (HDOP), from 0 to 655.35. A lower value means a more accurate position. |
| `accuracy` | `float` | The position's estimated accuracy, in meters, 0 or more. |
| `fix_time_ms` | `int64` | When the device got the position, in milliseconds since the Unix epoch, UTC. Navixy ignores 0 or less, and can replace the value: see [Which fix time Navixy saves](how-navixy-saves-packets.md#which-fix-time-navixy-saves). |
| `buffered` | `bool` | `true` if the device kept the packet and sends it later. `false` if the device sends the packet when it records it. `buffered` doesn't change the [order rules](how-navixy-saves-packets.md#in-which-order-navixy-saves-packets). |

A `float` has 32 bits, so the encoded value can be rounded. Navixy checks the range of the rounded value. A value such as 359.99999 becomes 360 after rounding, which is outside the `heading` range. To leave a margin, send 0 for any `heading` of 359.9999 or more. In a packet with a position, Navixy doesn't save the packet when a value is outside its range: see [Which packets Navixy doesn't save](how-navixy-saves-packets.md#which-packets-navixy-doesnt-save).

### Position fields

A packet has a position only when it has both `latitude` and `longitude`. Navixy saves the following packet fields only with a position: `altitude`, `speed`, `heading`, `satellites`, `fix_type`, `position_source`, `hdop`, `accuracy`, and `fix_time_ms`. In a packet without a position, Navixy ignores these fields and doesn't check their values.

### Fix types

A position fix is a position that the device calculated. `FixType` shows how the device got it:

| Value | Meaning |
| --- | --- |
| `FIX_TYPE_UNSPECIFIED` | The device didn't report the fix type. |
| `FIX_TYPE_NO_FIX` | Send it when the device's own receiver has no position, but the packet has coordinates from another source, such as a server's estimate. Navixy saves this value only with coordinates. When the device repeats its own last fix, send `FIX_TYPE_LAST_KNOWN` instead. |
| `FIX_TYPE_FIX` | A valid fix of an unknown kind. |
| `FIX_TYPE_2D` | A valid fix without altitude. |
| `FIX_TYPE_3D` | A valid fix with altitude. |
| `FIX_TYPE_LAST_KNOWN` | The device repeats its last known position with current data. In `fix_time_ms`, send the time of that earlier fix. |

The number 5 is reserved. Don't send it: Navixy treats it as `FIX_TYPE_UNSPECIFIED`.

When the device has no coordinates at all, leave out `latitude`, `longitude`, and `fix_type`. Navixy can't save a fix type without a position.

### Position sources

`PositionSource` shows where the coordinates come from:

| Value | Meaning |
| --- | --- |
| `POSITION_SOURCE_UNSPECIFIED` | The source is unknown, or it isn't in this list, such as a third-party positioning service. |
| `POSITION_SOURCE_GNSS` | A satellite navigation system (GNSS), such as GPS, GLONASS, or Galileo. |
| `POSITION_SOURCE_DEVICE_LBS` | The device calculated the position from the cell towers that it detects. This method is called location-based service (LBS). Send the estimated accuracy in `accuracy`. |

## Attributes

An attribute is a named value: a sensor reading or a device state, such as the battery level or the ignition state. It has a `name` and exactly one value field: `bool_value`, `int_value`, `double_value`, or `string_value`. Navixy skips an attribute without a value.

In Python code generated from `navixy3.proto`, the following code creates 3 attributes:

```python
ngp.Attribute(name="battery_level", int_value=68)
ngp.Attribute(name="board_voltage", double_value=13.9)
ngp.Attribute(name="ignition", bool_value=True)
```

For a name in the [Standard attribute names](#standard-attribute-names) or [Names that Navixy renames](#names-that-navixy-renames) table, use the value field that the table gives. For any other name, choose one value field by what the reading measures, and use it in every packet:

* A state that is true or false: `bool_value`
* A count, a code, or a bitmask: `int_value`
* A measurement, also when a value is a whole number: `double_value`
* Text: `string_value`

Navixy doesn't check the value field against the attribute name. For example, Navixy saves `battery_level` with `double_value` 55.5 as 55.5. Use the value field that [Standard attribute names](#standard-attribute-names) gives, so that the readings of all your devices have the same type.

Send only finite numbers in `double_value`, and send no character U+0000 in `string_value`. Navixy doesn't save a packet with such a value: see [Which packets Navixy doesn't save](how-navixy-saves-packets.md#which-packets-navixy-doesnt-save).

### Name rules

An attribute's `name` field has only lowercase Latin letters, digits, and single underscores between parts, such as `battery_level` or `can_engine_temperature`. It starts with a letter and has 48 characters at most.

Navixy checks a `name` in 2 steps:

1. Navixy checks the basic rules: lowercase Latin letters, digits, and underscores only, a letter first, and 48 characters at most. If the `name` breaks a basic rule, Navixy skips this attribute and saves the rest of the packet. Examples: `EngineHours`, `fuel-level`, `9volts`, `_level`.
2. Navixy checks the underscores in a `name` that passes step 1. If the `name` ends with an underscore or has 2 underscores in a row, Navixy doesn't save the packet. Examples: `fuel_`, `fuel__level`.

Step 1 comes first. So for a `name` that breaks both steps, such as `Fuel_`, Navixy skips only this attribute and saves the rest of the packet. Navixy returns no error for a wrong `name`, so check each `name` before you send it.

### Attribute channels

When an attribute's `name` ends with an underscore and a number, the number is the channel. Navixy saves `temperature_2` under the attribute name `temperature`, channel 2. Use channels for several sensors of the same kind.

Follow these rules for channels:

* Channel 0 is valid. `temperature` without a channel and `temperature_0` are different attributes.
* Navixy removes leading zeros, so `temperature_02` is also channel 2.
* A number without an underscore before it is part of the attribute name. `zn_temp1` has the attribute name `zn_temp1` and no channel.
* A channel can be 2,147,483,647 at most. Navixy doesn't save a packet with a larger channel: see [Which packets Navixy doesn't save](how-navixy-saves-packets.md#which-packets-navixy-doesnt-save).

### Standard attribute names

Navixy has a set of standard attribute names. When a reading matches a standard name, use this name: the reading then has the meaning and the unit that Navixy defines for it. Navixy saves every valid `name` as you send it, with 2 exceptions. Navixy reads the [channel](#attribute-channels) at the end of a name, and it renames the names in [Names that Navixy renames](#names-that-navixy-renames).

Navixy has more standard names than the following table lists, mainly for data from a vehicle's onboard computer. For a reading that the table doesn't list, choose a name that follows the [name rules](#name-rules). The saved names in [Names that Navixy renames](#names-that-navixy-renames), such as `door_open`, are standard names too.

The table gives each name as you send it in `name`. `N` stands for the channel, such as `temperature_1` for the first temperature sensor. A device with one sensor of a kind can also send the name without a channel, such as `temperature`. Use the same form in every packet, because Navixy saves `temperature` and `temperature_1` as different attributes.

The following table lists the standard names for the readings that most senders report. Where the table gives a unit, send the value in this unit, because Navixy doesn't convert units:

| Name | Value field | Meaning |
| --- | --- | --- |
| `ignition` | `bool_value` | The ignition is on. |
| `moving` | `bool_value` | The device is moving. |
| `hw_mileage` | `double_value` | Mileage that the device counts, in kilometers. |
| `board_voltage` | `double_value` | External power voltage, in volts. |
| `battery_voltage` | `double_value` | Built-in battery voltage, in volts. |
| `battery_level` | `int_value` | Built-in battery charge, in %. |
| `input_status` | `int_value` | All discrete inputs' states as a bitmask. Bit 0 is input 1. For example, 5 (binary 101) means that inputs 1 and 3 are on. |
| `output_status` | `int_value` | All outputs' states as a bitmask. Bit 0 is output 1. |
| `analog_N` | `double_value` | Analog input voltage, in volts. |
| `temperature_N` | `double_value` | A temperature sensor's reading, in degrees Celsius. |
| `humidity_N` | `double_value` | A relative humidity sensor's reading, in %. |
| `fuel_level_N` | `double_value` | A fuel sensor's reading, in the sensor's own unit. |
| `fuel_temperature_N` | `double_value` | Fuel temperature, in degrees Celsius. |
| `impulse_counter_N` | `int_value` | An impulse counter's reading: the number of impulses. |
| `hardware_key` | `string_value` | A driver or asset identifier from an RFID, iButton, or similar reader. |
| `vin` | `string_value` | The vehicle identification number (VIN). |
| `vdop` | `double_value` | Vertical dilution of precision, without a unit. |
| `pdop` | `double_value` | Position dilution of precision, horizontal and vertical together, without a unit. |

### Names that Navixy renames

Navixy saves a few `name` values under a different attribute name. The value then appears under this attribute name, not under the `name` that you sent.

Most renames apply with or without a channel. For example, `door_state` becomes `door_open`, and `can_engine_temp_3` becomes `can_engine_temperature`, channel 3. The Channel column has 2 values:

* Kept: Navixy renames the name with or without a channel, and the saved attribute keeps the channel that you sent.
* Fixed channel: Navixy renames the name only when you send it exactly as the table shows it. The saved attribute gets the channel from the Name saved column. With an added channel, such as `can_front_left_door_2`, Navixy doesn't rename the name.

The Value field column gives the value field to use for each name. Send temperatures in degrees Celsius and `obd_consumption` in liters per hour. The following table lists the names that Navixy renames:

| Name sent | Name saved | Channel | Value field |
| --- | --- | --- | --- |
| `door_state` | `door_open` | Kept | `bool_value` |
| `can_front_left_door` | `door_open`, channel 1 | Fixed channel | `bool_value` |
| `can_front_right_door` | `door_open`, channel 2 | Fixed channel | `bool_value` |
| `can_rear_left_door` | `door_open`, channel 3 | Fixed channel | `bool_value` |
| `can_rear_right_door` | `door_open`, channel 4 | Fixed channel | `bool_value` |
| `light_state` | `light_on` | Kept | `bool_value` |
| `ext_temp_sensor` | `temperature` | Kept | `double_value` |
| `can_hood_state` | `hood_open` | Kept | `bool_value` |
| `can_trunk_state` | `trunk_open` | Kept | `bool_value` |
| `can_roof_state` | `roof_open` | Kept | `bool_value` |
| `idling_state` | `idling` | Kept | `bool_value` |
| `tacho_card1_sn` | `driver_card`, channel 1 | Fixed channel | `string_value` |
| `tacho_card2_sn` | `driver_card`, channel 2 | Fixed channel | `string_value` |
| `can_engine_temp` | `can_engine_temperature` | Kept | `double_value` |
| `can_coolant_t` | `can_coolant_temperature` | Kept | `double_value` |
| `can_intake_air_t` | `can_intake_air_temperature` | Kept | `double_value` |
| `can_ambient_air_temp` | `can_ambient_air_temperature` | Kept | `double_value` |
| `can_cab_interior_temp` | `can_cab_interior_temperature` | Kept | `double_value` |
| `can_cargo_ambient_temp` | `can_cargo_ambient_temperature` | Kept | `double_value` |
| `can_engine_manifold_temp` | `can_engine_manifold_temperature` | Kept | `double_value` |
| `can_road_surface_temp` | `can_road_surface_temperature` | Kept | `double_value` |
| `can_transmission_oil_temp` | `can_transmission_oil_temperature` | Kept | `double_value` |
| `obd_coolant_t` | `obd_coolant_temperature` | Kept | `double_value` |
| `obd_intake_air_t` | `obd_intake_air_temperature` | Kept | `double_value` |
| `obd_consumption` | `obd_fuel_rate` | Kept | `double_value` |

Navixy checks for duplicates after it renames. For example, a packet with both `door_state_1` and `door_open_1` has the attribute name `door_open`, channel 1, twice. Navixy doesn't save this packet.

### Build a valid name

When you build an attribute's `name` from other text, such as another system's field names, follow these steps:

1. Change the text as the following table shows:

   | If the text | Change it like this | Example |
   | --- | --- | --- |
   | Has capital letters, or words written together | Write the words in lowercase and join them with single underscores. | `EngineHours` to `engine_hours` |
   | Has other characters, such as `-` or a space | Replace each group of these characters with one underscore. | `fuel-level` to `fuel_level` |
   | Has letters outside a to z | Replace each letter with the closest Latin letter, or remove it. | `température` to `temperature` |

2. Remove the underscores at the start and the end. For example, `temp-` becomes `temp`, not `temp_`.
3. Replace 2 or more underscores in a row with a single underscore.
4. If the name starts with a digit, add a word in front. For example, `9volts` becomes `sensor_9volts`.
5. Navixy reads an underscore and digits at the end of a name as a channel. If the digits aren't a channel number, remove the underscore. For example, `sensor_2024` becomes `sensor2024`. The text `123` becomes `sensor_123` in step 4, then `sensor123` in this step.
6. If the name has more than 48 characters, shorten it to a name that differs from your other names.
7. Repeat steps 2 and 5 on the shortened name.
8. For each attribute, find the attribute name and channel that Navixy saves. Apply the renames in [Names that Navixy renames](#names-that-navixy-renames). For example, Navixy saves `ext_temp_sensor_1` as `temperature`, channel 1.
9. If 2 attributes get the same attribute name and channel, change the `name` of one of them.

Navixy doesn't save a packet with duplicates. The following cases cause them:

* Different texts that become the same `name`, such as `Fuel-Level` and `fuel_level`
* A `name` that Navixy renames, together with its standard name, such as `ext_temp_sensor_1` and `temperature_1`
* The same channel in 2 forms, such as `temperature_02` and `temperature_2`

## Event IDs

`event_id` is the number of an event that the device reports, such as a button press or a power cut. 0 means that the packet reports no event.

Navixy saves the number with the packet in the form `nvx3.<event_id>`, for example, `nvx3.406`. Navixy gives the numbers no meaning of its own, so you can choose them. To read the saved code, use the `lastEvents` field of the [deviceStates](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/products/tracking/api-reference/state#devicestates) query in Navixy GraphQL API: `vendorCode` has the value `nvx3.<event_id>`. Use the same number for the same event on all your devices, so that people who read the data can distinguish the events.

## Example batch in Python

The following batch has one packet with a 3D GNSS position, an event, and 6 attributes. It uses Python code generated from `navixy3.proto`:

```python
import navixy3_pb2 as ngp

packet = ngp.Navixy3Packet(
    device_id="fleet7-3f9c2a81",
    time_ms=1725271423000,
    latitude=56.348579,
    longitude=60.12344,
    altitude=271.0,
    speed=43.0,
    heading=77.0,
    satellites=8,
    hdop=0.41,
    fix_type=ngp.FIX_TYPE_3D,
    fix_time_ms=1725271421000,
    position_source=ngp.POSITION_SOURCE_GNSS,
    event_id=406,
    buffered=False,
    attributes=[
        ngp.Attribute(name="ignition", bool_value=True),
        ngp.Attribute(name="moving", bool_value=True),
        ngp.Attribute(name="hw_mileage", double_value=7382.3),
        ngp.Attribute(name="board_voltage", double_value=13.9),
        ngp.Attribute(name="battery_level", int_value=93),
        ngp.Attribute(name="temperature_2", double_value=-13.7),
    ],
)
batch = ngp.Navixy3Batch(sequence=1, packets=[packet])
```

The example's times are fixed values from 2024. A real sender sets them from its clock, because Navixy replaces old times: see [Which time Navixy uses](how-navixy-saves-packets.md#which-time-navixy-uses).

To send the batch, put it in a frame as [Frame format](transport-layer.md#frame-format) describes. [Getting started](../getting-started.md) has a complete script.

## See also

* [Transport layer](transport-layer.md): Learn how to send a batch and how Navixy replies.
* [How Navixy saves packets](how-navixy-saves-packets.md): Learn which packets Navixy saves and in which order.
* [Data types and encoding standards](data-types-and-encoding-standards.md): Learn how NGP encodes numbers, times, and text, and how to leave a packet field out.
