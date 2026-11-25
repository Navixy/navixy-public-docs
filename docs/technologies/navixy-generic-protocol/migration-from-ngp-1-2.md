---
description: >-
  Move a sender from NGP 1.2 to NGP 4.0: the migration steps, the packet field
  for each NGP 1.2 key, the conversions, and the name rules.
tags:
  - reference
---

# Migration from NGP 1.2

This page is for developers whose devices or systems send Navixy Generic Protocol (NGP) 1.2, the latest JSON version, and now move to NGP 4.0. It gives the migration steps. For each NGP 1.2 key, it gives the NGP 4.0 field and the conversion that the value needs. The [NGP documentation for Navixy 3](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-generic-protocol) describes NGP 1.2. Navixy 3 is the previous generation of the Navixy platform.

The page uses the terms defined in [Key concepts](overview.md#key-concepts), such as packet, attribute, attribute name, channel, and value field. On this page, your converter is the code in your sender that converts NGP 1.2 messages into NGP 4.0 packets.

## What changes between the versions

NGP 1.2 and NGP 4.0 describe the same readings, but they encode and send them differently:

| Property | NGP 1.2 | NGP 4.0 |
| --- | --- | --- |
| Encoding | JSON | Protocol Buffers (protobuf) |
| Transport | HTTP, HTTPS, or MQTT | TCP, without encryption. See [Regional hosts](protocol-specification/transport-layer.md#regional-hosts). |
| Messages in one request | One | A batch of packets |
| Reply | HTTP status code, or none over MQTT | An acknowledgment with the batch's sequence number. It doesn't show packets that Navixy didn't save: see [What an acknowledgment confirms](protocol-specification/how-navixy-saves-packets.md#what-an-acknowledgment-confirms). |
| Device | Registered with the NGP model | Activated with the **NGP device** model in Location Intelligence |
| Time format | ISO 8601 text | Milliseconds since the Unix epoch, UTC |
| Readings outside the `location` object | Keys at the JSON object's top level | Attributes in the `attributes` list |

## Migration steps

To move a sender from NGP 1.2 to NGP 4.0, follow these steps:

1. Check the device identifiers. NGP 4.0 has no credentials, so the identifiers must be hard to guess: see [Device identifiers instead of credentials](protocol-specification/transport-layer.md#device-identifiers-instead-of-credentials).
2. If an identifier is easy to guess, give the device a new random identifier. For example, an IMEI, the serial number of a GPS device's modem, is easy to guess.
3. In your converter, keep a table that maps each old identifier to the new identifier. The converter then sends the new identifier in `device_id`.
4. Activate each device in Location Intelligence with the **NGP device** model and the identifier from steps 1 and 2. The model is under the vendor **Navixy**. Navixy doesn't use the NGP 1.2 device registrations. See [Activate a device](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/device-management/activate-device).
5. Activate a separate NGP device for tests of the converter. Don't send test packets with a real device's identifier. A test packet with the current time stops Navixy from saving this device's older messages.
6. Generate code for your language from the [message definition](protocol-specification/message-structure-and-attributes.md#message-definition).
7. Replace the HTTP or MQTT client with a TCP client that sends batches in frames and reads the acknowledgments. See [Transport layer](protocol-specification/transport-layer.md).
8. Convert each message's location keys into packet fields. See [Location and top-level keys](#location-and-top-level-keys).
9. Convert each message's other keys into attributes. See [Standard readings](#standard-readings) and [Custom attributes](#custom-attributes).
10. Send converted test messages for the test device from step 5.
11. Check that Navixy saved the test messages. See [How to check that Navixy saved data](protocol-specification/how-navixy-saves-packets.md#how-to-check-that-navixy-saved-data).
12. For each real device, send its queued NGP 1.2 messages over NGP 4.0, oldest first. Send them before any of its new data. See [Send queued NGP 1.2 messages](#send-queued-ngp-1.2-messages).
13. Then send each real device's new data over NGP 4.0.
14. Check that Navixy saves each real device's data. See [How to check that Navixy saved data](protocol-specification/how-navixy-saves-packets.md#how-to-check-that-navixy-saved-data).

## Location and top-level keys

The following table gives the `Navixy3Packet` field for each top-level NGP 1.2 key and each key in the `location` object:

| NGP 1.2 key | NGP 4.0 field | Conversion |
| --- | --- | --- |
| `device_id` | `device_id` | None, unless step 2 of [Migration steps](#migration-steps) gave the device a new identifier. Then send the new identifier. |
| `message_time` | `time_ms` | Convert the ISO 8601 time to milliseconds since the Unix epoch. See [Convert times](#convert-times). |
| `version` | None | Leave it out. NGP 4.0 has no version field. |
| `location.latitude` | `latitude` | None. |
| `location.longitude` | `longitude` | None. |
| `location.altitude` | `altitude` | None. |
| `location.speed` | `speed` | Leave the field out when the value is above the range in [Packet fields](protocol-specification/message-structure-and-attributes.md#packet-fields). |
| `location.heading` | `heading` | Send 0 when the value is 359.9999 or more, as [Packet fields](protocol-specification/message-structure-and-attributes.md#packet-fields) explains. |
| `location.satellites` | `satellites` | None. |
| `location.hdop` | `hdop` | Leave the field out when the value is above the range in [Packet fields](protocol-specification/message-structure-and-attributes.md#packet-fields). |
| `location.vdop` | The `vdop` attribute | Send it as a `double_value`. |
| `location.pdop` | The `pdop` attribute | Send it as a `double_value`. |
| `location.gnss_time` | `fix_time_ms` | Convert the ISO 8601 time to milliseconds since the Unix epoch. See [Convert times](#convert-times). |
| `location.fix_type` | `fix_type` | Use the value from [Fix type values](#fix-type-values). If the `location` object has no `fix_type`, send `FIX_TYPE_FIX`, because NGP 1.2 treated a missing fix type as `HAS_FIX`. If the message has no `location`, leave out `fix_type`. |
| `location.source_type` | `position_source` | Send `POSITION_SOURCE_GNSS` for `GNSS`, and `POSITION_SOURCE_DEVICE_LBS` for `LBS`. For `ATLAS`, see [Data without an NGP 4.0 field](#data-without-an-ngp-4.0-field). If the `location` object has no `source_type`, send `POSITION_SOURCE_GNSS`, because NGP 1.2 treated a missing source as `GNSS`. If the message has no `location`, leave out `position_source`. |
| `location.precision` | `accuracy` | None. Both versions use meters. |
| `event_id` | `event_id` | None. Navixy saves the number but doesn't use the meanings that the [NGP 1.x event identifiers](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-generic-protocol/predefined-event-identifiers) define. See [Event IDs](protocol-specification/message-structure-and-attributes.md#event-ids). |

### Fix type values

Convert each NGP 1.2 `fix_type` text as the following table shows:

| NGP 1.2 fix type | NGP 4.0 fix type |
| --- | --- |
| `HAS_FIX` | `FIX_TYPE_FIX` |
| `FIX_2D` | `FIX_TYPE_2D` |
| `FIX_3D` | `FIX_TYPE_3D` |
| `NO_FIX` | `FIX_TYPE_NO_FIX`. If the coordinates are the device's own last fix, send `FIX_TYPE_LAST_KNOWN` instead. |
| `LAST_KNOWN_POSITION` | `FIX_TYPE_LAST_KNOWN` |

### Convert times

NGP 1.2 times are in UTC, and a time without an offset, such as `2024-09-02T10:03:43`, is also in UTC. Some senders still add an offset, such as `+03:00`. The following Python function converts an NGP 1.2 time into milliseconds since the Unix epoch. It reads times with and without an offset, and fractions of a second with any number of digits:

```python
import re
from datetime import datetime, timedelta, timezone

EPOCH = datetime(1970, 1, 1, tzinfo=timezone.utc)


def to_epoch_ms(text):
    text = text.replace("Z", "+00:00")
    # Python 3.10 and earlier read a fraction only with 3 or 6 digits.
    text = re.sub(r"\.(\d+)", lambda m: "." + (m.group(1) + "000000")[:6], text)
    moment = datetime.fromisoformat(text)
    if moment.tzinfo is None:
        moment = moment.replace(tzinfo=timezone.utc)
    return (moment - EPOCH) // timedelta(milliseconds=1)
```

For example, `to_epoch_ms("2024-09-02T10:03:43Z")` returns `1725271423000`.

## Standard readings

Each remaining top-level key becomes an attribute in the packet's `attributes` list. The exceptions are `custom_attributes`, which [Structured custom attributes in NGP 1.1a](#structured-custom-attributes-in-ngp-1.1a) describes, and the keys in [Data without an NGP 4.0 field](#data-without-an-ngp-4.0-field). Most NGP 1.2 keys keep their names. Only the following 2 keys change:

| NGP 1.2 key | NGP 4.0 attribute name | Value field |
| --- | --- | --- |
| `is_moving` | `moving` | `bool_value` |
| `hardware_mileage` | `hw_mileage` | `double_value` |

The other standard NGP 1.2 keys keep their names, such as `battery_level` and `temperature_N`. Use the value field that [Standard attribute names](protocol-specification/message-structure-and-attributes.md#standard-attribute-names) gives.

`temperature_internal` and `humidity_internal` also keep their names, but they aren't standard NGP 4.0 attribute names: Navixy defines no meaning or unit for them. Send both in `double_value`.

Navixy also renames a few names that other devices use. For example, Navixy saves `door_state_2` as `door_open`, channel 2. If your messages have such names, see [Names that Navixy renames](protocol-specification/message-structure-and-attributes.md#names-that-navixy-renames).

## Custom attributes

A custom attribute is a top-level key that isn't a [standard NGP 1.2 key](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-generic-protocol/message-structure-and-attributes), such as `custom_fuel` or `engine_rpm`. Your converter sends it as an attribute with the key as its `name`. When a key breaks the name rules, your converter fixes the key first, as [Build a valid name](protocol-specification/message-structure-and-attributes.md#build-a-valid-name) describes. For example, `EngineHours` becomes `engine_hours`, and `temp-` becomes `temp`, not `temp_`.

Choose one value field for each attribute name in your converter, and use it in every packet, as [Attributes](protocol-specification/message-structure-and-attributes.md#attributes) describes. If one attribute name gets values of different types, the device's readings for this name have different types, and they're harder to compare.

Don't choose the value field by the type of each JSON value. JSON has one number type, and many JSON libraries write `12.0` as `12`. So one reading can arrive as a whole number in one message and as a decimal number in the next message. When you don't know what a reading measures, send every number as `double_value`.

### Structured custom attributes in NGP 1.1a

Some senders still send NGP 1.1a messages with structured custom attributes. In NGP 1.1a, `custom_attributes` is an array of objects. Each object has `type`, `value`, and the optional `id` and `units`:

```json
{
    "message_time": "2026-11-24T10:00:00Z",
    "device_id": "fleet7-3f9c2a81",
    "version": "1.1a",
    "custom_attributes": [
        { "type": "engine_rpm", "id": 1, "value": 2100, "units": "rpm" },
        { "type": "Coolant-Temp", "value": 87.5 }
    ]
}
```

Convert each object into an attribute in the following way:

* The `type` is the attribute's `name`. Fix it when it breaks the name rules: `Coolant-Temp` becomes `coolant_temp`.
* Put the `value` into the value field that you chose for the name. Both readings are measurements, so both use `double_value`: `engine_rpm` 2100.0 and `coolant_temp` 87.5.
* Leave out `units`. NGP 4.0 has no field for it.
* Decide how to handle `id` once for each `type`, for all messages of your sender:
  * If no message has 2 objects with this `type`, leave out `id`.
  * Otherwise, give every object with this `type` a channel, also in a message with only one such object:
    * If the objects have an `id`, use it as the channel, such as `engine_rpm_1`.
    * If they have no `id`, use the object's position among the objects of this `type`: 1 for the first, 2 for the second. Keep the same order in every message.

In the example, the sender never puts 2 objects with the same `type` in one message. So the converter leaves out `id` and sends `engine_rpm` and `coolant_temp` without a channel.

## Data without an NGP 4.0 field

NGP 4.0 has no field for the following NGP 1.2 data:

| NGP 1.2 data | What to do |
| --- | --- |
| `mobile_cells` | Leave it out. |
| `wifi_points` | Leave it out. |
| `location.source_type` with the value `ATLAS`, a third-party positioning service | Leave `position_source` unset, and send the position's accuracy in `accuracy`. |

## Send queued NGP 1.2 messages

Your sender may have NGP 1.2 messages that it kept and didn't send yet, for example, from a device that was offline. When you send such queued messages, follow these rules:

* Send each device's queued messages oldest first, before any new data from the device. [In which order Navixy saves packets](protocol-specification/how-navixy-saves-packets.md#in-which-order-navixy-saves-packets) explains why.
* Don't send messages that are more than 365 days old. [Which time Navixy uses](protocol-specification/how-navixy-saves-packets.md#which-time-navixy-uses) explains why.

## Example: one message in both versions

The following NGP 1.2 message has a position, an event, and 3 readings:

```json
{
    "message_time": "2024-09-02T10:03:43Z",
    "device_id": "fleet7-3f9c2a81",
    "version": "1.2",
    "location": {
        "gnss_time": "2024-09-02T10:03:41Z",
        "fix_type": "FIX_3D",
        "latitude": 56.348579,
        "longitude": 60.12344,
        "altitude": 271,
        "satellites": 8,
        "speed": 43,
        "heading": 77,
        "source_type": "GNSS"
    },
    "event_id": 406,
    "is_moving": true,
    "battery_level": 93,
    "temperature_2": -13.7
}
```

The following Python code builds the same data as an NGP 4.0 packet, with code generated from `navixy3.proto`:

```python
import navixy3_pb2 as ngp

packet = ngp.Navixy3Packet(
    device_id="fleet7-3f9c2a81",
    time_ms=1725271423000,
    fix_time_ms=1725271421000,
    fix_type=ngp.FIX_TYPE_3D,
    latitude=56.348579,
    longitude=60.12344,
    altitude=271.0,
    satellites=8,
    speed=43.0,
    heading=77.0,
    position_source=ngp.POSITION_SOURCE_GNSS,
    event_id=406,
    attributes=[
        ngp.Attribute(name="moving", bool_value=True),
        ngp.Attribute(name="battery_level", int_value=93),
        ngp.Attribute(name="temperature_2", double_value=-13.7),
    ],
)
batch = ngp.Navixy3Batch(sequence=1, packets=[packet])
```

The example's times are from 2024. A converter takes the times from each message's `message_time` and `gnss_time`. Navixy replaces a time that is more than 365 days old: see [Which time Navixy uses](protocol-specification/how-navixy-saves-packets.md#which-time-navixy-uses).

To send the batch, put it in a frame and write the frame to the TCP connection, as [Getting started](getting-started.md) shows.

## See also

* [Getting started](getting-started.md): Send a first NGP 4.0 packet and check that Navixy saved it.
* [Message structure and attributes](protocol-specification/message-structure-and-attributes.md): Look up every NGP 4.0 packet field and the rules for attribute names.
* [NGP 1.2 message structure](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-generic-protocol/message-structure-and-attributes): Look up the NGP 1.2 keys that this page converts.
