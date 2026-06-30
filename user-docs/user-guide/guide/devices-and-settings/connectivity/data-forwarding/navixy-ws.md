---
description: Forward GPS tracking data from Navixy to external systems using the Navixy Web Service SOAP endpoint, supporting time-based queries for historical telemetry.
---

# Navixy Web Service

{% hint style="info" %}
**Protocol Category:** Data consolidation protocol
{% endhint %}

### Table of contents

1. [What is Navixy Web Service?](navixy-ws.md#what-is-ws)
2. [Technical information about Navixy Web Service](navixy-ws.md#tech-info-ws)
3. [Navixy Web Service Configuration](navixy-ws.md#ws-config)
4. [Setting up](navixy-ws.md#setting-up)
5. [Managing](navixy-ws.md#managing)
6. [Troubleshooting](navixy-ws.md#troubleshooting)

### What is Navixy Web Service?

**Navixy Web Service** data forwarding protocol transmits fleet data from the Navixy system to any third-party system. The protocol is flexible, allowing third parties to store fleet data in their databases to use for any purposes or to display data on the web resources.

Since this data forwarding protocol is platform agnostic, it would be the ideal option for any partner that works with XML files.

### Navixy Web Service general technical information

The Navixy Web Service protocol uses SOAP over the OSI application layer to forward XML telemetry data to the connected system.

Data fields that are sent:

* `dateGPS`: Date and time in UTC
* `ignition`: Boolean ignition status
* `latitude`
* `longitude`
* `speedGPS`: km/h
* `unitPlate`: License plate
* `altitude`: Meters
* `course`: Vehicle direction, for example: **N,S,E,O,NO,NE,SO,SE**
* `deviceId`: IMEI
* `numSat`: Number of GNSS satellites the device is using
* `odometer`: Traveled distance in km
* `eventId`: the event code according to the [Navixy Generic Protocol's event ID](https://www.navixy.com/docs/iot-logic-api/technologies/navixy-generic-protocol/navixy-generic-protocol-10/predefined-event-identifiers)

### Navixy Web Service configuration

#### Setting up

To set up data forwarding in Navixy Web Service protocol:

1. Go to **Devices and settings** from the left sidebar.
2. Select the needed device from the **Objects** list.
3. Find the **Data forwarding** block, expand it and click <img src="../../../../.gitbook/assets/image (36).png" alt="" data-size="line">.
4. In the opened window, click "+" to add a new retranslation.
5. In the **New retranslation protocol** dialog, input the required information. For Navixy Web Service protocol, fill in the following fields:

<table><thead><tr><th width="187.8182373046875">Parameter</th><th>Explanation</th></tr></thead><tbody><tr><td>Name</td><td>A descriptive label to identify this retranslation protocol configuration. Enter a name to make this retranslator easily identifiable.</td></tr><tr><td>Protocol and Login</td><td>The communication protocol used for retranslation. Select <strong>Navixy Web Service</strong> from the dropdown menu.</td></tr><tr><td>Address</td><td>The URL or IP address of the destination server.<br><strong>Note</strong>: It is not used by Navixy Web Service, enter any valid address format.</td></tr><tr><td><strong>Port</strong></td><td>The network port for connecting to the destination server.<br><strong>Note</strong>: It is not used by Navixy Web Service, enter any port number.</td></tr><tr><td>Login</td><td>A unique identifier for this retranslator connection. Enter any login that isn't already used by another retranslator in your system.</td></tr><tr><td>Password</td><td>Authentication passcode for this retranslator connection. Enter a unique password to secure this retranslator configuration.</td></tr></tbody></table>

6. Toggle the **Enabled** switch on to activate data retranslation. The retranslator will not transmit any data while disabled.
7. The **Retranslation management** screen should look similar to the following, with the Navixy Web Service login and password. Make sure the status is **Active** if you want this retranslator to send data.\
   ![](<../../../../.gitbook/assets/image (37).png>)
8. Next, the retranslator will need to be linked to the device. To do so, enable the toggle with the needed retranslator name in the **Data forwarding** block. External ID is not needed for the Navixy Web Service protocol.\
   ![](<../../../../.gitbook/assets/image (38).png>)

{% hint style="success" %}
Now the retranslator is available for all devices in the account. Enable it by switching the toggle in other devices' settings.
{% endhint %}

#### External access:

Required parameters:

* **Login and password**: These should match the credentials you set up in the retranslator configuration
* **deviceIDs**: Max of 100
* **startDate** and **endDate**: For example, September 9, 2022 12am UTC to 11:59:59
  * UTC: 2022-09-01T00:00:00Z to 2022-09-01T11:59:59Z

The description of the protocol in WSDL can be found below, relating to where the server is located:

EU [https://soap.navixy.com/LocationDataService?wsdl](https://soap.navixy.com/LocationDataService?wsdl)

US [https://soap.us.navixy.com/LocationDataService?wsdl](https://soap.us.navixy.com/LocationDataService?wsdl)

A SOAP request must be made using one of the above WSDL pages. The XML request itself is as follows, replaced with the associated information:

{% code overflow="wrap" %}
```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:tem="http://tempuri.org">

   <soapenv:Header>
   <tem:authentication>
     <login>username</login>
     <password>password</password>
   </tem:authentication>
   </soapenv:Header>
   <soapenv:Body>
   <tem:dataRequest>
      <!--1 to 100 repetitions:-->
      <deviceIds>IMEI of device</deviceIds>
      <startDate>2022-08-30T00:00:00Z</startDate>
      <endDate>2022-08-31T00:00:00Z</endDate>
   </tem:dataRequest>
   </soapenv:Body>
</soapenv:Envelope>
```
{% endcode %}

An example response may look like this:

```xml
<S:Envelope xmlns:S="http://schemas.xmlsoap.org/soap/envelope/">
    <S:Body>
        <ns:getLocationDataResponse xmlns:ns="http://tempuri.org">
            <result>
                <dateGps>2019-10-15T08:48:50.000Z</dateGps>
                <ignition>false</ignition>
                <latitude>75.9270866</latitude>
                <longitude>-85.5207616</longitude>
                <speedGps>0.0</speedGps>
                <unitPlate>JRT1550</unitPlate>
                <altitude>284.0</altitude>
                <course>N</course>
                <deviceId>866258048802349</deviceId>
                <numSat>15</numSat>
                <odometer>59845</odometer>
                <eventId>401</eventId>
            </result>
        </ns:getLocationDataResponse>
    </S:Body>
</S:Envelope>
```

#### Managing

To edit or stop data being forwarded, follow these steps:

1. In any **Data forwarding** block, click <img src="../../../../.gitbook/assets/image (39).png" alt="" data-size="line"> to open the list of available protocols.
2. Click <img src="../../../../.gitbook/assets/Untitled-20250425-103233.png" alt="" data-size="line"> to change retranslator settings such as name, login information, or enabled status.
3. Click <img src="../../../../.gitbook/assets/image-20250425-104605.png" alt="" data-size="line"> and confirm to delete a retranslator

All changes are saved automatically.

{% hint style="info" %}
To enable/disable a retranslator for a certain device, switch the toggle with the needed retranslator name in the device's **Data forwarding** block.
{% endhint %}

#### Troubleshooting

To verify and test your SOAP request to the Navixy platform, use SoapUI which can be found here: [https://www.soapui.org/downloads/soapui/](https://www.soapui.org/downloads/soapui/)

1. Install Soap UI
2. From the file menu, select “New SOAP Project”
3. Paste the correct path into the WSDL field according to the server and select **Create sample requests for all operations?**
4. US: https://soap.us.navixy.com/LocationDataService?wsdl
5. EU: https://soap.navixy.com/LocationDataService?wsdl
