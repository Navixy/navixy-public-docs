---
title: Guides and examples
description: Whole tasks worked end to end, with the calls in the order an integration makes them.
---

# Guides and examples

The reference tells you what one operation does. These guides tell you which operations to call, in what order, and why: getting a tracker's track points takes three calls, and a report is generated, retrieved once it is ready, then deleted.

Start with [Getting list of GPS trackers](data-retrieval/get-tracker-list.md) if you are new to the API. Almost every other guide begins from a tracker ID.

## [Data retrieval](data-retrieval/README.md)

* [Getting list of GPS trackers](data-retrieval/get-tracker-list.md): the first call most integrations make, and where the other guides start.
* [Getting track points](data-retrieval/get-track-points.md): from a tracker to its trips, and from a trip to the raw points behind it.
* [Retrieving sensor and counter data](data-retrieval/sensor-data.md): reading what a device measured alongside its location.
* [Obtaining report information](data-retrieval/obtain-reports.md): generating a report and collecting the result, which is asynchronous.

## [Device management](device-management/README.md)

* [Activate device](device-management/activate-device.md): registering a GPS device or a mobile app on the Navixy platform.
* [Sending commands to a GPS tracker via IP protocol (OTA)](device-management/send-commands.md): reconfiguring a device remotely instead of physically.
* [Tracking stationary objects with BLE tags](device-management/ble-beacons.md): using radio tags for objects that carry no device of their own.

## [Places](places/README.md)

* [Managing geofences](places/manage-geofences.md): creating areas and using them to limit where a rule applies.
* [Managing POIs](places/manage-pois.md): creating the client sites and depots that tasks are assigned against.

## [Rules and notifications](rules-notifications/README.md)

* [Using rules](rules-notifications/use-rules.md): defining the conditions the platform watches for.
* [Working with notifications](rules-notifications/work-with-notifications.md): reading the events a rule logged, and how they are delivered.
* [Receiving push notifications in your app](rules-notifications/get-push-notifications.md): getting events to an app without polling for them.

## [Field service management](field-service-management/README.md)

* [How to create and assign tasks](field-service-management/manage-tasks.md): the starting point, and what the other three build on.
* [Changing employee statuses](field-service-management/change-task-statuses.md): recording what an employee is currently doing.
* [Using task forms](field-service-management/create-forms.md): collecting structured information when a task is completed.
* [Working with check-ins](field-service-management/check-ins.md): recording what an employee reported from a location.

## [Fleet management](fleet-management/README.md)

* [Managing vehicles and service works](fleet-management/service-works.md): the vehicle record, and the maintenance due against it.
* [Using driver journals](fleet-management/driver-journals.md): categorising trips so transport use splits between business and private.
* [Using tags](fleet-management/use-tags.md): labelling across entity types so related things can be found together.

## Raw IoT Data

* [Requesting Raw IoT Data](raw-data.md): reading unprocessed device data through the [Raw IoT Data API](../resources/data-warehouse-api/README.md), which has its own base URL and specification.
