---
description: Transfer GPS tracking data from Navixy to the Recurso Confiable enterprise platform every 5 minutes via SOAP, used across Mexico, Colombia, and Central America.
---

# Recurso Confiable

## Recurso Confiable: GPS tracking/data forwarding for enterprises

**Recurso Confiable** is a security and logistics software that monitors, gives visibility, and generates predictability on transport operations. Navixy created the Recurso Confiable data forwarding protocol to be used by other companies that also work with Recurso Confiable across multiple industries in Mexico, Colombia, the United States, and Central America.

_Protocol Category: Enterprise protocol_

### Table of contents

1. [What is Recurso Confiable?](recurso-confiable.md#what-is-rc)
2. [Technical information about Recurso Confiable](recurso-confiable.md#tech-info-rc)
3. [Recurso Confiable Configuration](recurso-confiable.md#rc-config)
4. [Setting up](recurso-confiable.md#setting-up)
5. [Managing](recurso-confiable.md#managing)
6. [Troubleshooting](recurso-confiable.md#troubleshooting)

### What is Recurso Confiable?

With the Recurso Confiable protocol, enterprise partners can transfer GPS tracking data safely and reliably into a single location to optimize fleet management processes. This allows the client a way to work and communicate with other Recurso Confiable partners.

When developing this protocol, Navixy took into account the needs of large logistics and retail companies. Companies can streamline important fleet management and GPS tracking platform data and capabilities such as control systems, trip planning, tracking in real-time, predictive modeling reporting, geofence implementation and editing, route optimization, and more. Companies previously disconnected from one another on the Recurso Confiable network are now able to communicate efficiently and quickly.

### Recurso Confiable general technical information

The Recurso Confiable protocol uses SOAP to push XML data every 5 minutes over HTTP to Recurso Confiable.

Data that’s sent to Recurso Confiable:

* AVL Event Code
* License Plate
* Shipment ID
* Date
* Direction
* Latitude
* Longitude
* Altitude
* Speed
* Course
* Ignition
* Odometer
* CustomerID
* CustomerName
* Device ID

### Recurso Confiable configuration

#### Setting up

Required Parameters

* Recurso Confiable Login and Password
* External ID
* Destination server address
  * [http://gps.rcontrol.com.mx/Tracking/wcf/RCService.svc](http://gps.rcontrol.com.mx/Tracking/wcf/RCService.svc)
* Destination server port
  * 80

To set up data forwarding in the Recurso Confiable protocol:

1. Open the device settings from the main menu by clicking the gear icon on the bottom left of the screen.
2. Click the **Data forwarding** block.
3. Click **Protocols.**
4. This opens a pop-up where you input the required parameters by clicking the **+** button.
5. For the Recurso Confiable protocol, input the following information:

<table><thead><tr><th width="236.54541015625">Parameter</th><th>Explanation</th></tr></thead><tbody><tr><td>Name</td><td>Enter a name to make this retranslator easily identifiable</td></tr><tr><td>Protocol and login</td><td>Select the Recurso Confiable protocol from the dropdown<br>Use Recurso Confiable login and password</td></tr><tr><td>Destination server address and port</td><td>Address: http://gps.rcontrol.com.mx/Tracking/wcf/RCService.svc<br>Port: 80</td></tr></tbody></table>

6. The **Retranslation management** screen should look similar to the following, with Recurso Confiable login and password. Make sure the **Enabled** button is checked and click the **Save** button to complete the process.

![](https://www.navixy.com/wp-content/uploads/2022/10/pasted-image-0-1-1-600x120.png)

7. Next, the retranslator will need to be linked to the device on the Recurso Confiable side. To do so, select the **Link** <img src="https://www.navixy.com/wp-content/uploads/2022/08/image-3.png" alt="link image" data-size="line"> button in the **Data forwarding** block. Select the retranslator to be connected, and click **Link** below.
8. Next, add information needed to identify the device on Recurso Confiable in the External ID field either by clicking the pencil icon or the external ID field itself. This value should include the following from the Recurso Confiable side, where only the License Plate is mandatory:

* License Plate
* Route ID
* Company ID
* Company Name

The format for the External ID field will be separated by a pipe. For example:

```
ABC123|1|123|John
```

If only the License Plate is available, the External ID can be entered by itself:

```
ABC123
```

Or if any other information is missing, be sure to include the pipes, for example:

```
ABC123||123|
```

9. Select **Save** once completed.

#### Managing

To edit or stop data from being forwarded, follow these steps:

1. Select the **Pencil** icon or click in the associated box to edit the external ID used to point to the device on the 3rd party system.
2. Click the **Trash** button to stop the data forwarding.
3. Acknowledge the change in the pop-up.
4. Click **Protocols** to change retranslator settings such as name, login information, or enabled status
5. This will open the retranslator management window. Select the row to edit and either click the pencil in the top left or double-click the row in question to allow editing. Save any changes.

![](https://www.navixy.com/wp-content/uploads/2022/10/pasted-image-0-3-600x107.png)

#### Troubleshooting

If data doesn't display on the 3rd-party Recurso Confiable system, make sure that:

* Username and password for Recurso Confiable are correctly entered
* URL was entered correctly
* Retranslator is enabled
* External ID matches the related Recurso Confiable information
