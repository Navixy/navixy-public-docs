---
description: Status indicators in Navixy show device activation and connection health. Color codes diagnose online, offline, and transitional device states.
---

# Device connection status

Each device registered on the Navixy platform has a status indicating its current connection state to the server. These statuses are color-coded to enable users to quickly determine the device's current state. The statuses can be broadly categorized into two types: activated or not activated.

<table><thead><tr><th width="190.3636474609375">Icon</th><th>Description</th></tr></thead><tbody><tr><td><strong>Activated devices</strong></td><td></td></tr><tr><td><img src="../../../.gitbook/assets/NVIDIA_Share_H9tS2oUyWm.png" alt="NVIDIA_Share_H9tS2oUyWm.png"></td><td>Connected visible, GPS coordinates updated.<br><br>The GPS device is connected to the server and has transmitted the current coordinates and time. Everything is ok, no action is required.</td></tr><tr><td><img src="../../../.gitbook/assets/NVIDIA_Share_zSZ8MaXwwC.png" alt="NVIDIA_Share_zSZ8MaXwwC.png"></td><td>Connected invisible, but GPS coordinates are outdated. The device is connected to the server, but has not transmitted the current coordinates for more than 5 minutes.<br><br>This can happen for a number of reasons:<br><br>- 5 minutes have passed after the last GPS coordinates update.<br>- Data is being received from the device's memory, so the packet timestamps differ from the current time. For example, it lost GSM signal for some time and then reconnected and started sending all saved GPS points from its memory to the platform. Once the device provides all data from its memory, it will start sending data with current timestamps and back to green again.<br>- The device is not sending GPS coordinates in data packets. This can happen due to protocol specifics, loss of satellite connection (under a roof, in a tunnel, in an underground parking, etc.), due to GPS signal jamming, or due to firmware failure.<br>- The device is in power saving mode or sleep mode. For example, the device is parked and sends the last known coordinates with the time they were received, or it sends heartbeat packets to maintain connection with the server.<br>- The device transmits data with a time zone that differs from UTC+0. If the device has a time zone setting, it should be set to UTC+0.</td></tr><tr><td><img src="../../../.gitbook/assets/NVIDIA_Share_ypRqHVOXgH.png" alt="NVIDIA_Share_ypRqHVOXgH.png"></td><td>Not connected. The device has not sent any data to the platform for the user-specified time in the connection status settings. By default, it is 10 minutes after the last received package.<br><br>The device may stop sending data for the following reasons:<br><br>- Moved to an area with weak GPRS signal.<br>- Entered deep sleep mode. This can be disabled in the device configurator or using SMS commands.<br>- Located in a roaming area, but this service is not enabled on its SIM card.<br>- The GPRS traffic limit or balance on its SIM card has been exhausted. Check this in the personal account of your mobile operator.<br>- If the device has broken connection from the platform on its own.</td></tr><tr><td><img src="../../../.gitbook/assets/NVIDIA_Share_LJrBv3tcjC.png" alt="NVIDIA_Share_LJrBv3tcjC.png"></td><td>The device is blocked due to the tariff plan. During the blocking period, no data from the device is accepted by the platform. Even after unblocking, the data for this period will not become available. Top up your balance in your personal account or contact the support team to unblock.<br><br>The device will be automatically unblocked within 15 minutes after the necessary amount for extending the services under the tariff plan has been paid.</td></tr><tr><td><strong>Non-activated devices</strong></td><td></td></tr><tr><td><img src="../../../.gitbook/assets/NVIDIA_Share_NrhgYBgICl.png" alt="NVIDIA_Share_NrhgYBgICl.png"></td><td>The device has not sent data to the platform a long time after registration or replacement. It is mostly the welcome screen's status. It counts all devices with registered, not activated and from blocked status.</td></tr><tr><td><img src="../../../.gitbook/assets/NVIDIA_Share_1774UbRWaj.png" alt="NVIDIA_Share_1774UbRWaj.png"></td><td>The light green status with clocks indicates that the device has just been<br>registered and is in the process of activation. It is normal to see this<br>status for newly registered devices and it may take a time for<br>the activation process to complete. This status will always be displayed for unactivated <a href="../../mobile-apps/x-gps-tracker-mobile-app.md">X-GPS Trackers</a> as well.</td></tr><tr><td><img src="../../../.gitbook/assets/NVIDIA_Share_oQyQigEF02.png" alt="NVIDIA_Share_oQyQigEF02.png"></td><td>The light orange status indicates that a significant amount of time has<br>passed since the device was registered, but it has not been activated<br>yet. In this case, it is recommended to see the document that<br>describes common causes of <a href="troubleshoot-device-activation.md">activation problems</a>. This can help identify and resolve any issues preventing the device from being activated.</td></tr><tr><td><img src="../../../.gitbook/assets/NVIDIA_Share_LttM5LqtOu.png" alt="NVIDIA_Share_LttM5LqtOu.png"></td><td>The light gray status with clocks indicates that the device does not support automatic activation,<br>and therefore, it must be manually configured using the configurator<br>that came with the device. If you do not have the configurator, please<br>contact the manufacturer's support or visit their official website. You<br>can find the server address and port <a href="https://www.navixy.com/devices/">on our website</a> or in the widget.</td></tr></tbody></table>

