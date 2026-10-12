---
title: Anti-jammer devices
description: No GPS device is protected from jamming. Navixy can't detect jamming itself, but some devices detect it and report it as a reading
tags:
  - fundamentals
noIndex: true
---

# Anti-jammer devices

## Question

Is there an anti-jammer device?

## Answer

No GPS device can be protected from jammers.

Navixy shows the position that the device calculates and has no jamming detection of its own. Some devices detect jamming themselves and report it. For example, a Teltonika FMB device sends it as the reading `gps_jamming`, which appears with the other readings of the device in [Tracking](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/tracking). A device without this feature gives no reliable sign of jamming. Which models detect jamming is in the documentation of the device manufacturer.

In Tracking, a position from fewer than three satellites counts as weak. It still appears as the current position on the map, but it doesn't change the motion status of the asset.
