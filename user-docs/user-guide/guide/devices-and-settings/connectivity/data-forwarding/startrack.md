---
description: Send vehicle location and status data to Startrack's SOAP endpoint over HTTPS, used by fleet operators in Guatemala, El Salvador, and Honduras.
---

# Startrack

The Startrack protocol allows forwarding GPS and telemetry data from Navixy to external systems in real time. It is commonly used in Central America, especially in Guatemala, El Salvador, and Honduras, for fleet management, industrial operations, construction, and temperature-controlled transport.

### What is Startrack?

Startrack is a lightweight retranslation protocol that transmits location, speed, time, and vehicle status data to backend platforms. Its purpose is to enable continuous integration between Navixy and third-party monitoring or management systems.

### General technical information

Startrack uses SOAP to send data in XML format over HTTPS to the configured endpoint.

The following data is sent:

* Vehicle ID
* Latitude
* Longitude
* Heading
* Speed
* Date
* Ignition
* LocationValid

### Configuration

To set up data forwarding to Startrack, use the following parameters:

| Parameter | Value                                       |
| --------- | ------------------------------------------- |
| Domain    | `https://cempro.gps.gt/gpsDataService?wsdl` |
| Port      | `443`                                       |
| Protocol  | SOAP / XML over HTTPS                       |

After creating the retranslator in Navixy, assign it to the corresponding device and ensure the external ID matches the identifier expected by Startrack.

### Troubleshooting

If data is not received on the Startrack side, verify the following:

* The retranslator is enabled
* The endpoint URL and port are correct
* The vehicle ID matches the destination system
* Startrack confirms incoming data

To test connectivity, you can run a `curl` request using a prepared SOAP XML file. A successful response should include `ok>true` and a message indicating the data was added to the processing queue.

**Example:**

\<soap:Envelope xmlns:soap="[http://schemas.xmlsoap.org/soap/envelope/](http://schemas.xmlsoap.org/soap/envelope/)">\<soap:Body>\<ns2:receiveWithConfirmationResponse xmlns:ns2="[https://cempro.gps.gt](https://cempro.gps.gt/)">\<return>\<ok>true\</ok>\<result>u66> isOk:true statuses:\[ADDED\_TO\_RECORD\_Q] messages:\[added to recorder queue] \</result>\</return>\</ns2:receiveWithConfirmationResponse>\</soap:Body>\</soap:Envelope>%

<br>
