---
description: How the MQTT protocol works. Covers publish and subscribe through a broker, topics, packet structure, QoS levels, and uses in telematics.
tags:
  - fundamentals
---

# MQTT fundamentals

MQTT is a lightweight messaging protocol for devices that send small messages over slow or unreliable networks. This page explains how the protocol moves messages, what its delivery levels guarantee, and where it fits in vehicle telematics.

## Origin and standards

Andy Stanford-Clark of IBM and Arlen Nipper of Arcom Control Systems wrote the first version of MQTT in 1999. They needed to monitor an oil pipeline over an expensive satellite link that often lost the connection. For this reason, the protocol keeps messages small and recovers from dropped connections.

OASIS, a standards organization, publishes the two versions in use:

- **MQTT 3.1.1**: An OASIS standard since October 29, 2014, also published as ISO/IEC 20922:2016.
- **MQTT 5.0**: An OASIS standard since March 7, 2019. It adds reason codes, message expiry, user properties, and flow control.

MQTT runs over TCP/IP, or over any network protocol that delivers bytes in order, without loss, in both directions. IANA reserves TCP port 1883 for MQTT and port 8883 for MQTT over TLS. A separate specification, MQTT for Sensor Networks (MQTT-SN), applies the same model to networks without TCP, such as ZigBee.

## Publish and subscribe through a broker

MQTT clients never send messages to each other directly. Every message goes through a server called the broker. A client is any program that connects to the broker, such as the firmware of a GPS device or a server application.

A client publishes a message to a topic, which is a text label that names what the message is about. Other clients subscribe to the topics that they want. The broker sends each message to every client that subscribes to its topic.

The publisher doesn't know who receives the message, and subscribers don't know who sent it. As a result, one device can send data to many applications, and a client can join or leave without changes to the other clients.

## Topics and wildcards

A topic is a UTF-8 string that a forward slash divides into levels, for example `fleet/truck-17/position`. Topics need no registration.

A subscription can use two wildcards:

- **`+`**: Matches exactly one level. `fleet/+/position` matches the position topic of every vehicle.
- **`#`**: Matches any number of levels and must be the last character. `fleet/truck-17/#` matches every topic of that vehicle.

Clients can't publish to a topic that contains a wildcard. Topics that start with `$` are reserved for the broker's own use. The specification leaves the rules for who may publish or subscribe to each topic to the broker product.

## Packet structure

Every MQTT packet starts with a fixed header of 2 to 5 bytes. Some packet types add a variable header and a payload.

- **Fixed header**: The first byte gives the packet type, such as CONNECT or PUBLISH, and 4 bits of flags. In a PUBLISH packet, the flags set the QoS level, the retain option, and a mark for a repeated delivery. The next 1 to 4 bytes give the number of bytes that follow.
- **Variable header**: Many packet types include a 2-byte packet identifier, which links an acknowledgment to the packet that it confirms. A PUBLISH packet also has the topic name here.
- **Payload**: In a CONNECT packet, the payload has the client identifier and the optional user name and password. In a PUBLISH packet, the payload is the message itself.

The smallest packets, such as the keep-alive ping, are 2 bytes long. The specification doesn't define the message format, so the receiver must know in advance whether a device sends binary data, JSON, or text.

## Quality of service levels

Quality of service (QoS) sets the delivery guarantee for a message. The sender chooses one of three levels for each message. A higher level uses more packets and gives a stronger guarantee.

| Level | Guarantee | Packets | Result |
| ----- | --------- | ------- | ------ |
| QoS 0 | At most once | PUBLISH | The message can be lost. Nothing is acknowledged or resent. |
| QoS 1 | At least once | PUBLISH, PUBACK | The sender resends until it receives PUBACK, so duplicates can arrive. |
| QoS 2 | Exactly once | PUBLISH, PUBREC, PUBREL, PUBCOMP | The four-step exchange prevents both loss and duplicates. |

QoS applies to each connection separately: from the publisher to the broker, and from the broker to each subscriber. The broker delivers at the lower of two levels: the level that the publisher used, and the maximum level granted for the subscription.

## Sessions, retained messages, and last will

Four features help clients whose connections drop often:

- **Keep alive**: The client sets an interval when it connects and sends a ping when it has nothing else to send. If the broker receives nothing for 1.5 times the interval, it closes the connection.
- **Persistent session**: The broker can keep a client's subscriptions after a disconnect and queue QoS 1 and QoS 2 messages until the client reconnects with the same client identifier.
- **Retained message**: The broker stores the last retained message for each topic and sends it to every new subscriber, who gets the last known value at once.
- **Last will**: When a client connects, it can give the broker a Will message. The broker publishes the Will message if the connection ends without a normal disconnect, for example after a network failure.

## Strengths and limits

MQTT suits devices that send small, frequent messages over mobile networks. Its strengths:

- **Low overhead**: Small headers keep traffic low on metered mobile data plans.
- **Independent clients**: A new application can read device data with no change to the devices.
- **Two-way traffic**: The device opens the connection, and the broker sends commands back over it. The server never needs to open a connection to the device, which mobile networks often block.
- **Offline detection**: The Will message tells other clients when a device disconnects unexpectedly.

Its limits:

- **No encryption of its own**: Without TLS, MQTT sends data, user names, and passwords as plain text.
- **Central broker**: A broker failure stops all traffic. The specification doesn't define broker redundancy.
- **No message schema**: The sender and the receiver must agree on the payload format outside the protocol.
- **Open connection**: Keep-alive pings on a permanent connection add traffic and use battery power.
- **No media streaming**: MQTT moves separate messages, not continuous video or audio.

## MQTT in vehicle telematics

Telematics systems use MQTT in these ways:

- **Periodic telemetry**: GPS devices publish position, speed, and sensor readings to a topic for each vehicle. QoS 0 often suits these readings, because the next reading replaces a lost one.
- **Alarms**: Events that must arrive, such as a panic button press, use QoS 1. The receiving application ignores duplicates.
- **Records that must not repeat**: QoS 2 suits messages where a duplicate gives a wrong result, such as a billing record.
- **Commands and configuration**: A server publishes to a topic that the device subscribes to. A retained configuration message reaches the device as soon as it subscribes again after a reconnect.
- **Connection status**: The Will message reports a device that went offline, so applications don't need to poll each device.
- **Data for other systems**: Back-end services subscribe to the same topics and send the data to dashboards or databases.
