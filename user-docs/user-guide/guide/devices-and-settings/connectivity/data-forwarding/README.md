---
description: Set up GPS and telematics data forwarding from Navixy to third-party servers for compliance, enterprise integration, or data consolidation needs.
---

# Data forwarding

The **Data forwarding** block in Navixy enables you to transmit GPS tracking and telematics data from Navixy to other servers and third-party applications using API and web services.

## What is data forwarding?

Data forwarding (or data retransmission) is a feature that allows you to send GPS tracking and telematics data from Navixy to other servers or third-party applications in real-time. The types of data that can be forwarded include:

* Vehicle data
* GPS location
* IoT device data

Data forwarding can be done either offline or nearly instantly as data is transmitted.

### Main purposes of data forwarding

1. **Government regulation compliance:** In some countries, authorities require vehicles to transmit data (such as location and speed) using specific protocols to comply with local laws.
2. **Enterprise integrations:** Large businesses, particularly in sectors like retail or logistics, often require their suppliers to send GPS and telematics data to their systems to meet contractual obligations, such as ensuring timely deliveries or maintaining specific conditions (like temperature) for cargo.
3. **Data consolidation:** Integration of various components within complex software systems often requires data normalization, especially when these components are provided by multiple independent vendors.

### How data forwarding works

Collected vehicle data is sent in a specified protocol format to an address and port determined by the user. Navixy offers various data forwarding protocols that can be selected based on your specific needs within the user interface.

## Managing data forwarding

You can manage data forwarding through the corresponding block in the **Devices and settings** section.

In this block, you can:

* Link one or more retranslators to a device.
* Specify the ID used when sending data (by default, the same ID as the device itself is used).
* Unlink retranslators from the device.
* Create new and edit existing retranslators by clicking the **Protocols** button.

### Retranslators management

When managing a retranslator, you can configure the following parameters:

* **Name:** A convenient label for easy identification.
* **Data transfer protocol:** Choose from the supported protocols.
* **Destination server address and port:** Specify where the data should be sent.
* **Login and password:** For authorization on the receiving server (if required).
* **Retranslator activity:** Enable or disable the retranslator as needed.

You can create multiple retranslators if your subscription plan allows it. Retranslator profiles can be edited, deleted, or suspended.

### Protocols

Here are some examples of protocols available for various purposes:

#### Government regulation compliance protocols

| Protocol                       | Description                                                                                                                   |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| **Maquinaria Amarilla** (🇨🇴) | SOAP-based protocol mandatory for Colombia, reporting to police servers.                                                      |
| **Olympstroy** (🇷🇺)          | SOAP-based protocol used during the preparation for the 2014 Olympic Games.                                                   |
| **OSINERGMIN** (🇵🇪)          | Protocol for sending telematic information to the Peruvian government for monitoring units in industries like mining and gas. |
| **RNIS** (🇷🇺)                | Used in Moscow's regional navigation and information system for vehicle movement control.                                     |

#### Enterprise compliance

| Protocol                                         | Description                                                                                                |
| ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------- |
| **Altotrack Chep Mexico**                        | Forwards vehicle positions from Chep to the Altotrack HUB.                                    |
| **ArmCargo**                                     | Sends data to Asset Risk Management services for risk assessment.                                          |
| **Cargo online**                                 | Forwards data to the CargoOnline service.                                                                  |
| [**ILSP**](ilsp.md)                              | Enables ILSP’s software to share vehicle data across networks in Mexico.                                   |
| **Localizar-t**                                  | Forwards data to Localizar-t's logistics project, Forza.                                                   |
| **ReC Solutions**                                | Sends data to ReC Servicios Consultores servers.                                                           |
| [**Recurso Confiable**](recurso-confiable.md)    | Used for data forwarding with Recurso Confiable’s software across various industries in Mexico and beyond. |
| **SafetyNet pulsian**                            | Forwards SOS alarms to a SafetyNet CAD server for emergency assistance.                                    |
| [**SimpliRoute**](simpliroute.md)                | Protocol for transmitting vehicle tracking data to SimpliRoute.                                            |
| **TraceReports**                                 | Sends data to the TraceReports system.                                                                     |
| [**Unigis**](unigis.md) | Enables data sharing with Unigis’ TMS platform, often used by logistics departments.                       |
| **Wirtrack**                                     | Forwards data to Wirsolut services.                                                                        |

#### Data consolidation

| Protocol                               | Description                                                                  |
| -------------------------------------- | ---------------------------------------------------------------------------- |
| **AVL Control**                        | Sends data to AVL Control servers for security management and statistics.    |
| **Granit3**                            | Forwards data to Santel navigation servers.                                  |
| **Startrack**                          | SOAP-based protocol for sending data to Startrack's logistics system.        |
| **Lacak.io**                           | Data forwarding protocol for Lacak.io.                                       |
| [**Navixy Web Service**](navixy-ws.md) | SOAP-based protocol for transmitting fleet data to third-party systems.      |
| **SA-RM**                              | General data forwarding protocol.                                            |
| **Transnavigation**                    | Forwards data to Transnavigation servers.                                    |
| [**Wialon IPS**](wialon-ips.md)        | Public protocol from Gurtam for retransmitting GPS and GLONASS device data. |
| **Wisetrack**                          | Forwards data to Wisetrack servers.                                          |

