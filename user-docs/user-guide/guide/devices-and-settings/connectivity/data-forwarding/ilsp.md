---
description: Push vehicle positioning and alarm events from Navixy to ILSP shared security monitoring centers in Mexico, enabling multi-company data sharing via HTTP.
---

# ILSP

## ILSP: a protocol for private security vehicle data between servers

**ILSP** offers private security services in Mexico. The ILSP data forwarding protocol enables their software to share vehicle data across networks.

_Protocol Category: Enterprise compliance_

### Table of contents

1. [What is ILSP?](ilsp.md#what-is-ilsp)
2. [Technical information about ILSP](ilsp.md#tech-info-ilsp)
3. [ILSP Configuration](ilsp.md#ilsp-config)
4. [Setting up](ilsp.md#setting-up)
5. [Managing](ilsp.md#managing)
6. [Troubleshooting](ilsp.md#troubleshooting)

### What is ILSP?

The ILSP data forwarding protocol is for companies that have common monitoring centers dedicated to security issues and need to monitor vehicle positioning and need to share this data with a third party.

Using ILSP, partners can update their information with Navixy data such as vehicle data, positioning, etc. Users can connect with multiple companies and send information to the ILSP, streamlining fleet and security data.

### ILSP general technical information

The ILSP protocol uses the POST method to send JSON data over HTTP to the ILSP servers for further data processing.

Data sent to ILSP:

* Customer ID
* Transport Line ID
* License Plate
* Tracker Event and time
  * SOS, power lost, and GSM damp alarm
* Latitude
* Longitude
* Speed
* Heading
* Odometer
* Battery

### ILSP configuration

#### Setting up

Required Parameters

* Login and Password
  * ILSP Login and Password
* Destination server address and port
  * Address: `https://www.ilspservices.com.mx/`
  * Port: 443

To set up data forwarding in ILSP protocol:

1. Open the device settings from the main menu by clicking the gear icon on the bottom left of the screen.
2. Click the **Data forwarding** block.
3. Click **Retranslators management**.
4. This opens a pop-up where you input the required parameters by clicking the **+** button.
5. For the ILSP protocol, input the following information:

<table><thead><tr><th width="288.18182373046875">Parameter</th><th>Explanation</th></tr></thead><tbody><tr><td>Name</td><td>Enter a name to make this retranslator easily identifiable</td></tr><tr><td>Protocol</td><td>Select the ILSP protocol from the dropdown</td></tr><tr><td>Destination server address and port</td><td>* Address: `https://www.ilspservices.com.mx/`<br>* Port: 443</td></tr></tbody></table>

6. The **Retranslation management** screen should look similar to the following, with ILSP login and password. Make sure the **Enabled** button is checked and click the **Save** button to complete the process.

![](https://www.navixy.com/wp-content/uploads/2022/10/image-8-600x111.png)

7. Next, the retranslator will need to be linked to the device on the ILSP side. To do so, select the  <img src="https://www.navixy.com/wp-content/uploads/2022/08/image-3.png" alt="link" data-size="line">button in the **Data forwarding** block. Select the retranslator to be connected and click **Link** below.
8. Add information needed to identify the device on ILSP in the **External ID** field, either by clicking the pencil icon or the external ID field itself. This value should include the following from the ILSP side:

```
UserId|VehicleLicensePlate|RouteID
```

#### Managing

To edit or stop data from being forwarded, follow these steps:

1. To stop the data forwarding, click the **Trash** button.
2. Acknowledge the change via the pop-up.
3. Click **Retranslators management** to change retranslator settings such as name, login information, or enabled status.
4. This will open the **Retranslator management** window. Select the row to edit and either click the pencil in the top left or double-click the row in question to allow editing. Save any changes.

![ILSP](https://www.navixy.com/wp-content/uploads/2022/10/image-9-600x100.png)

#### Troubleshooting

If data doesn't display on the 3rd-party ILSP system, check if:

* Username and password for ILSP are correctly entered
* URL was entered correctly
* Retranslator is enabled
* External ID information is correct
