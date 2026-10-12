---
title: Ways to get raw device data
description: Navixy has no raw data export tool. Query the stored messages in IoT Query Premium, read the latest values through the GraphQL API, or watch a flow in Data Stream Analyzer
tags:
  - reference
noIndex: true
---

# Ways to get raw device data

## Question

How can I get the raw data that my GPS devices send, for analytics, diagnostics, or my own reports?

## Answer

Navixy has no tool that exports the raw data of a device to a file. Depending on what you need, use one of the following:

* **Analytics and your own reports:** [IoT Query Premium](https://app.gitbook.com/s/75iUkTidqKcVcd2J49aB/data-access/data-schema/raw-data-layer/telemetry) gives SQL access to the stored messages, one row for each message. Dashboard Studio exports query results to CSV or Excel.
* **Device data in your own system:** the [Tracking API](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/products/tracking) of the Navixy GraphQL API returns the latest value of each reading of a device. It also returns the points of each trip with the readings that you choose. See [Reading a vehicle trip history](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/products/tracking/guides/reading-a-vehicle-trip-history).
* **What a device sends now:** [Data Stream Analyzer](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/monitoring) in IoT Logic shows the latest values that pass through a flow. It exports them to a JSON file, with the time when the device recorded each value and the time when Navixy received it.
* **Sensor values over a period:** the [Sensor readings](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/reports/sensor-readings) report shows how each value changed, in steps of 5 minutes or more.
