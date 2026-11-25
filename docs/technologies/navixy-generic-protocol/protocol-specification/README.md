---
description: >-
  The NGP 4.0 specification: the transport, the message definition, the
  encoding rules, and the rules by which Navixy saves each packet.
tags:
  - overview
---

# Protocol specification

The Navixy Generic Protocol (NGP) 4.0 specification gives the rules that a sender follows to send data that Navixy saves. It's for developers who write a sender's firmware or server code. To send a first packet before you read the rules, start with [Getting started](../getting-started.md). The pages use the terms defined in [Key concepts](../overview.md#key-concepts), such as packet, batch, and acknowledgment.

## What the specification covers

The pages follow the order in which data moves from a sender to Navixy:

1. [Transport layer](transport-layer.md): How a sender connects to Navixy and exchanges frames. Read it to write the network code: the hosts, the frame format, the acknowledgments, and the connection limits.
2. [Message structure and attributes](message-structure-and-attributes.md): What a batch and a packet contain. Read it to fill the packets: the message definition, every packet field, the attribute names, and the event IDs.
3. [Data types and encoding standards](data-types-and-encoding-standards.md): How NGP encodes values. Read it when you write your own protobuf encoder, or when a value doesn't arrive as you expect.
4. [How Navixy saves packets](how-navixy-saves-packets.md): What Navixy does with each packet after it receives a batch. Read it when Navixy acknowledges a batch, but a packet or a value doesn't appear.

## See also

* [Protocol overview](../overview.md): Learn the key concepts and how a sender works with Navixy.
* [See NGP data](../see-ngp-data.md): See the positions and readings that Navixy saved for a device.
* [Migration from NGP 1.2](../migration-from-ngp-1-2.md): Convert NGP 1.2 JSON messages into NGP 4.0 packets.
