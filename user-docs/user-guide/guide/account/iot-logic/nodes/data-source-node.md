---
description: Data Source node is the IoT Logic entry point for device telemetry. It receives data via TCP, UDP, HTTP, or MQTT, decodes it, and passes it to downstream nodes.
---

# Data Source

## Technical overview and capabilities

{% columns %}
{% column %}
**Data Source** node is an entry point for telemetry data from IoT devices and OEM platforms in the IoT Logic system. It functions as a universal translator, receiving data via TCP/UDP/HTTP protocols on network interfaces and through MQTT queues, then decoding incoming data streams according to the selected protocol. The node transforms device messages into a standardized format that can be further processed in your flow.
{% endcolumn %}

{% column %}
<figure><img src="../../../../.gitbook/assets/image-20250403-162909.png" alt="Data source node in the flow workspace"><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

### Flow architecture integration

<figure><img src="../../../../.gitbook/assets/Data-source-in-flow.webp" alt="Data source node included in a flow on workspace"><figcaption></figcaption></figure>

**Data Source node** functions as the entry point for data in an IoT Logic flow. A single flow can contain multiple source nodes, each with independent configurations. This architecture enables:

* Initial data acquisition from multiple device types and protocol formats
* Standardized data transformation from various manufacturers into unified formats
* Parallel processing paths by connecting one data source to multiple downstream nodes
* Selective device filtering to include only relevant data sources in your flow

### Node capabilities

The **Data Source node** by itself offers:

* **Protocol diversity**: Supports multiple device manufacturers, including Teltonika, Queclink, Suntech, Jimi, and others, through inherited Navixy parsers and decoders
* **Transport flexibility**: Accommodates TCP, UDP, HTTP protocols and MQTT broker connections
* **Unified data transformation**: Converts device-specific messages to standardized format for consistent processing
* **Device filtering**: Provides filtering capabilities to select specific models or protocols
* **Real-time processing**: Handles incoming telemetry data streams in real-time for immediate processing

## Configuration options

{% columns %}
{% column width="58.333333333333336%" valign="middle" %}
**Data source node** requires configuration to establish which devices will send data to your flow. You'll need to specify the protocol, transport protocol, and select the specific devices you want to include.
{% endcolumn %}

{% column width="41.666666666666664%" %}
<figure><img src="../../../../.gitbook/assets/Data_source_node_edit.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

Let's see what elements this node uses and what you can configure when working with it:

### Configuration steps

{% stepper %}
{% step %}
#### Specify Node name

Enter a descriptive name for this data source:

* Use a name that helps you identify the manufacturer, models, or other relevant information.
* This name will be displayed in the flow diagram for easy identification.
{% endstep %}

{% step %}
#### Select Sources

From the filtered list, select the devices to include. Only devices registered in your Navixy user account are available for selection.
{% endstep %}

{% step %}
#### Save the node configuration

Click **Apply changes** to complete the node creation.
{% endstep %}
{% endstepper %}

{% hint style="info" %}
If you change the manufacturer or model settings after selecting devices, Navixy notifies you if any selected devices don't match the new parameters but doesn't automatically remove them from your selection.
{% endhint %}

### Data processing specifics

**Data source node** inherits all parsers and decoders from Navixy, providing compatibility with a wide range of IoT devices. When data arrives at this node, it goes through the following process:

1. The incoming data stream is received through the specified transport protocol
2. The data is passed to the appropriate protocol decoder based on your configuration
3. Device messages are transformed into a standardized format that IoT Logic can process
4. The unified data is passed to the next node in your flow

This standardization process enables you to build consistent processing flows regardless of the original data format from various device manufacturers.

## Frequently asked questions

### Can I use multiple Data source nodes in one flow?

Yes, you can use several **Data source nodes** in one workspace. This is useful when you need to process data from different types of devices in different ways or want to merge several data streams after specific transformations.

### What happens if a device is already used in another flow?

A device can belong to multiple flows at the same time. If you add a device that is already used in another flow, both flows process its data simultaneously and results are merged to avoid data loss. Being assigned to another flow isn't a restriction.

### Are all my Navixy devices automatically available in IoT Logic?

Yes, all devices from your Navixy user account can be used in IoT Logic processing. This includes GPS devices, OEM platforms, MQTT devices and gateways, and MQTT/Kafka connectors.

### How do I know which manufacturer to select for my devices?

The protocol should match the communication protocol used by your device manufacturer. Most devices use a protocol associated with their manufacturer (e.g., Teltonika devices use the Teltonika protocol). Check your device documentation or consult with your device provider if you're unsure.

### Can I connect a Data Source node to multiple downstream nodes?

Yes, you can connect a **Data Source node** to multiple processing nodes to create parallel processing paths. This allows you to apply different transformations to the same data stream. Here’s an example:

<figure><img src="../../../../.gitbook/assets/image-20250404-075539.png" alt="Example showing the Data source node in context with multiple outbound connections and outputs"><figcaption></figcaption></figure>
