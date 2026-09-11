---
description: >-
  Let a third-party system query historical GPS data from Navixy over SOAP,
  requesting up to 100 devices and one day of data per call.
---

# Navixy Web Service

Navixy Web Service gives a third-party system a SOAP endpoint that it queries for tracking data. It's the option to choose when the receiving system prefers to pull data on its own schedule, or when it can't accept an inbound connection.

{% hint style="warning" %}
Navixy Web Service works differently from every other protocol in the **Data forwarding** block. The others push each message to a destination as it arrives. Navixy Web Service pushes nothing: Navixy hosts an endpoint, and the receiving system requests the data it wants.

This has two consequences. Navixy never connects to the receiving system, so the **Address** and **Port** fields are unused. And because the receiving system reads stored data rather than a live stream, a query can cover a past period, which no other protocol supports.
{% endhint %}

Because the data is XML over SOAP and no Navixy-specific format is involved, this protocol suits any partner that can consume a WSDL.

In Navixy, select **Navixy Web Service** in the **Protocol** dropdown.

## What the endpoint returns

The endpoint returns one record per stored position, with these fields:

| Field                      | Contents                                                                                                                                                                                |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `deviceId`                 | The device id as given in the request                                                                                                                                                   |
| `dateGps`                  | When the device recorded the position, in UTC                                                                                                                                           |
| `latitude` and `longitude` | Position in degrees                                                                                                                                                                     |
| `altitude`                 | Altitude in meters                                                                                                                                                                      |
| `speedGps`                 | Speed in kilometers per hour                                                                                                                                                            |
| `course`                   | Heading as a compass direction, such as `N`, `SE`, or `O`                                                                                                                               |
| `ignition`                 | Whether the ignition was on                                                                                                                                                             |
| `numSat`                   | Number of satellites the device was using                                                                                                                                               |
| `odometer`                 | The odometer reading reported by the device, in kilometers                                                                                                                              |
| `unitPlate`                | The registration number of the vehicle linked to the device                                                                                                                             |
| `eventId`                  | The event code, following [Navixy Generic Protocol event identifiers](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-generic-protocol/predefined-event-identifiers) |

Two fields depend on data outside the device. `unitPlate` is empty when the device isn't linked to a vehicle, and Navixy shortens the registration number by removing spaces and hyphens and keeping the first six characters. A device that isn't permitted to connect returns no records at all rather than an error.

## Request limits

Each request must stay within these limits, or the endpoint returns a fault:

* At most **100 device ids** per request.
* At most **one day** between the start date and the end date. A slightly longer range is tolerated, but a request spanning several days fails with `Too long interval`.

To collect a longer period, send one request per day.

## Before you start

Unlike the other protocols, you don't need anything from a third party. You choose the login and password, and the receiving system uses them to authenticate.

Pick a login that no other retranslator in your account already uses, because Navixy identifies the retranslator by the login and password pair.

## Set up the endpoint

{% stepper %}
{% step %}
### Open the Data forwarding block

Go to **Devices and settings**, select a device, then find the **Data forwarding** block.
{% endstep %}

{% step %}
### Open the protocol list

Click **Protocols**, then click **+** to add a configuration.
{% endstep %}

{% step %}
### Enter the Navixy Web Service settings

Fill in the fields as follows:

* **Name**: a label that identifies this retranslator
* **Protocol**: **Navixy Web Service**
* **Address**: any valid address. This protocol doesn't use it, but the field is required.
* **Port**: any port number. This protocol doesn't use it either.
* **Login**: a login of your choice, not already used by another retranslator
* **Password**: a password of your choice

Give the login and password to the party that will query the endpoint.
{% endstep %}

{% step %}
### Enable and save

Switch on **Enabled**, then click **Save**.
{% endstep %}

{% step %}
### Link the retranslator to your devices

In each device's **Data forwarding** block, switch on the toggle for the retranslator you created, then click **Save**. Only linked devices return data. External ID isn't used by this protocol.
{% endstep %}
{% endstepper %}

## Query the endpoint

The WSDL address depends on which platform hosts your account:

| Platform | WSDL                                                                                                       |
| -------- | ---------------------------------------------------------------------------------------------------------- |
| EU       | [https://soap.navixy.com/LocationDataService?wsdl](https://soap.navixy.com/LocationDataService?wsdl)       |
| US       | [https://soap.us.navixy.com/LocationDataService?wsdl](https://soap.us.navixy.com/LocationDataService?wsdl) |

Send a request in this form, with the login and password in the SOAP header and the device ids and date range in the body. Device ids are the IMEI values of the devices:

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
         <deviceIds>866258048802349</deviceIds>
         <startDate>2022-08-30T00:00:00Z</startDate>
         <endDate>2022-08-31T00:00:00Z</endDate>
      </tem:dataRequest>
   </soapenv:Body>
</soapenv:Envelope>
```
{% endcode %}

The response contains one `result` element per stored position:

{% code overflow="wrap" %}
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
{% endcode %}

## Manage the endpoint

To change or withdraw access:

* Switch off the toggle in a device's **Data forwarding** block to exclude that device from query results, and keep the others available.
* Click **Protocols**, select the row, and edit it to change the login or password. Queries using the old credentials stop working immediately, so tell the querying party before you change them.
* Delete the configuration from the **Protocols** list to withdraw access entirely. Confirm in the dialog.

## Troubleshooting

When a query returns no data, check the following in order:

1. The retranslator is enabled, and its toggle is switched on for the devices you're querying.
2. The login and password in the SOAP header match the retranslator configuration.
3. The device ids in the request are the IMEI values of devices in your account.
4. The date range covers a period when those devices were reporting.
5. The date range spans no more than one day, and the request lists no more than 100 device ids.

To test a request before writing integration code, use [SoapUI](https://www.soapui.org/downloads/soapui/):

1. Install SoapUI.
2. From the **File** menu, select **New SOAP Project**.
3. Paste the WSDL address for your platform into the **WSDL** field.
4. Select **Create sample requests for all operations**, then fill in the credentials and device ids.
