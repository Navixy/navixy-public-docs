---
description: >-
  Learn which NGP 4.0 packets Navixy saves, which time it uses for a packet,
  and in which order it saves a device's packets.
tags:
  - fundamentals
---

# How Navixy saves packets

This page explains what Navixy does with each packet of a batch that it receives. It's for developers who send Navixy Generic Protocol (NGP) data and need to know why Navixy saved a packet or didn't save it. The page uses the terms defined in [Key concepts](../overview.md#key-concepts), such as packet, batch, and acknowledgment.

Navixy checks every packet in 2 services before it saves the packet. The data gateway checks a packet when the batch arrives. Then IoT Logic checks it. Navixy saves only the packets that pass both checks. Tracking and the other Navixy products read only saved packets.

IoT Logic is the Location Intelligence product that processes device data. It processes every device's data, even for a device that isn't in an IoT Logic flow.

## What an acknowledgment confirms

An acknowledgment confirms that Navixy decoded the batch and saved the packets that passed all checks. It doesn't show which packets failed a check. [Acknowledgments and replies](transport-layer.md#acknowledgments-and-replies) describes every reply.

Navixy checks each packet separately. When a packet fails a check, Navixy doesn't save this packet, but it saves the batch's other packets and acknowledges the batch as usual. The acknowledgment is the same whether every packet passed or not, so it can't show you a failed check. To find out whether Navixy saved a packet, see [How to check that Navixy saved data](#how-to-check-that-navixy-saved-data).

## Which packets Navixy doesn't save

Navixy doesn't save a packet in the following cases:

| Check | Navixy doesn't save the packet when |
| --- | --- |
| Device | `device_id` is empty, has more than 64 characters, or belongs to no NGP device in the region of the host that you connect to. After a packet with an unknown identifier, Navixy treats this identifier as unknown for 45 seconds, even if you activate the device. So wait a minute after the activation before you send again. |
| Position values | The packet has a position, and a position field is outside its range in the [Packet fields](message-structure-and-attributes.md#packet-fields) table. This check applies to `latitude`, `longitude`, `altitude`, `speed`, `heading`, `satellites`, `hdop`, and `accuracy`. |
| Finite numbers | A `double_value` is NaN or infinite. |
| Text | A `string_value` has the character U+0000. |
| Channel numbers | An attribute's channel is more than 2,147,483,647. |
| Duplicate attributes | 2 attributes have the same attribute name and channel, after Navixy renames some names. See [Names that Navixy renames](message-structure-and-attributes.md#names-that-navixy-renames). |
| Underscores | An attribute's `name` passes the basic name rules, but ends with an underscore or has 2 underscores in a row. See [Name rules](message-structure-and-attributes.md#name-rules). |
| Time order | The packet's time is earlier than the newest saved packet's time. Navixy compares only with packets from batches that it received before this batch. See [In which order Navixy saves packets](#in-which-order-navixy-saves-packets). |

Navixy checks position fields only in a packet with a position: see [Position fields](message-structure-and-attributes.md#position-fields).

Some problems make Navixy skip only one attribute and save the rest of the packet. Navixy skips an attribute without a value. It also skips an attribute whose `name` breaks a basic name rule, such as `EngineHours`. See [Name rules](message-structure-and-attributes.md#name-rules).

## Which time Navixy uses

Navixy uses a packet's `time_ms` when the time is between 365 days before and 70 minutes after the receive time. The receive time is the moment when Navixy receives the packet. Navixy uses the receive time as the packet's time in the following cases:

* The packet has no `time_ms`, or `time_ms` is 0 or less.
* `time_ms` is more than 365 days before the receive time.
* `time_ms` is more than 70 minutes after the receive time.

A time outside this period usually comes from a wrong device clock. Navixy saves the packet with the receive time, so a device with a broken clock doesn't lose its data.

Don't send packets that are more than 365 days old. Navixy saves them with the receive time, so the old values replace the device's current position and readings.

## Which fix time Navixy saves

The fix time is the time of the position fix: when the device calculated the position. A packet has it in `fix_time_ms`. In the following checks, the period is the one in [Which time Navixy uses](#which-time-navixy-uses): from 365 days before to 70 minutes after the receive time. The packet's time is the time that Navixy uses for the packet, `time_ms` or the receive time.

Navixy decides the saved fix time with the following checks, in this order. The first check that matches decides it:

1. The packet has no position: Navixy saves no fix time. See [Position fields](message-structure-and-attributes.md#position-fields).
2. The packet has no `fix_time_ms`, or the value is 0 or less: Navixy saves no fix time.
3. The packet has `FIX_TYPE_LAST_KNOWN`: Navixy saves `fix_time_ms` as you sent it, even outside the period. A last known position is older than its packet.
4. `time_ms` is above 0 and outside the period, or `fix_time_ms` is outside it: Navixy saves the packet's time.
5. In all other cases, Navixy saves `fix_time_ms` as you sent it. This includes a packet without `time_ms`.

## In which order Navixy saves packets

Navixy saves each device's packets in time order. It doesn't save a packet whose time is earlier than the newest saved packet's time. Navixy compares only with packets from batches that it received before this batch. The following rules apply:

* Navixy compares a packet only with saved packets. A packet that Navixy didn't save for another reason doesn't count as the newest packet.
* Navixy saves a packet with the same time as the newest packet.
* Inside one batch, the order of the packets doesn't matter. Navixy compares each packet with the newest packet from batches that it received before, not with the other packets of its own batch.
* Navixy first replaces a time outside the period in [Which time Navixy uses](#which-time-navixy-uses) with the receive time, then compares the times.
* A packet with a future time, up to 70 minutes ahead, becomes the newest packet. Until the current time reaches this future time, every packet with a correct time is earlier than the newest packet, so Navixy doesn't save it. This rule applies to the device's packets on all connections.

For example, a sender's clock is 2 minutes fast. At 10:00:00 UTC, the sender sends a packet with the time 10:02:00. Then you correct the sender's clock. At 10:00:30 and 10:01:30, the sender sends packets with the correct times. Navixy doesn't save these 2 packets, because their times are earlier than 10:02:00, and it never saves them later. Navixy saves new packets again when their times are 10:02:00 or later.

To avoid packets that Navixy doesn't save, follow these rules:

* Keep the sender's clock correct, for example, with [NTP](https://datatracker.ietf.org/doc/html/rfc5905), the Network Time Protocol. Don't send future times.
* When a device keeps packets while it's offline, send these kept packets before any new packet. If Navixy saves a new packet first, the kept packets are older than the newest packet, and Navixy doesn't save them. Setting `buffered` to `true` doesn't change this rule.
* Send each device's batches in time order. A batch must have no packet that is older than the newest packet in the batches that you sent before it.
* After each start, send packets without `time_ms` until the device's clock is set, for example, from GNSS or NTP. Then check that the clock is correct, and switch to `time_ms`. Don't send packets without `time_ms` again until the next start. Right after the switch, Navixy may not save one packet if the device's clock is behind Navixy's clock.

For example, you send a packet without `time_ms` at 10:00:00.300 UTC, so Navixy saves it with this receive time. Right after, you send a packet in the next batch with `time_ms` 10:00:00.100 from your clock, which is 300 ms slow. The second packet is older than the first packet, so Navixy doesn't save it.

## How to check that Navixy saved data

To check that Navixy saved data for a device, use one of the following ways:

* **Last activity**: In Location Intelligence, go to **Device management** and click the device in the **Devices** list. **Last activity** shows the newest saved packet's time. If the **Devices** list was open while you sent data, reload the page: the list doesn't update **Last activity** by itself.
* **Data Stream Analyzer**: In an IoT Logic flow with the device, Data Stream Analyzer shows the saved packets' values. [See NGP data](../see-ngp-data.md) shows how to set up this flow.

**Last activity** shows the time that Navixy used for the newest saved packet. After a packet with a future time, **Last activity** shows this future time. Until the current time reaches it, Navixy doesn't save packets with a correct time: see [In which order Navixy saves packets](#in-which-order-navixy-saves-packets). Correct the sender's clock. For a test device, you can activate a new device instead of waiting.

To test a sender, send a packet with the current time and an attribute whose value changes on every test. For example, send `test_marker` with the current Unix time in seconds as its `int_value`. If Data Stream Analyzer shows this exact value, Navixy saved the packet.

## See also

* [Message structure and attributes](message-structure-and-attributes.md): Learn the packet fields and the rules for attribute names.
* [Transport layer](transport-layer.md): Learn how Navixy replies to a frame and when it closes a connection.
* [Data types and encoding standards](data-types-and-encoding-standards.md): Learn how NGP encodes times, numbers, and text.
