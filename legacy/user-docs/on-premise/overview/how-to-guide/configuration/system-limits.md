---
description: Adjust report and alert device limits in Navixy on-premise to prevent server overload. Edit configuration files on Linux or Windows to set date range caps.
---

# System limits

The Navixy platform configuration imposes limits to prevent overloading the server. This page explains what parameters Navixy offers as limits and how to adjust them. By setting limits on the number of devices and the reporting period, Navixy ensures optimal performance and reliable service while minimizing the risk of overload or downtime.

## Adjusting default limits

By default, the Navixy platform set limitations when it comes to working with reports and alerts. Specifically, users can only create reports and set up notifications for a limited number of devices, and can generate reports for a maximum period of time. These restrictions are in place to prevent overloading the server since report generation and notification setup require significant server resources.

In some business scenarios, you may require higher limits than those imposed by default on Navixy's platform. In such cases, you can adjust the limits, typically by increasing them. For Navixy's On-premise solution, you can modify these limits if your server's performance is adequate. This way, you can generate more reports and notifications or expand the coverage of your device network, without overloading the server.

{% hint style="danger" %}
The instructions on this page involve modifying the configuration files of the Navixy platform. Please note that making changes to the configuration without due care and attention may result in the platform malfunctioning or becoming inoperable. Therefore, it is crucial to back up the configuration before making any modifications to the default settings.
{% endhint %}

## Number of devices in Reports

Maximum number of devices per report may vary depending on the platform version and the previously made settings. To change the maximum number of devices, you need to edit _**Config.js**_ file located at the path:

`/var/www/pro-ui/Config.js` (Linux)

`C:\nginx\www\pro-ui\Config.js` (Windows)

In this file you need to find the _reportsMaxTrackersCount_ block. In recent versions of the platform it looks like this:

```editorconfig
reportsMaxTrackersCount:{
StayInPlaces: 100,
TripsAndEvents: 100,
Zone: 100,
DetailingOfTransmittedData: 1
},
```

In this block you can specify limits separately for each report type.

<details>

<summary>Full list of report types</summary>

* SOS
* Fall
* Detach
* Safety
* LocationRequest
* VehicleReadings
* DeviceOnOff
* ExternalPower
* LowBattery
* DeviceStatus
* Speeding
* RoadRulesViolations
* DeviceOnOffIdle
* TaskReport
* DrivingQuality
* StatusReport
* DriverChangesReport
* FormFieldsValues
* TripByState
* TripByShifts
* FuelConsumption
* Checkin
* DrivingQuality
* StayInZones
* StayInPlaces
* TripsAndEvents
* ZoneEvents
* DetailingOfTransmittedData
* FormFieldsValues
* TaskReport
* Event
* TaskFullReport

</details>

However, in most cases it is not necessary to configure the limits separately, and it is enough to specify a general limit for all the reports. To do this, add the line `maxTrackersPerReport: XXXXX` to the configuration **after** the above block. Example (limit of 1000 devices per report):

```editorconfig
reportsMaxTrackersCount:{
StayInPlaces: 100,
TripsAndEvents: 100,
Zone: 100,
DetailingOfTransmittedData: 1
},
maxTrackersPerReport: 1000,
```

After these changes, the maximum number of devices per report will be increased to 1000.

The _maxTrackersPerReport_ parameter applies to all reports except for **Geofence visits** (Zone) and **POI visits** (StayInPlaces) reports. Due to peculiarities of the platform, restrictions for these reports should be specified separately in the above block:

```
StayInPlaces: 1000,
Zone: 1000,
```

Another report that is not affected by the general setting is **Point report**. It can be generated for a single device only.

## Number of devices in Alerts

The maximum number of devices for alerts is also specified in the _Config.js_ file:

* `/var/www/pro-ui/Config.js` (Linux)
* `C:\nginx\www\pro-ui\Config.js` (Windows)

You need to find the line:

```
greenModeTrackersCount: 100,
```

The value in this line adjusts the number of devices you can select when setting up an alert.

## Date range in Reports

By default, the maximum date range for most reports is 90 days. This value can also be increased, but you need to change a different configuration file to do this. You need to find the _api-server_ configuration. The file is located at the path:

* `/home/java/api-server/conf/config.properties` (Linux)
* `C:\java\api-server\conf\config.properties` (Windows)

Open this file in a text editor and find the following line:#reports

```
maxReportTimeSpan=90d
```

Change the _90d_ value to whatever you need. Note that the number of days must be followed by the letter **d**.
