---
description: Retransmit GPS and GLONASS fleet data to Wialon-based monitoring servers or a separate Navixy instance using the Gurtam TCP retranslation protocol.
---

# Wialon IPS

Wialon IPS is a generic, publicly available data forwarding protocol from Gurtam for retranslating personal and vehicle GPS and GLONASS devices that transfer data to satellite monitoring servers or third-party resources.

_Protocol Category: Data consolidation_

### Table of contents

1. [What is Wialon IPS?](wialon-ips.md#what-is-wialon-ips)
2. [Wialon IPS general technical information](wialon-ips.md#wialon-ips-general-technical-information)
3. [Wialon IPS configuration](wialon-ips.md#wialon-ips-configuration)
4. [Setting up](wialon-ips.md#setting-up)
5. [Managing](wialon-ips.md#managing)
6. [Troubleshooting](wialon-ips.md#troubleshooting)

### What is Wialon IPS?

The Wialon IPS data forwarding protocol can be used to forward fleet or vehicle device data between two Navixy servers (e.g. if you have ServerMate and On-Premise versions).

This is ideal for partners with devices connected to Wialon who would like that data sent to Navixy. Data forwarded can include information regarding: vehicle positioning, fuel monitoring, sensors, temperature, etc.

### Wialon IPS general technical information

The Wialon IPS protocol uses the TCP transport layer to send ASCII data to the 3rd party server, receive 3rd party data, or send to a separate Navixy server for further data processing.

Data sent to Wialon IPS:

* Date and time
* Lat
* Long
* Altitude
* Speed
* Satellites
* Inputs
* Outputs
* Analog sensors
* Mileage
* Battery level
* Driver ID
* Events

### Wialon IPS configuration

#### Setting up

To set up data forwarding for the Wialon IPS protocol:

1. Open the device settings from the main menu by clicking the **Gear** icon on the bottom left of the screen.
2. Click the **Data forwarding** block.
3. Click **Retranslators management**.
4. This opens a pop-up where you input the required parameters by clicking the **+** button.
5. For the Wialon IPS protocol, input the following information:

* Name
  * Enter a name to make this retranslator easily identifiable
* Protocol
  * Select the Wialon IPS protocol from the dropdown
* Destination server address
  * 3rd party server
  * Navixy A to Navixy B
    * On A, input the server address for B
  * If receiving from Wialon
    * EU domain: tracker.navixy.com
    * US domain: tracker.us.navixy.com
* Destination Port
  * Related information from the 3rd-party server
  * Navixy A to Navixy B, and from Wialon IPS
    * 47768

6. The Retranslation management screen should look like the following, no password needed. Make sure the **Enabled** button is checked and click the **Save** button to complete the process.

<figure><img src="https://www.navixy.com/wp-content/uploads/2022/10/wialon-ips.png" alt="Wialon IPS setup"><figcaption></figcaption></figure>

7. Next, the retranslator will need to be linked to the device on the Navixy side. To do so, select the **Link** ![link image](https://www.navixy.com/wp-content/uploads/2022/08/image-3.png) button in the **Data forwarding** block. Select the retranslator to be connected, and click **Link** below. External ID is not needed for the Wialon IPS protocol.
8. Select **Save** once completed.

{% hint style="warning" %}
If receiving data from Wialon IPS, a related Wialon IPS-compatible device will need to be created on the Navixy platform, such as Bitrek.
{% endhint %}

#### Managing

To edit or stop data from being forwarded, follow these steps:

1. Click the **Trash** button to stop the data forwarding.
2. Acknowledge the change in the pop-up.
3. Click **Protocols** to change retranslator settings such as name, login information, or enabled status
4. This will open the retranslator management window. Select the row to edit and either click the pencil in the top left or double-click the row in question to allow editing. Save any changes.

![](https://www.navixy.com/wp-content/uploads/2022/10/wialon-ips-management.png)

#### Troubleshooting

If data doesn't display on the 3rd-party Wialon IPS system or Navixy platform, verify:

* The URL was entered correctly with the associated port
* Retranslator is enabled
