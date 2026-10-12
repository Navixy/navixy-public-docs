---
title: Slight difference between GPS CAN mileage and physical odometer
description: Vehicle control modules report mileage asynchronously; CAN data is more precise than dashboard odometer readings. Small differences are normal and expected
---

# Slight difference between GPS CAN mileage and physical odometer

## Question

Why is there a slight difference between the GPS CAN mileage value and the physical odometer?**

## Answer

A common reason is that modern vehicles store and report mileage from multiple control modules (such as the engine control module, body control module, or instrument cluster), which are not always perfectly synchronized in real time. The dashboard odometer typically displays a rounded value, while CAN data may provide more precise readings, including decimals. Additionally, CAN signals are transmitted at intervals, meaning the reported value may reflect a slightly earlier measurement. As a result, small discrepancies—like a difference of around 17 km over 185,000 km—are normal and fall well within expected tolerance. This doesn't indicate any issue with the vehicle or the data, but rather standard behavior in how vehicle systems report mileage.

## Links

* [Can bus GPS Tracker](https://www.navixy.com/blog/can-bus-gps-trackers/)