## Status logic

The sequence of connection statuses is as follows:

![Status logic - from offline to visible online](../../../.gitbook/assets/image-20240306-113015.png)

* The transition from offline to online occurs after the platform receives a packet from the device.
  * When the device sends a packet containing accurate coordinates and time, it is considered to be in an Online Visible status, indicated by a green light (bottom line on the scheme).
  * If the device transmits a packet containing incorrect coordinates or time, it remains online but is marked with an Invisible status, indicated by a green & white light (upper line on the scheme).
* The transition from online visible status to offline, if the last packet had valid GPS and time and no more packets were received during the connection timeout, follows this logic:
  * After obtaining a visible online status, if 5 minutes pass without further updates, the device's status changes to invisible online. This accounts for the uncertainty of the device's current location (represented by a line between the green and green & white statuses on the scheme).
  * After the time frame specified by the user in the "Connection Status" widget minus 5 minutes, the device's status changes from invisible online to offline (bottom line from the green & white status).

![Status logic - from offline to invisible online and back](../../../.gitbook/assets/image-20240306-113325.png)

* The transition from online status, if the last packet lacked valid GPS and/or time, to offline follows this logic:
  * After receiving a packet without valid GPS and/or time, the device remains online but in the invisible status.
  * The device will become offline if no further packets are received within the time frame specified by the user in the "Connection Status" widget.

When a vehicle goes into a tunnel with a GSM connection, your device will have sent messages with valid GPS coordinates and time before entering the tunnel and becoming online and visible. Inside the tunnel, the device will continue sending messages **without** valid GPS coordinates because it cannot connect to satellites.\
Additionally, if the device also loses its GSM connection, it won't be able to send data to the platform. If connection timeout is set (e.g., 60 minutes) and 60 minutes have passed since the last device packet, the device will be marked offline. Once the vehicle exits the tunnel and reconnects to GSM and satellites, it will send a new packet with valid coordinates and again become online and visible.

![Status logic - real example with tunnel](../../../.gitbook/assets/image-20240306-113712.png)

## How to customize connection timeout on devices

Configuring connection timeout settings for devices is a straightforward process, designed for ease of use. Follow these steps to adjust the connection timeout according to your preferences:

1. Navigate to the user interface's **Devices and settings** section.
2. Select the specific device for which you want to customize the timeout settings.

![How to customize connection timeout on devices - device's blocks](../../../.gitbook/assets/image-20240222-091026.png)

3. In the **Connection status** block, input your desired timeout interval. This can range from a minute up to 3000 days, depending on your operational requirements.

![How to customize connection timeout on devices - choosing from different values and setting the time](../../../.gitbook/assets/image-20240222-091107.png)

4. The platform will notify you about unsaved settings. Save the changes. The updated timeout settings will take effect immediately.

Furthermore, if you wish to apply the same settings across other devices of the user, you can easily achieve this by copying the settings, mirroring the approach used with other blocks.

![How to customize connection timeout on devices - copy settings button](../../../.gitbook/assets/image-20240222-091147.png)

For those who prefer to set default timeout settings, a convenient one-click option is available with the "Default" button.
