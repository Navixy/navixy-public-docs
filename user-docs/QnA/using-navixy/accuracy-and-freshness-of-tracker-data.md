---
title: Accuracy and freshness of tracker data
description: Tracker accuracy depends on GPS conditions; platform filters unreliable data with too few satellites or interference from jammers
---

# Accuracy and freshness of tracker data

## Question

Are the data provided by GPS trackers (location, events) accurate and up-to-date?

## Answer

The accuracy and freshness of tracker data depend on both the device itself and the conditions under which it transmits data.

For example, if a tracker loses GSM connection, it won't be able to send data to the platform in real time. In this case, most devices store data in their internal memory (exact behavior depends on the model). Once the connection is restored, the tracker uploads this historical data in chronological order. These uploads are clearly marked. If the data is more than 10 minutes old, the platform shows that while the data was received recently, it is no longer current. The device status in this case will indicate "GPS not updated."

Regarding accuracy: if the tracker is indoors with a weak satellite signal or is affected by a GPS jammer, it may start reporting incorrect or imprecise coordinates. The tracker calculates its own position, and the platform displays this information as provided. In cases where the tracker can't reliably connect to satellites or is under a strong GPS jammer, it may report very few satellites (for example, fewer than two), and the platform automatically filters out these unreliable data points.

Some advanced trackers can detect signal jamming and send a specific event to the platform to indicate this. However, if the device does not support this feature, there is no reliable way for the platform to identify jamming on its own.

## Links

* [Connection status](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/tracking/objects-list/connection-state)
