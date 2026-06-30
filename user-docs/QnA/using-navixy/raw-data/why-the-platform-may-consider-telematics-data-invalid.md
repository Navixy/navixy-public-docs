---
title: Why the platform may consider telematics data invalid
description: "Platform filters data with zero satellites or invalid coordinates (0,0) to prevent inaccurate location display and track quality issues"
---

# Why the platform may consider telematics data invalid

## Question

Why can the platform ignore or mark certain telematics data as invalid?

## Answer

There are two key factors that can cause the platform to ignore or mark telematics data as invalid when received from a device:

1. **Zero satellites (0 satellites)**\
   When the device reports zero satellites, the platform automatically treats this as an invalid reading. As a result, even though the device may still be transmitting data, those points will not appear on the platform. This prevents inaccurate location data from being displayed.
2. **Invalid coordinates (0,0)**\
   If the coordinates received are (0,0), this indicates an invalid geographic point. Displaying such data could lead to a misleading interpretation of the route, so the platform filters these points out by default.
