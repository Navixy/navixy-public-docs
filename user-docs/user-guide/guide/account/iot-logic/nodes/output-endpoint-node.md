---
description: Output Endpoint node transmits processed device data from IoT Logic flows to external systems using the Navixy Generic Protocol for standardized data delivery.
---

# Output Endpoint

## Technical overview and capabilities

{% columns %}
{% column %}
**Output Endpoint node** serves as the data transmission component within IoT Logic flows, defining where processed device data is sent. Its primary function is to standardize heterogeneous device data into a consistent format before transmitting it to external systems or services. All data is transferred in a unified format, enabled by [Navixy Generic Protocol](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-generic-protocol).
{% endcolumn %}

{% column %}
![](../../../../.gitbook/assets/image-20250407-210332.png)
{% endcolumn %}
{% endcolumns %}

For details on the format in which data is transmitted, see [Output data format](output-endpoint-node.md#output-data-format).

### Flow architecture integration

<figure><img src="../../../../.gitbook/assets/Output-endpoint-in-flow.webp" alt="Output Endpoint node in the flow workspace"><figcaption></figcaption></figure>

A single IoT Logic flow can contain multiple output nodes, each with independent configurations. This architecture enables:

* Multi-destination data transmitting to different external systems simultaneously
* Multiple data sources handling with different incoming data formats
* Selective data routing that enables flexible data flow scenarios

{% hint style="info" %}
Each flow should include a **Default Output Endpoint** node to send data to the Navixy platform. Maintain connections between your **Data Source** nodes and this output. The connection ensures device data is sent to the platform, enabling monitoring capabilities using Navixy tools. If the Navixy output is removed, data from the devices involved in the flow will no longer reach the platform.
{% endhint %}

### Node capabilities

The **Output Endpoint** node by itself offers:

* **Secure transmission**: Implements SSL encryption and authentication mechanisms for data protection during transit
* **Configurable delivery assurance**: Provides MQTT QoS level selection to balance between delivery guarantees and network overhead
* **Configuration reusability**: Supports creating endpoint profiles that can be reused across multiple flows, ensuring configuration consistency
* **Concurrent processing**: Accepts inputs from multiple data sources within a flow, allowing consolidated data transmission
* **Transport protocol version selection**: Supports both MQTT 3.1.1 and 5.0 to accommodate various broker implementations

## Configuration options

{% columns %}
{% column valign="middle" %}
Setting up an **Output endpoint node** determines how and where data will be delivered from a particular flow. Each configuration option serves a specific purpose in establishing reliable data transmission.
{% endcolumn %}

{% column %}
<figure><img src="../../../../.gitbook/assets/Output_Endpoint_node_edit.png" alt="" width="236"><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

Let's see what elements this node uses and what you can configure when working with it:

### Configuration steps

{% stepper %}
{% step %}
#### Specify endpoint Name

Enter a unique, descriptive name for this endpoint configuration

* Use a name that helps you identify the destination the data is sent to
* This name will be displayed in the flow diagram for easy identification
{% endstep %}

{% step %}
#### Select endpoint Mode

Choose what type of transmitting to use for this endpoint

* **Default endpoint**: standard configuration for sending flow data to the Navixy platform that can't be edited
* **MQTT endpoint**: custom configuration that uses MQTT as transport for sending flow data to 3rd-party systems. For te specific configuration parameters of this mode, see [MQTT](output-endpoint-node.md#mqtt).
{% endstep %}

{% step %}
#### Save your configuration

Click **Apply changes** to finalize the node creation.
{% endstep %}
{% endstepper %}

{% hint style="info" %}
Make sure to connect the relevant data nodes to your new output. Otherwise, it won’t receive any data.
{% endhint %}

### Mode-specific configurations

<details>

<summary>MQTT</summary>

If you plan to use an MQTT output, you need to configure these parameters:

1. **Endpoint settings**
   1. Select **MQTT Version**: 3.1.1 or 5.0.
   2. Enter the target **IP** in the format: _123.123.123.123_ or _example.example.com_.
   3. Specify the **Port** number. By default, _1883_ is used for standard MQTT.
   4. Specify **Topic** in the form of tags that will be used for data transmission.
   5. Choose **QoS** level that determines the logic of data transmission:
      1. **QoS 0**: no delivery confirmation.
      2. **QoS 1**: guaranteed delivery with possible duplication.
      3. **QoS 2**: guaranteed delivery without duplication.
   6. Enter **MQTT Client ID**. The receiving side has a fixed list of clients. In this field, the correct value must be specified so that the data is not rejected.
2. **MQTT authentication** (optional)
   1. Toggle **Use Authentication** on.
   2. Enter **MQTT username** and **MQTT password** for the receiving party in the appeared fields.
3. **SSL** (optional)
   1. Toggle-on **Use SSL** for secure connections. This action automatically sets the port to &#x31;_&#x38;83_ if it wasn’t changed manually.

</details>

## Output data format

The node's primary capability is data format standardization through [Navixy Generic Protocol](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-generic-protocol). This standardization solves a fundamental challenge in IoT implementations, the diversity of device-specific protocols that require custom integration work.

Usually, each device type uses its own data format, requiring dedicated protocol handlers on receiving systems. This approach increases development and maintenance overhead exponentially as device fleets expand. IoT Logic addresses this by normalizing all device data into a standardized JSON structure, regardless of the original manufacturer or protocol.

The [Navixy Generic Protocol](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-generic-protocol) specification includes standardized fields for device identification, location data, telemetry values, and metadata. This format supports bidirectional communication, allowing both data transmission from IoT Logic to external systems and data ingestion from external sources into the platform.

By implementing a single protocol, the **Output Endpoint** node enables:

* A single integration pattern for receiving systems instead of multiple device-specific handlers
* Consistent data structure that remains uniform across all connected devices
* Reduced server-side processing overhead through protocol normalization
* Simplified scaling when adding new device types to existing deployments
* Bidirectional data exchange capabilities with external systems

The node implements MQTT as the transport protocol for this standardized JSON payload, providing a reliable, lightweight transmission mechanism suitable for IoT deployments.

## Frequently asked questions

### Can I connect multiple data sources to a single Output Endpoint node?

Yes. The **Output Endpoint** node accepts inputs from multiple **Data Source** nodes simultaneously. All processed data, including location coordinates, device identifiers, telemetry parameters, and calculated attributes, is serialized according to the [Navixy Generic Protocol](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-generic-protocol) specification before transmission.

<figure><img src="../../../../.gitbook/assets/image-20250404-105858.png" alt="Example flow showing multiple Data Sources connected to a single Output Endpoint"><figcaption></figcaption></figure>

### What happens if I modify an endpoint that's used in multiple flows?

Endpoint configurations are stored as reusable resources across the entire client account. Modifications to an existing endpoint configuration affect all nodes referencing that endpoint across all flows. This behavior facilitates configuration standardization but requires careful change management when updating endpoint parameters.

### What security practices are recommended for production deployments?

For implementations requiring high security standards (healthcare, financial, etc.), enable SSL and implement MQTT authentication. While this increases protocol overhead slightly, it provides necessary data protection during transmission. Standard implementations should use at minimum QoS level 1 to ensure delivery confirmation.
