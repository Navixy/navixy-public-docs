---
description: Sync vehicle positions from Navixy to SimpliRoute order management via HTTP JSON, linking live tracker data to delivery routes and orders in Latin America.
---

# SimpliRoute

## SimpliRoute

**SimpliRoute** is a data retransmission protocol that delivers vehicle tracking data for users to send information from their system to SimpliRoute.

_Protocol Category: Enterprise compliance_

### Table of contents

1. [What is SimpliRoute?](simpliroute.md#what-is-simpliroute)
2. [Technical information about SimpliRoute](simpliroute.md#tech-info-simpliroute)
3. [SimpliRoute Configuration](simpliroute.md#simpliroute-config)
4. [Setting up](simpliroute.md#setting-up)
5. [Managing](simpliroute.md#managing)
6. [Troubleshooting](simpliroute.md#troubleshooting)

### What is SimpliRoute?

SimpliRoute is a data retransmission protocol that delivers vehicle location data and links it to the SimpliRoute order system. This allows users the ability to track orders that are managed within this software and that are synchronized with the vehicle positions obtained.

This data retransmission protocol is ideal for users looking to comply with SimpliRoute data forwarding to track vehicle positioning.

### SimpliRoute general technical information

The SimpliRoute protocol uses the POST method to send JSON data over HTTP to the SimpliRoute servers for further data processing.

Data sent to SimpliRoute:

* Vehicle VIN
* License Plate
* Latitude
* Longitude
* Date/Time
* Speed
* Ignition
* GPS Provider - tax number specified in user information

### SimpliRoute configuration

#### Setting up

Required Parameters

* Vehicle with an associated license plate and VIN according to the standards [here](../../../fleet-management/).

To set up data forwarding for the SimpliRoute protocol:

1. Open the device settings from the main menu by clicking the gear icon on the bottom left of the screen.
2. Click the **Data forwarding** block.
3. Click **Protocols**.
4. This opens a pop-up where you input the required parameters by clicking the **+** button.
5. For the SimpliRoute protocol, input the following information:

<table><thead><tr><th width="243.09088134765625">Parameter</th><th>Explanation</th></tr></thead><tbody><tr><td>Name</td><td>Enter a name to make this retranslator easily identifiable</td></tr><tr><td>Protocol</td><td>Select the SimpliRoute protocol from the dropdown</td></tr><tr><td>Destination server address and port</td><td>* Address: https://k8k5azm77j.execute-api.sa-east-1.amazonaws.com/prod/gps<br>* Port: 443</td></tr></tbody></table>

6. The **Retranslation management** screen should look like the following, with SimpliRoute login and password. Make sure the **Enabled** button is checked and click the **Save** button to complete the process.

![](https://www.navixy.com/wp-content/uploads/2022/10/pasted-image-0-2-600x115.png)

7. Next, the retranslator will need to be linked to the device on the SimpliRoute side. To do so, select the ![image-20250310-140837.png](../../../../.gitbook/assets/image-20250310-140837.png) button in the **Data forwarding** block. Select the retranslator to be connected, and click **Link** below. External ID is not needed for the SimpliRoute protocol.
8. Select **Save** once completed.

#### Managing

To edit or stop data from being forwarded, follow these steps:

1. Click the **Trash** button to stop the data forwarding.
2. Acknowledge the change in the pop-up.
3. Click **Protocols** to change retranslator settings such as name, login information, or enabled status
4. This will open the retranslator management window. Select the row to edit and either click the pencil in the top left or double-click the row in question to allow editing. Save any changes.

![](https://www.navixy.com/wp-content/uploads/2022/10/pasted-image-0-1-600x116.png)

#### Troubleshooting

If data doesn't display on the 3rd-party SimpliRoute system, make sure that:

* URL was entered correctly
* Retranslator is enabled
* A GPS device on Navixy is associated with a vehicle on the Navixy platform with a valid license plate and VIN
