# Understanding Gray Zones in Fuel Level Reports

### Overview

Customers occasionally report gray zones or discontinuous lines in Fuel Level reports, assuming that the platform is not processing sensor data correctly. This behavior can create confusion because it may appear that sensor readings are missing or ignored.

In reality, the platform continues processing the sensor data correctly. The gray zones are a consequence of the platform's data continuity logic, which is designed to prevent inaccurate interpolation between long reporting gaps.

This document explains how the platform processes sensor data, why discontinuous intervals appear, and what can be done to minimize them.

<img src="../../.gitbook/assets/unknown (15).png" alt="" height="301" width="624">

<br>

### Valid Sensor Data vs. Invalid GPS Data

The first concept to understand is that GPS validity and sensor validity are independent.

A location is considered invalid when:

* No GPS satellites are available.
* Fewer than three satellites are available.
* Latitude or longitude equals 0.

Although the location itself is considered invalid, sensor values contained in the same packet remain valid and are always processed.

For example:

<img src="../../.gitbook/assets/unknown (16).png" alt="" height="235" width="624">

Although the GPS position cannot be displayed on the map due to lack of satellites, the value of avl\_io\_9 (Fuel Level Sensor) is still:

* Received
* Stored
* Processed
* Displayed in Fuel Reports
* Used by IoT Logic

In other words, invalid GPS data does not invalidate sensor data.



### Continuity of Sensor Data

One of the most common questions regarding Fuel Level reports is why the graph displays discontinuous segments, even though the individual sensor readings are present in the report.

From the customer's perspective, this behavior may appear incorrect because the sensor values are visible, yet the graph is not rendered as a single continuous line. In many cases, customers interpret these interruptions as missing data or incorrect processing, when in reality they are the result of the platform's continuity logic.

#### Continuity Logic

The platform groups sensor readings into continuous intervals based on the time difference between consecutive records.

When the elapsed time between two consecutive sensor readings exceeds 30 minutes, the platform considers that the previous interval has ended and a new interval begins. Consequently, the graph is divided into independent segments instead of being rendered as one uninterrupted line.

This behavior is intentional and applies regardless of whether the sensor values before and after the communication gap appear consistent. The continuity of the graph is determined by the reporting interval rather than by the similarity of the measured values.

As a result, individual sensor readings continue to be stored, processed, and included in reports, but they are not visually connected when they belong to different continuity intervals.

<img src="../../.gitbook/assets/unknown (17).png" alt="" height="223" width="624">



#### Why Doesn't the Platform Connect the Segments?

The primary objective of the reporting engine is to represent only information that has actually been reported by the device.

If the platform automatically connected two points separated by a long communication gap, it would implicitly assume what occurred during the period in which no data was received. Since the platform has no information describing the sensor behavior during that interval, any line connecting those points would represent an estimation rather than actual recorded data.

During a communication gap, numerous situations may have occurred.&#x20;

* The sensor value may have remained completely stable throughout the reporting gap.

<img src="../../.gitbook/assets/unknown (18).png" alt="" height="244" width="561">

* The sensor value may have increased gradually over time.

<img src="../../.gitbook/assets/unknown (19).png" alt="" height="243" width="557">

* The sensor value may have decreased progressively during the interval.

<img src="../../.gitbook/assets/unknown (20).png" alt="" height="250" width="571">

* A sudden event, such as a refueling or fuel theft, may have occurred but was never reported due to the absence of data.

<img src="../../.gitbook/assets/unknown (21).png" alt="" height="248" width="572">

* The device itself may have been offline or temporarily stopped transmitting data during that period.

This design guarantees that every line displayed in the report represents a sequence of real measurements instead of assumptions. Although this approach may produce visual interruptions in the graph, it preserves the integrity of the data and prevents users from drawing conclusions based on information that was never reported by the device.



### Reducing Communication Gaps

In most situations, discontinuous intervals originate from the device configuration rather than from the reporting engine itself.

The first aspect that should be reviewed is the sensor reporting strategy. Many tracking devices are configured to transmit sensor information only when a change is detected instead of sending periodic updates. While this behavior reduces network traffic and bandwidth consumption, it naturally produces long periods without sensor readings whenever the measured value remains unchanged.

Another important factor is the device reporting frequency. Increasing the reporting interval frequency generally results in more continuous datasets and therefore smoother report visualization.

Power management should also be considered. Depending on the device configuration, the tracker may enter Sleep, Deep Sleep, or another power-saving mode when the ignition is turned off. In these states, the device may continue communicating with the server through heartbeat packets while temporarily suspending sensor transmissions. Although the server still recognizes the device as connected, no new sensor values are received, which eventually creates new continuity intervals.

For this reason, the most effective way to reduce gray zones is to review the tracker configuration, including:

* Sensor reporting frequency.
* Sensor transmission priority.
* Event-based versus periodic reporting.
* Sleep and Deep Sleep configuration.
* Device power source and ignition behavior.

Optimizing these parameters allows the platform to receive sensor readings more frequently, resulting in longer continuous intervals and a more complete graphical representation, while preserving the accuracy and integrity of the recorded data.

<br>
