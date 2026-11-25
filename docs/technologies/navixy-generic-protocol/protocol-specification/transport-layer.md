---
description: >-
  Connect to Navixy over TCP to send NGP 4.0 data. Learn the regional hosts,
  the frame format, Navixy's replies, and the connection limits.
tags:
  - reference
---

# Transport layer

This page describes how a Navixy Generic Protocol (NGP) sender connects to Navixy and exchanges data with it. It's for developers who write a sender's network code. The page uses the terms defined in [Key concepts](../overview.md#key-concepts), such as frame, batch, packet, and acknowledgment. [Message structure and attributes](message-structure-and-attributes.md) describes what a batch contains.

A sender opens a TCP connection to the Navixy data gateway, the Navixy service that receives device data. Then the sender and Navixy exchange frames.

## Regional hosts

Connect to the host for your Navixy account's region:

| Region | Host | Port |
| --- | --- | --- |
| EU | `gateway.iot.eu.navixy.com` | `30006` |
| US | `gateway.iot.us.navixy.com` | `30006` |

Use TCP. Navixy doesn't support UDP or TLS on this port, so Navixy receives the data without encryption. If your data must be encrypted, [contact Navixy](https://www.navixy.com/en/contact?utm_source=navixy-docs&utm_campaign=ngp-transport-layer).

## Device identifiers instead of credentials

A sender sends no user name, password, or key. Navixy saves each packet for the device whose identifier is in the packet's `device_id`.

Anyone who knows a device identifier can send data for this device. Use identifiers that other people can't guess, and choose them before you activate the devices. For example, `fleet7-3f9c2a81` is harder to guess than `truck-01`. Don't use the identifiers from the examples on these pages.

## Frame format

Every batch that you send is one frame, and every acknowledgment that Navixy returns is one frame. A frame has 2 parts in the following order:

1. The protobuf message's length, as a 4-byte unsigned integer in big-endian byte order, with the most significant byte first.
2. The protobuf message, with exactly as many bytes as the length gives.

You send a `Navixy3Batch` message, and Navixy returns a `Navixy3Ack` message with your batch's `sequence`. A frame's protobuf message can have 512 KiB at most.

The following frame, in hexadecimal, has a batch with `sequence` 7 and no packets:

```
00 00 00 02 08 07
```

The frame's bytes mean the following:

* `00 00 00 02` is the length, 2 bytes.
* `08` is the protobuf tag of field 1, `sequence`. A tag gives a field's number and encoding. For field 1, the tag is one byte. `07` is the field's value, 7.

Navixy replies with an acknowledgment that has the same `sequence`. `Navixy3Ack` also has `sequence` as field 1, so the reply has the same bytes as the batch:

```
00 00 00 02 08 07
```

A batch without packets and with a `sequence` other than 0 is a quick way to check that a connection to Navixy works.

## Recommended batch size

Put only one device's packets in each batch, and keep each batch under 400 KiB. Navixy accepts frames up to 512 KiB, but each batch must also fit the storage limit:

* For each device in a batch, Navixy stores one record: a copy of the whole batch's bytes, and this device's decoded packets.
* A record can have 1 MiB at most.
* The decoded packets take about as much space as their original bytes. So the record for a 1-device batch is about twice the batch's size. For example, a 400 KiB batch gives a record of about 800 KiB, under 1 MiB.

In a batch with several devices, each device's record has a copy of the whole batch's bytes. So Navixy stores the same bytes once for each device. One device per batch avoids these extra copies. It also makes Navixy store the whole batch or nothing, so you know what a resend does: see [How to handle a missing acknowledgment](#how-to-handle-a-missing-acknowledgment).

When a record is over 1 MiB, Navixy doesn't store it, doesn't acknowledge the batch, and closes the connection. In a batch with several devices, Navixy may already have stored the other devices' records.

## Acknowledgments and replies

Navixy acknowledges every batch that it can decode and store. To store a batch, Navixy writes one record for each device in the batch, as [Recommended batch size](#recommended-batch-size) describes. Then Navixy checks the packets and saves the packets that pass all checks, and returns the acknowledgment. A batch that is too large to store gets no acknowledgment.

Navixy replies to each frame in the following way:

| What you send | What Navixy does |
| --- | --- |
| A batch that Navixy can decode and store | Saves the packets that pass all checks, then returns a `Navixy3Ack` with the batch's `sequence`. Packets that fail a check don't change the reply: see [Which packets Navixy doesn't save](how-navixy-saves-packets.md#which-packets-navixy-doesnt-save). |
| A frame whose length is 0, or a protobuf message that isn't a valid `Navixy3Batch` | Returns nothing. The connection stays open. |
| A frame whose protobuf message is longer than 512 KiB | Closes the connection. |
| A batch whose record for one device is over 1 MiB | Returns nothing and closes the connection. See [Recommended batch size](#recommended-batch-size). |

Protobuf doesn't write a field with implicit presence, such as `sequence`, when its value is 0: see [Fields set to 0 and fields left out](data-types-and-encoding-standards.md#fields-set-to-0-and-fields-left-out). So a batch with `sequence` 0 and no packets has 0 bytes, and Navixy doesn't reply to it. A batch with packets and `sequence` 0 gets an empty acknowledgment, the frame `00 00 00 00`. [Batch fields](message-structure-and-attributes.md#batch-fields) gives the rules for `sequence`.

### How to handle a missing acknowledgment

When no acknowledgment arrives, the next step depends on the connection's state:

| What happens | Likely cause | What to do |
| --- | --- | --- |
| The connection doesn't open: a name lookup error, a refused connection, or a timeout | The host name, the port, or your network | Check your region's host and port `30006`. Check that your network allows outgoing TCP connections to this port. |
| The connection closes right after it opens, before you send a frame | Your IP address already has 1,000 open connections | Close the connections that you don't use. See [Many devices and connections](#many-devices-and-connections). |
| The connection closes right after you send a frame | The length isn't big-endian, or the protobuf message is longer than 512 KiB | Write the length in big-endian byte order. Navixy reads a little-endian length as a very large number and treats the frame as too long. Keep each batch under 400 KiB. |
| The connection closes later, before the acknowledgment arrives | Navixy couldn't store the batch within 3 minutes, the batch is too large to store, or the connection failed | Connect again and send the same batch with the same `sequence`. If the connection closes again after this batch, split the batch into smaller batches. If the batch has only one packet, try again later, or [contact Navixy](https://www.navixy.com/en/contact?utm_source=navixy-docs&utm_campaign=ngp-transport-layer). |
| The connection stays open, but no acknowledgment arrives within 200 seconds | Navixy couldn't decode the batch | Close the connection and connect again. Check that every text value is valid UTF-8, because one invalid string makes the whole batch impossible to decode. Check that the length matches the number of protobuf bytes after it. Then send the corrected batch. |

When a length doesn't match the bytes after it, close the connection. If you keep it open, Navixy reads the next frames from the wrong position and doesn't reply to them.

Navixy stores each device's packets in a batch together, in one record. If the connection closes, Navixy has stored either all of a one-device batch or none of it. You can't tell which, so always send the same batch again. Then no packet is lost in either case. After Navixy stores a batch, it checks and saves the packets, even if the connection closes before the acknowledgment. Navixy applies the [order rules](how-navixy-saves-packets.md#in-which-order-navixy-saves-packets) to the resent batch in the following way:

* If Navixy stored nothing, it handles the resent batch as usual.
* If Navixy stored the batch, it handles the resent packets in the following way:
  * Packets older than the newest saved packet: Navixy doesn't save them again.
  * Packets with the same time as the newest saved packet: Navixy saves a second copy.
  * Packets that got the receive time, because they had no usable `time_ms`: Navixy saves them again with a new receive time.

  If such duplicates matter to your system, remove them when you read the data. To find copies with a new receive time, compare the attribute values, not the times.

## Connection lifetime

Keep the connection open between batches. Send the next batch after the previous batch's acknowledgment arrives, so that you always know which batch to send again after a connection fails.

Navixy usually returns the acknowledgment within a few seconds. If Navixy can't store a batch within 3 minutes, it closes the connection without an acknowledgment. So on an open connection, wait a little longer than 3 minutes, for example, 200 seconds, before you treat an acknowledgment as missing.

Navixy closes a connection that receives no data for 10 minutes. Connect again when your next batch is ready.

## Many devices and connections

You can send many devices' packets over one connection. To send faster, open more connections, up to 1,000 from one IP address. Send all batches of one device over one connection. Don't send one device's batches over 2 connections at the same time, because they could then arrive out of time order.

For each connection, Navixy keeps a list of up to 1,024 devices that recently sent data. When another device sends data and the list is full, Navixy removes the device that sent nothing for the longest time. When this device sends data again, Navixy adds it to the list again. The list doesn't limit the number of devices on a connection and doesn't cause data loss, so you don't need to do anything about it.

## Connection and frame limits

The following table summarizes the limits:

| Limit | Value |
| --- | --- |
| Protobuf message in one frame | 512 KiB. Navixy closes the connection after a longer frame. |
| Recommended batch size | Under 400 KiB, with one device's packets. See [Recommended batch size](#recommended-batch-size). |
| Devices on one connection | No limit. Navixy lists the 1,024 most recently active devices for each connection: see [Many devices and connections](#many-devices-and-connections). |
| Record for one device in a batch | 1 MiB. Navixy closes the connection when a record is larger. See [Recommended batch size](#recommended-batch-size). |
| Time to store a batch | 3 minutes. Navixy closes the connection when it can't store a batch in this time. |
| Connections from one IP address | 1,000 at the same time. Navixy closes a new connection from an address that already has 1,000 open connections. |
| Time without data before Navixy closes a connection | 10 minutes |
| Transport | TCP only, without TLS |

## See also

* [Getting started](../getting-started.md): Send a first batch in Python and read its acknowledgment.
* [Message structure and attributes](message-structure-and-attributes.md): Learn what a batch and a packet contain.
* [How Navixy saves packets](how-navixy-saves-packets.md): Learn which packets Navixy saves after it receives a batch.
