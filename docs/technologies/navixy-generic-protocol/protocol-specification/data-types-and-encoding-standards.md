---
description: >-
  Learn how NGP 4.0 encodes values: the protobuf types, the time format, text,
  and the difference between a field set to 0 and a field left out.
tags:
  - reference
---

# Data types and encoding standards

This page describes how Navixy Generic Protocol (NGP) encodes values. It's for developers who build packets, especially developers who write their own protobuf encoder. The page uses the terms defined in [Key concepts](../overview.md#key-concepts), such as packet, batch, packet field, and value field. [Message structure and attributes](message-structure-and-attributes.md) describes each field.

NGP encodes every value with a [Protocol Buffers](https://protobuf.dev/programming-guides/encoding/) (protobuf) scalar type. The page lists these types, the time format, and the encoding rules that decide whether Navixy can decode a batch.

## Protobuf types in NGP

NGP uses the following protobuf types:

| Type | Fields | Notes |
| --- | --- | --- |
| `uint32` | `sequence`, `satellites`, `event_id` | Unsigned 32-bit integer. |
| `int64` | `time_ms`, `fix_time_ms` | Signed 64-bit integer. |
| `sint64` | An attribute's `int_value` | Signed 64-bit integer with ZigZag encoding, which encodes negative numbers in fewer bytes. |
| `double` | `latitude`, `longitude`, an attribute's `double_value` | 64-bit floating-point number. |
| `float` | `altitude`, `speed`, `heading`, `hdop`, `accuracy` | 32-bit floating-point number, with about 7 significant digits. In a packet with a position, Navixy checks the range of the encoded 32-bit value. |
| `bool` | `buffered`, an attribute's `bool_value` | `true` or `false`. |
| `string` | `device_id`, an attribute's `name` and `string_value` | UTF-8 text. |
| Enum | `fix_type`, `position_source` | One value from a list of named numbers. For example, `FIX_TYPE_3D` is 4. |

A protobuf compiler generates code that encodes these types for you. If you write the encoder yourself, follow the [protobuf encoding specification](https://protobuf.dev/programming-guides/encoding/). Use ZigZag encoding for `int_value`.

NGP has no type for binary data. To send binary data, such as a sensor's raw output, first encode it as text, for example, in [Base64](https://datatracker.ietf.org/doc/html/rfc4648). Then send the text as a `string_value`.

## Time format

Every NGP time is in milliseconds since the Unix epoch, January 1, 1970, 00:00:00 UTC. The device's time zone doesn't change the value. For example, `1725271423000` is the time `2024-09-02T10:03:43Z`.

Leave out `time_ms`, or send 0, only when the device's clock isn't set. Navixy then uses the receive time: the moment when Navixy receives the packet. If a device switches between packets with and without `time_ms`, Navixy can fail to save a packet: see [In which order Navixy saves packets](how-navixy-saves-packets.md#in-which-order-navixy-saves-packets). For `fix_time_ms`, Navixy ignores 0 or less.

Navixy uses `time_ms` only inside a period around the receive time. [Which time Navixy uses](how-navixy-saves-packets.md#which-time-navixy-uses) gives this period.

## Units of values

Navixy doesn't convert units. Send each value in the unit that [Packet fields](message-structure-and-attributes.md#packet-fields) and [Standard attribute names](message-structure-and-attributes.md#standard-attribute-names) give.

## Fields set to 0 and fields left out

A field with explicit presence is a field for which Navixy can see the difference between the value 0 and a missing field. The following packet fields have explicit presence: `latitude`, `longitude`, `altitude`, `speed`, `heading`, `satellites`, `hdop`, `accuracy`, and `buffered`. For example, a `speed` of 0 means that the device isn't moving. A packet without `speed` means that the device didn't report its speed.

When you set one of these fields to 0, generated code includes the field and its value in the encoded bytes. If you write your own encoder, write the field's tag and then the value 0. The tag gives the field's number and encoding. It's 1 byte for fields 1 to 15, and 2 bytes for `buffered`, field 16. For example, the encoder writes `buffered` set to `false` as the bytes `80 01 00`. An encoder that leaves out zero values sends the field as missing.

`fix_time_ms` also has explicit presence, but Navixy ignores a value of 0 or less.

An attribute's value fields also have explicit presence: `bool_value`, `int_value`, `double_value`, and `string_value`. They're in a `oneof`: a group of fields. A message sets at most one field of the group. Write the field's tag and value even when the value is `false`, `0`, or an empty string. Code that a protobuf compiler generates does this for you. An attribute without any value field has no value, and Navixy skips it.

In `navixy3.proto`, the packet fields with explicit presence have the option `features.field_presence = EXPLICIT`. The other packet and batch fields, and an attribute's `name`, use `IMPLICIT` presence. For fields with implicit presence, Navixy treats 0, an empty string, or the enum value 0 as a field that the packet doesn't have. The following table shows what such a value means for each field:

| Field | Value that means "not set" |
| --- | --- |
| `sequence` | 0. See [Acknowledgments and replies](transport-layer.md#acknowledgments-and-replies) for a batch with `sequence` 0. |
| `device_id` | An empty string. Navixy doesn't save a packet without a device identifier. |
| `time_ms` | 0. Navixy treats any value of 0 or less as unknown. |
| `event_id` | 0 |
| `fix_type` | `FIX_TYPE_UNSPECIFIED` |
| `position_source` | `POSITION_SOURCE_UNSPECIFIED` |
| An attribute's `name` | An empty string. Navixy skips an attribute with an empty `name`. |

## Text encoding

Every `string` field must be valid UTF-8. If one text value in a batch isn't valid UTF-8, Navixy can't decode the batch and doesn't acknowledge it. Check the encoding of text from devices or other systems before you put the text in a packet.

`device_id` has 64 characters at most. Navixy counts Unicode characters, also called code points, not bytes. So an identifier in a non-Latin script can also have 64 characters. An attribute's `name` has 48 characters at most. A `name` has only ASCII characters, so its length is the same in characters and in bytes.

Navixy doesn't save a packet with some text values: see [Which packets Navixy doesn't save](how-navixy-saves-packets.md#which-packets-navixy-doesnt-save).

## Values that the definition doesn't list

Navixy decodes a batch that has fields or enum numbers that `navixy3.proto` doesn't list:

* Navixy ignores a field number that the definition doesn't list.
* Navixy treats a `fix_type` or `position_source` number that the definition doesn't list as the enum value 0, such as `FIX_TYPE_UNSPECIFIED`.

## See also

* [Message structure and attributes](message-structure-and-attributes.md): Look up every packet field and the rules for attribute names.
* [How Navixy saves packets](how-navixy-saves-packets.md): Learn which times Navixy accepts and in which order it saves packets.
* [Transport layer](transport-layer.md): Learn how Navixy replies to a batch that it can't decode.
