---
description: Adjust online, GPS-not-updated, and offline timeout thresholds for device status indicators in Navixy on-premise. Edit the tcp-server configuration file.
---

# Device statuses

Each device in the Navixy platform has a specific status that indicates its current condition. These statuses change based on the duration of time that elapses.

<table data-header-hidden><thead><tr><th width="236.727294921875" align="center"></th><th width="119.9090576171875"></th><th></th></tr></thead><tbody><tr><td align="center"><img src="../../../.gitbook/assets/browser_lrla4GLmVP.png" alt="browser_lrla4GLmVP.png" data-size="original"></td><td>N/A</td><td>Data is constantly being received from a device and contains valid coordinates</td></tr><tr><td align="center"><img src="../../../.gitbook/assets/browser_qJjb9CyZAT.png" alt="browser_qJjb9CyZAT.png"></td><td>5 minutes</td><td>No valid location data for more than 5 minutes or the device is transmitting data in a time zone other than UTC+0</td></tr><tr><td align="center"><img src="../../../.gitbook/assets/browser_CHEecmH3nB.png" alt="browser_CHEecmH3nB.png"></td><td>10 minutes</td><td>The device does not send any data packets for over set time period</td></tr></tbody></table>

### Adjusting Timeout Settings

If needed, timeouts in On-Premise instances can be adjusted by modifying the "tcp-server" configuration file. The configuration file is located at the following path

* on Linux: `/home/java/tcp-server/conf/config.properties`
* on Windows: `C:\java\tcp-server\conf\config.properties`

To adjust the timeouts, open the "tcp-server" configuration file for editing and add the following lines:

```
timers.idleTimeout=10m
timers.offlineTimeout=15m
```

In the above example, the timeout value for "<mark style="color:purple;">GPS not updated</mark>" status is set to 10 minutes, while the timeout value for "<mark style="color:orange;">Offline</mark>" status is set to 15 minutes. You can specify any values you need, but make sure to set the first timeout value to be less than the second one. Also, note that it is mandatory to add the letter "m" after the specified timeout value, which means minutes.

After specifying the timeout values, save the configuration file and restart the "tcp-server" to apply the changes.

With these adjustments, the timeout settings can be customized to suit users' needs and ensure optimal device performance on the Navixy platform.
