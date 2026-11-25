---
description: >-
  Send a first NGP 4.0 packet to Navixy from a Python script, read the
  acknowledgment, and check that Navixy saved the data.
tags:
  - guide
---

# Getting started

In this guide, you send one Navixy Generic Protocol (NGP) packet to Navixy from a Python script. Then you check that Navixy saved it. The guide is for developers who build an NGP sender and want a working connection before they write their own code. At the end, your script sends a packet with a position and 3 attributes. Location Intelligence then shows that Navixy saved the packet. The guide uses the terms defined in [Key concepts](overview.md#key-concepts), such as packet, batch, and acknowledgment.

## Prerequisites

Before you start, make sure that you have the following:

* An NGP device for tests: a device activated in Location Intelligence with the vendor **Navixy** and the model **NGP device**. [Activate a device](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/device-management/activate-device) describes the activation. Note the device identifier, because the script uses it. The following rules apply to this device:
  * Activate a new device. Don't use a device whose older data you still need to send. After Navixy saves a packet with the current time, it doesn't save packets with an earlier time that it receives afterward. See [In which order Navixy saves packets](protocol-specification/how-navixy-saves-packets.md#in-which-order-navixy-saves-packets).
  * When you activate the device, choose an identifier that other people can't guess. Don't use the identifier from the examples on these pages. See [Device identifiers instead of credentials](protocol-specification/transport-layer.md#device-identifiers-instead-of-credentials).
* Access to Location Intelligence, the Navixy web app where you manage devices. To open it, sign in at [account.navixy.com](https://account.navixy.com/?utm_source=navixy-docs&utm_campaign=ngp-getting-started).
* Your Navixy account's region, EU or US. In Navixy Console, the [organization details](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/organization#organization-details) show it in **Region**.
* Python 3.9 or later. The commands use `python3`. On Windows, use `py` instead.
* A network that allows outgoing TCP connections to port `30006`.

## Send a packet

To send your first packet, follow these steps:

{% stepper %}
{% step %}
### Install the protobuf tools

Install the protobuf runtime and the code generator:

```bash
python3 -m pip install protobuf "grpcio-tools>=1.66"
```

`grpcio-tools` has a protobuf compiler, so you don't need to install `protoc` separately. Use version 1.66 or later. Earlier versions can't read the editions syntax of `navixy3.proto`, the newer syntax for protobuf files.
{% endstep %}

{% step %}
### Generate the message classes

To generate the classes, follow these steps:

1. Save the [message definition](protocol-specification/message-structure-and-attributes.md#message-definition) as `navixy3.proto` in an empty folder.
2. In this folder, run the following command:

   ```bash
   python3 -m grpc_tools.protoc -I. --python_out=. navixy3.proto
   ```

The command creates `navixy3_pb2.py`, which has a Python class for each NGP message.
{% endstep %}

{% step %}
### Write the script

Save the following script as `send_ngp4.py` in the same folder:

{% code title="send_ngp4.py" %}
```python
import socket
import struct
import time

import navixy3_pb2 as ngp

HOST = "gateway.iot.eu.navixy.com"
PORT = 30006
DEVICE_ID = "fleet7-3f9c2a81"


def read_exactly(sock, size):
    data = b""
    while len(data) < size:
        chunk = sock.recv(size - len(data))
        if not chunk:
            raise ConnectionError("Navixy closed the connection")
        data += chunk
    return data


packet = ngp.Navixy3Packet(
    device_id=DEVICE_ID,
    time_ms=int(time.time() * 1000),
    latitude=34.159296,
    longitude=-118.461413,
    speed=43.0,
    heading=77.0,
    altitude=95.0,
    satellites=8,
    fix_type=ngp.FIX_TYPE_3D,
    position_source=ngp.POSITION_SOURCE_GNSS,
    attributes=[
        ngp.Attribute(name="battery_level", int_value=68),
        ngp.Attribute(name="board_voltage", double_value=13.9),
        ngp.Attribute(name="ignition", bool_value=True),
    ],
)
# A real sender uses a new sequence value for each new batch.
batch = ngp.Navixy3Batch(sequence=1, packets=[packet])
payload = batch.SerializeToString()

with socket.create_connection((HOST, PORT), timeout=10) as sock:
    sock.settimeout(200)  # Navixy closes the connection if it can't write the batch to its storage within 3 minutes.
    sock.sendall(struct.pack(">I", len(payload)) + payload)
    (length,) = struct.unpack(">I", read_exactly(sock, 4))
    ack = ngp.Navixy3Ack.FromString(read_exactly(sock, length))
    print("Acknowledged batch", ack.sequence)
```
{% endcode %}

In the script, change these values:

1. Set `DEVICE_ID` to your NGP device's identifier.
2. If your account is in the US region, set `HOST` to `gateway.iot.us.navixy.com`.

The script builds one packet, puts it in a batch, and sends the batch as one frame. A frame is a 4-byte length, then the protobuf bytes. `struct.pack(">I", ...)` writes the length in big-endian byte order, with the most significant byte first. Then the script reads Navixy's reply, an acknowledgment frame in the same format. [Frame format](protocol-specification/transport-layer.md#frame-format) describes frames in detail.

The script sets the following packet fields and batch fields:

* `time_ms`: The current time, because the packet describes the moment when you run the script.
* `altitude` and `fix_type`: `FIX_TYPE_3D` means a position with altitude, so the packet also has `altitude`.
* `position_source`: `POSITION_SOURCE_GNSS` means that the position comes from satellites.
* `attributes`: 3 attributes with standard names. Each attribute uses the value field that the [Standard attribute names](protocol-specification/message-structure-and-attributes.md#standard-attribute-names) table gives: `int_value`, `double_value`, and `bool_value`.
* `sequence`: The number that the acknowledgment returns. This value is 1 for the first batch.

The `read_exactly` function reads the exact number of bytes that it needs, because TCP can split a frame into several parts.
{% endstep %}

{% step %}
### Run the script

Run the script:

```bash
python3 send_ngp4.py
```

Navixy returns the acknowledgment, and the script prints the following line:

```
Acknowledged batch 1
```
{% endstep %}

{% step %}
### Check the device

To check the device, follow these steps:

1. If the **Devices** list in Location Intelligence is already open, reload the page.
2. In Location Intelligence, go to **Device management**.
3. In the **Devices** list, click the NGP device.

**Last activity** shows the newest saved packet's time.

The script sends the current time. The device page shows how long ago that time was, such as a few seconds. The **Devices** list shows the time of day.

Check **Last activity** after every successful run. The acknowledgment doesn't show whether your packet passed all checks. For example, Navixy also acknowledges a batch for an unknown device identifier.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
Your script sent an NGP packet with a position and 3 attributes. Navixy acknowledged the batch, and the NGP device shows the new activity. Your own sender follows the same steps: build packets, send a batch in a frame, and read the acknowledgment.
{% endhint %}

## Error handling

The following table lists the errors that can occur in this guide, with each error's cause and fix:

| What happens | Cause | What to do |
| --- | --- | --- |
| `error: externally-managed-environment` when you install the tools | Your Python doesn't allow packages outside a virtual environment. | Create and activate a virtual environment: run `python3 -m venv .venv`, then `source .venv/bin/activate`. On Windows, run `.venv\Scripts\activate`. Then install the tools again. |
| `ModuleNotFoundError: No module named 'navixy3_pb2'` | The script runs in a folder without the generated `navixy3_pb2.py`. | Run the script in the folder where you generated the code. |
| `socket.gaierror`, `ConnectionRefusedError`, or a timeout (`socket.timeout` or `TimeoutError`) before the script sends data | The host name is wrong, or your network blocks port `30006`. | Check `HOST` against [Regional hosts](protocol-specification/transport-layer.md#regional-hosts). Check that your network allows outgoing TCP connections to port `30006`. |
| A timeout (`socket.timeout` or `TimeoutError`) while the script waits for the acknowledgment | No reply within 200 seconds: Navixy couldn't decode the batch. The unchanged script doesn't cause this error. | If you changed the script, check that every text value is valid UTF-8. Check that the length matches the protobuf bytes. |
| `ConnectionError: Navixy closed the connection` | Navixy couldn't write the batch to its storage within 3 minutes, or the network connection failed. See [Connection lifetime](protocol-specification/transport-layer.md#connection-lifetime). | Run the script again. If the error repeats, see [How to handle a missing acknowledgment](protocol-specification/transport-layer.md#how-to-handle-a-missing-acknowledgment). |
| The script prints the acknowledgment, but **Last activity** doesn't change after you reload the page | Navixy didn't save the packet. For example, you ran the script before you activated the device. Navixy then treats the identifier as unknown for up to 45 seconds after the activation. | Wait a minute, then run the script again. Compare `DEVICE_ID` with the device identifier, character by character. Check that `HOST` is the host for your account's region. Check that your computer's clock is correct, because `time_ms` comes from it. See [Which packets Navixy doesn't save](protocol-specification/how-navixy-saves-packets.md#which-packets-navixy-doesnt-save). |

## Next steps

* [See NGP data](see-ngp-data.md): See the positions and readings that your packet had.
* [Transport layer](protocol-specification/transport-layer.md): Keep a connection open and send a batch again after a connection fails.
* [Message structure and attributes](protocol-specification/message-structure-and-attributes.md): Add the packet fields and attributes that your device reports.
* [How Navixy saves packets](protocol-specification/how-navixy-saves-packets.md): Learn why Navixy can acknowledge a batch but not save a packet.
