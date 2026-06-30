---
title: Difference between fuel level and fuel level 2.0 reports
description: "Fuel Level 2.0 uses machine learning to smooth noisy sensor data but cannot track drains; use Fuel Level for drain detection"
---

# Difference between fuel level and fuel level 2.0 reports

## Question

Can you share what is the difference between fuel level report and fuel level 2.0? Which one is recommended to use?

## Answer

A standard fuel report does not use complex algorithms to modify the data it receives. It displays information as close as possible to how it was received by the platform. If you generate a report without smoothing or filtering out peak values, it will show the raw data exactly as it arrived. The report then compares sensor readings and its settings to detect refueling and fuel drains.

The problem is that many fuel sensors are inaccurate and can send raw data that is difficult to use for a proper fuel report. The report will still generate, but its quality will match the quality of the sensor data itself. You’ve probably seen such reports where the fuel level graph looks like mountains or sawtooth patterns—this is sensor noise.

This issue is common for analog fuel level sensors, CAN bus sensors, and OBD-based sensors, as they typically read raw fuel data without processing it before sending it to the tracker and platform.

However, some sensors process the data on their own before transmitting it, providing much cleaner and smoother fuel level readings. This makes it possible to generate accurate graphs and correctly calculate fuel consumption and refueling with usual fuel report.

Since many clients do not use high-quality sensors (either due to installation complexity or cost), they end up with inconsistent fuel reports. The only way to fix this is to teach the platform how to interpret the data from each specific sensor.

To solve this, Navixy developed Fuel Report 2.0. It uses advanced averaging algorithms and machine learning to turn poor-quality data into a more realistic fuel level representation. This helps eliminate sensor noise and sharp spikes while providing smoother fuel level graphs. However, the trade-off is that the new report cannot track fuel drains. Since smoothing and machine learning algorithms apply to all data, sudden drops in fuel level are also averaged out.

Fuel Report 2.0 is based on the fact that most clients focus on tracking fuel consumption and refueling, rather than detecting fuel theft through drains. Additionally, there are numerous ways drivers can steal fuel without triggering a traditional drain detection. Instead, the report helps track how much fuel was refueled compared to receipts, monitor consistent fuel consumption levels, and detect fuel losses through less obvious methods.

The new fuel report should be used in these cases:

* Your fuel sensor data is inaccurate, fluctuates constantly, and produces a sawtooth-like fuel level graph.
* You need to track actual fuel consumption and refueling, compare how much fuel was refilled versus receipt records, and check whether the vehicle's fuel usage stays within normal limits to detect possible theft through more subtle methods than direct draining.

## Links

* [Fuel management](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/vehicle-telematics-technology/fuel-management)
* [Fuel control in Navixy](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/vehicle-telematics-technology/fuel-management/fuel-control-in-navixy)
* [Fuel volume report](https://app.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/s/446mKak1zDrGv70ahuYZ/guide/reports/specific-report-details/fuel-volume-report)
* [Fuel volume 2.0 report](https://app.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/s/446mKak1zDrGv70ahuYZ/guide/reports/specific-report-details/fuel-volume-2.0-report)
