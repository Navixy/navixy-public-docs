---
description: Display interval-mode device data as map tracks by generating tracks and disabling the Split by stops option in the Navixy tracking settings.
---

# View tracks recorded in interval tracking mode

There are a large number of GPS devices that work in interval mode. They send points once in a certain period of time.

To save the device battery and traffic, this period can be large (some devices can transmit data once a day, for example, asset trackers for cargo transportation).\
However, such data does not allow for building accurate tracks on the map, and if the data transmission interval will exceed the idle time of the "parking" state, tracks will not be displayed at all and the device will always have the **Parked** status.

If you need to show the movement of the device as a line on the map in any case, then generate a track

![Displaying tracks in interval mode](../../.gitbook/assets/image-20231130-084047.png)

And disable the **Split by stops** option in the **Tracks** settings.

![Displaying tracks in interval mode](../../.gitbook/assets/image-20231130-084219.png)

In this case, all points transmitted by the device during the specified time period are shown as one trip.
