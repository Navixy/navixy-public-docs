---
description: Set up door, trunk, and hood sensors for vehicles that transmit this data via the CAN bus. The platform logs open status events as they arrive from the device.
---

# Doors, trunk, and hood sensor

Track the open/closed status of doors, trunk, and hood for vehicles that transmit this data over the CAN bus.

## Availability

Available on devices and vehicles that report door, trunk, and hood status via the CAN bus.

## Setup

No special configuration is required. However, several factors need to be considered.

First, not all car models transmit this information via the CAN bus. Unfortunately, you can only learn it by testing or requesting detailed documentation from the manufacturer.

Second, due to some peculiarities of the device's data transfer protocol, you aren't able to track the status of doors, etc., until Navixy receives the **Open** status at least once. For correct operation, leave the trunk, hood, and all doors open for several minutes with the engine running.
