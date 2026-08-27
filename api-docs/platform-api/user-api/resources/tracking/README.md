---
title: Tracking
description: Devices, where they have been, and the map areas and rules that give their movement meaning.
---

# Tracking

Tracking covers the device itself and everything the Navixy platform records from it: the trips it made, the points behind those trips, the beacons it saw, and the geofences and rules that turn raw movement into events someone can act on.

Most integrations start here, with [Tracker](tracker/README.md) for the device record and [Track](track/README.md) for what it did. The rest of the section supports those two: [Geofence](zone/README.md) and [Rules](tracker/rules/README.md) decide when an event is logged, [Status](status/README.md) records what a tracker is currently doing, and [Asset](asset/README.md) follows a subject across the several trackers it was attached to over time.

## Operations in this section

<!-- endpoint-reference:start -->

#### APN settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/apn_settings/read`](apn_settings.md#post-apn_settings-read) | POST | Read APN settings |

#### Delivery info

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/delivery/read`](delivery.md#post-delivery-read) | POST | Read delivery state |
| [`/delivery/list`](delivery.md#post-delivery-list) | POST | List delivery states |

#### Geocoder

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/geocoder/search_address`](geocoder.md#post-geocoder-search_address) | POST | Search address |
| [`/geocoder/search_location`](geocoder.md#post-geocoder-search_location) | POST | Search location |

#### Map layer

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/map_layer/read`](map_layer.md#post-map_layer-read) | POST | Read map layer |
| [`/map_layer/list`](map_layer.md#post-map_layer-list) | POST | List map layers |
| [`/map_layer/upload`](map_layer.md#post-map_layer-upload) | POST | Upload map layer |
| [`/map_layer/update`](map_layer.md#post-map_layer-update) | POST | Update map layer |
| [`/map_layer/delete`](map_layer.md#post-map_layer-delete) | POST | Delete map layer |

#### Retranslator

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/retranslator/create`](retranslator.md#post-retranslator-create) | POST | Create retranslator |
| [`/retranslator/delete`](retranslator.md#post-retranslator-delete) | POST | Delete retranslator |
| [`/retranslator/list`](retranslator.md#post-retranslator-list) | POST | List retranslators |
| [`/retranslator/update`](retranslator.md#post-retranslator-update) | POST | Update retranslator |
| [`/retranslator/protocols/list`](retranslator.md#post-retranslator-protocols-list) | POST | List retranslator protocols |

#### BLE beacon data

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/beacon/data/read`](beacon/README.md#post-beacon-data-read) | POST | Read BLE beacon data history |
| [`/beacon/data/last_values`](beacon/README.md#post-beacon-data-last_values) | POST | Read last BLE beacon values |

#### Route

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/route/get`](route/README.md#post-route-get) | POST | Get route |

#### Route with Google

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/route/google/get`](route/route_google.md#post-route-google-get) | POST | Get route with Google |

#### Route with OSRM

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/route/osrm/get`](route/route_osrm.md#post-route-osrm-get) | POST | Get route with OSRM |

#### Route with Progorod

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/route/progorod/get`](route/route_progorod.md#post-route-progorod-get) | POST | Get route with Progorod |

#### Status

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/status/create`](status/README.md#post-status-create) | POST | Create working status |
| [`/status/delete`](status/README.md#post-status-delete) | POST | Delete working status |
| [`/status/list`](status/README.md#post-status-list) | POST | List working statuses |
| [`/status/update`](status/README.md#post-status-update) | POST | Update working status |

#### Tracker's working status

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/status/tracker/assign`](status/tracker.md#post-status-tracker-assign) | POST | Assign working status to tracker |
| [`/status/tracker/list`](status/tracker.md#post-status-tracker-list) | POST | List trackers' working statuses |
| [`/status/tracker/read`](status/tracker.md#post-status-tracker-read) | POST | Read tracker's working status |

#### Listing

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/status/listing/create`](status/listing/README.md#post-status-listing-create) | POST | Create working status list |
| [`/status/listing/delete`](status/listing/README.md#post-status-listing-delete) | POST | Delete working status list |
| [`/status/listing/list`](status/listing/README.md#post-status-listing-list) | POST | List working status lists |
| [`/status/listing/update`](status/listing/README.md#post-status-listing-update) | POST | Update working status list |

#### Tracker's working status list

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/status/listing/tracker/assign`](status/listing/tracker.md#post-status-listing-tracker-assign) | POST | Assign working status list to tracker |

#### Track

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/track/download`](track/README.md#post-track-download) | POST | Download track as KML |
| [`/track/list`](track/README.md#post-track-list) | POST | List tracks |
| [`/track/read`](track/README.md#post-track-read) | POST | Read track points |
| [`/track/visit/list`](track/README.md#post-track-visit-list) | POST | List visited geofences and places |

#### Waybill

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/track/waybill/download`](track/waybill/README.md#post-track-waybill-download) | POST | Download waybill |

#### Waybill settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/track/waybill/settings/read`](track/waybill/settings.md#post-track-waybill-settings-read) | POST | Read last waybill number |

#### Tracker

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/read`](tracker/README.md#post-tracker-read) | POST | Read one tracker |
| [`/tracker/list`](tracker/README.md#post-tracker-list) | POST | List trackers |
| [`/tracker/corrupt`](tracker/README.md#post-tracker-corrupt) | POST | Corrupt a tracker |
| [`/tracker/delete`](tracker/README.md#post-tracker-delete) | POST | Delete a clone |
| [`/tracker/change_phone`](tracker/README.md#post-tracker-change_phone) | POST | Change the SIM phone and APN |
| [`/tracker/get_diagnostics`](tracker/README.md#post-tracker-get_diagnostics) | POST | Read CAN and OBD values |
| [`/tracker/get_fuel`](tracker/README.md#post-tracker-get_fuel) | POST | Read fuel level |
| [`/tracker/get_inputs`](tracker/README.md#post-tracker-get_inputs) | POST | Read digital inputs |
| [`/tracker/batch_get_inputs`](tracker/README.md#post-tracker-batch_get_inputs) | POST | Read digital inputs for several trackers |
| [`/tracker/get_outputs`](tracker/README.md#post-tracker-get_outputs) | POST | Read outputs |
| [`/tracker/batch_get_outputs`](tracker/README.md#post-tracker-batch_get_outputs) | POST | Read outputs for several trackers |
| [`/tracker/output/update`](tracker/README.md#post-tracker-output-update) | POST | Rename an output |
| [`/tracker/get_last_gps_point`](tracker/README.md#post-tracker-get_last_gps_point) | POST | Read the last GPS point |
| [`/tracker/get_readings`](tracker/README.md#post-tracker-get_readings) | POST | Read general sensor values |
| [`/tracker/get_state`](tracker/README.md#post-tracker-get_state) | POST | Read tracker state |
| [`/tracker/get_states`](tracker/README.md#post-tracker-get_states) | POST | Read the state of several trackers |
| [`/tracker/list_models`](tracker/README.md#post-tracker-list_models) | POST | List tracker models |
| [`/tracker/tags/set`](tracker/README.md#post-tracker-tags-set) | POST | Set a tracker's tags |
| [`/tracker/location_request`](tracker/README.md#post-tracker-location_request) | POST | Request the current location |
| [`/tracker/register`](tracker/README.md#post-tracker-register) | POST | Register a tracker |
| [`/tracker/register_retry`](tracker/README.md#post-tracker-register_retry) | POST | Resend registration commands |
| [`/tracker/register_quick`](tracker/README.md#post-tracker-register_quick) | POST | Register a preconfigured device by IMEI |
| [`/tracker/replace`](tracker/README.md#post-tracker-replace) | POST | Replace the device behind a tracker |
| [`/tracker/replace_quick`](tracker/README.md#post-tracker-replace_quick) | POST | Replace a device by IMEI |
| [`/tracker/replace_retry`](tracker/README.md#post-tracker-replace_retry) | POST | Resend replacement commands |
| [`/tracker/send_command`](tracker/README.md#post-tracker-send_command) | POST | Send a special control command |
| [`/tracker/raw_command/send`](tracker/README.md#post-tracker-raw_command-send) | POST | Send a raw GPRS command |

#### Alarm mode

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/alarm_mode/read`](tracker/alarm_mode.md#post-tracker-alarm_mode-read) | POST | Read alarm mode |
| [`/tracker/alarm_mode/set`](tracker/alarm_mode.md#post-tracker-alarm_mode-set) | POST | Turn alarm mode on or off |

#### APN settings by tracker

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/apn_settings/read`](tracker/apn_settings.md#post-tracker-apn_settings-read) | POST | Read APN settings by tracker |

#### Avatar

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/avatar/upload`](tracker/avatar.md#post-tracker-avatar-upload) | POST | Upload a tracker avatar |

#### Chat

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/chat/list`](tracker/chat.md#post-tracker-chat-list) | POST | List chat messages |
| [`/tracker/chat/mark_read_all`](tracker/chat.md#post-tracker-chat-mark_read_all) | POST | Mark all messages read |
| [`/tracker/chat/mark_read`](tracker/chat.md#post-tracker-chat-mark_read) | POST | Mark messages read |
| [`/tracker/chat/send`](tracker/chat.md#post-tracker-chat-send) | POST | Send a chat message |
| [`/tracker/chat/broadcast`](tracker/chat.md#post-tracker-chat-broadcast) | POST | Send a chat message to several trackers |
| [`/tracker/chat/updated/list`](tracker/chat.md#post-tracker-chat-updated-list) | POST | Read last message times |
| [`/tracker/chat/unread/count`](tracker/chat.md#post-tracker-chat-unread-count) | POST | Count unread messages |

#### Contact

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/contact/list`](tracker/contact.md#post-contact-list) | POST | List contacts and their shared trackers (deprecated) |

#### Counters

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/counter/read`](tracker/counter.md#post-tracker-counter-read) | POST | Read a counter |
| [`/tracker/counter/update`](tracker/counter.md#post-tracker-counter-update) | POST | Update a counter |
| [`/tracker/get_counters`](tracker/counter.md#post-tracker-get_counters) | POST | Read all counter values |
| [`/tracker/counter/value/get`](tracker/counter.md#post-tracker-counter-value-get) | POST | Read one counter value |
| [`/tracker/counter/value/list`](tracker/counter.md#post-tracker-counter-value-list) | POST | Read one counter across several trackers |
| [`/tracker/counter/value/set`](tracker/counter.md#post-tracker-counter-value-set) | POST | Set a counter value |
| [`/tracker/counter/data/read`](tracker/counter.md#post-tracker-counter-data-read) | POST | Read counter history |

#### Datalogger

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/datalogger/upload`](tracker/datalogger.md#post-tracker-datalogger-upload) | POST | Upload datalogger track data |

#### Employee

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/employee/assign`](tracker/employee.md#post-tracker-employee-assign) | POST | Assign or unassign an employee |
| [`/tracker/employee/read`](tracker/employee.md#post-tracker-employee-read) | POST | Read the assigned employee |

#### Engine immobilizer

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/engine_immobilizer/read`](tracker/engine_immobilizer.md#post-tracker-engine_immobilizer-read) | POST | Read immobiliser state |
| [`/tracker/engine_immobilizer/set`](tracker/engine_immobilizer.md#post-tracker-engine_immobilizer-set) | POST | Engage or release the immobiliser |

#### Group

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/group/assign`](tracker/group.md#post-tracker-group-assign) | POST | Assign trackers to a group |
| [`/tracker/group/create`](tracker/group.md#post-tracker-group-create) | POST | Create a group |
| [`/tracker/group/delete`](tracker/group.md#post-tracker-group-delete) | POST | Delete a group |
| [`/tracker/group/list`](tracker/group.md#post-tracker-group-list) | POST | List tracker groups |
| [`/tracker/group/update`](tracker/group.md#post-tracker-group-update) | POST | Update a group |

#### LED

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/led/read`](tracker/led.md#post-tracker-led-read) | POST | Read LED state |
| [`/tracker/led/update`](tracker/led.md#post-tracker-led-update) | POST | Switch the LED |

#### Mobile app register

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/mobile/register`](tracker/mobile.md#post-tracker-mobile-register) | POST | Register a mobile application (deprecated) |

#### Digital output

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/output/set_all`](tracker/output.md#post-tracker-output-set_all) | POST | Switch all outputs |
| [`/tracker/output/set`](tracker/output.md#post-tracker-output-set) | POST | Switch one output |

#### Sensor readings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/readings/list`](tracker/readings.md#post-tracker-readings-list) | POST | Read all sensor values |
| [`/tracker/readings/batch_list`](tracker/readings.md#post-tracker-readings-batch_list) | POST | Read all sensor values for several trackers |

#### Retranslator binding

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/retranslator/bind`](tracker/retranslator.md#post-tracker-retranslator-bind) | POST | Bind a tracker to a retranslator |
| [`/tracker/retranslator/list`](tracker/retranslator.md#post-tracker-retranslator-list) | POST | List a tracker's retranslator bindings |
| [`/tracker/retranslator/unbind`](tracker/retranslator.md#post-tracker-retranslator-unbind) | POST | Unbind a tracker from a retranslator |

#### Trusted number

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/trusted_number/list`](tracker/trusted_number.md#post-tracker-trusted_number-list) | POST | List trusted numbers |
| [`/tracker/trusted_number/update`](tracker/trusted_number.md#post-tracker-trusted_number-update) | POST | Replace the trusted numbers |

#### Unconfirmed commands

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/command/unconfirmed/count`](tracker/command/README.md#post-tracker-command-unconfirmed-count) | POST | Count queued SMS commands |
| [`/tracker/command/unconfirmed/reset`](tracker/command/README.md#post-tracker-command-unconfirmed-reset) | POST | Clear the SMS command queue |

#### Rule

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/rule/bind`](tracker/rules/rule.md#post-tracker-rule-bind) | POST | Bind a rule to trackers |
| [`/tracker/rule/create`](tracker/rules/rule.md#post-tracker-rule-create) | POST | Create a rule |
| [`/tracker/rule/delete`](tracker/rules/rule.md#post-tracker-rule-delete) | POST | Delete a rule |
| [`/tracker/rule/list`](tracker/rules/rule.md#post-tracker-rule-list) | POST | List rules |
| [`/tracker/rule/unbind`](tracker/rules/rule.md#post-tracker-rule-unbind) | POST | Unbind a rule from trackers |
| [`/tracker/rule/update`](tracker/rules/rule.md#post-tracker-rule-update) | POST | Update a rule |

#### Sensor

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/sensor/batch_list`](tracker/sensor/README.md#post-tracker-sensor-batch_list) | POST | List sensors for several trackers |
| [`/tracker/sensor/create`](tracker/sensor/README.md#post-tracker-sensor-create) | POST | Create a sensor |
| [`/tracker/sensor/delete`](tracker/sensor/README.md#post-tracker-sensor-delete) | POST | Delete a sensor |
| [`/tracker/sensor/list`](tracker/sensor/README.md#post-tracker-sensor-list) | POST | List a tracker's sensors |
| [`/tracker/sensor/update`](tracker/sensor/README.md#post-tracker-sensor-update) | POST | Update a sensor |
| [`/tracker/sensor/batch_copy`](tracker/sensor/README.md#post-tracker-sensor-batch_copy) | POST | Copy sensors between trackers |
| [`/tracker/sensor/data/read`](tracker/sensor/README.md#post-tracker-sensor-data-read) | POST | Read sensor history |

#### Sensor calibration data

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/sensor/calibration_data/read`](tracker/sensor/calibration_data.md#post-tracker-sensor-calibration_data-read) | POST | Read calibration data |
| [`/tracker/sensor/calibration_data/update`](tracker/sensor/calibration_data.md#post-tracker-sensor-calibration_data-update) | POST | Replace calibration data |
| [`/tracker/sensor/calibration_data/upload_omnicomm`](tracker/sensor/calibration_data.md#post-tracker-sensor-calibration_data-upload_omnicomm) | POST | Upload an Omnicomm calibration file |

#### Sensor inputs

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/sensor/input_name/list`](tracker/sensor/input_name.md#post-tracker-sensor-input_name-list) | POST | List all sensor inputs and state fields |

#### Settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/read`](tracker/settings/README.md#post-tracker-settings-read) | POST | Read label and group |
| [`/tracker/settings/update`](tracker/settings/README.md#post-tracker-settings-update) | POST | Change label and group |

#### LBS settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/lbs/read`](tracker/settings/lbs.md#post-tracker-settings-lbs-read) | POST | Read the LBS radius limit |
| [`/tracker/settings/lbs/update`](tracker/settings/lbs.md#post-tracker-settings-lbs-update) | POST | Change the LBS radius limit |

#### Tracking mode

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/tracking/read`](tracker/settings/tracking.md#post-tracker-settings-tracking-read) | POST | Read tracking mode settings |
| [`/tracker/settings/tracking/update`](tracker/settings/tracking.md#post-tracker-settings-tracking-update) | POST | Change tracking mode settings |

#### Parking detection

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/trip_detection/read`](tracker/settings/trip_detection.md#post-tracker-settings-trip_detection-read) | POST | Read parking detection settings |
| [`/tracker/settings/trip_detection/update`](tracker/settings/trip_detection.md#post-tracker-settings-trip_detection-update) | POST | Change parking detection settings |

#### Special

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/settings/special/read`](tracker/settings/special/README.md#post-tracker-settings-special-read) | POST | Read special settings |
| [`/tracker/settings/special/update`](tracker/settings/special/README.md#post-tracker-settings-special-update) | POST | Change special settings |

#### Engine hours

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/stats/engine_hours/read`](tracker/stats/stats_engine_hours.md#post-tracker-stats-engine_hours-read) | POST | Read engine hours |

#### Mileage

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/stats/mileage/read`](tracker/stats/stats_mileage.md#post-tracker-stats-mileage-read) | POST | Read mileage per day |

#### Commands

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/command/create`](tracker/commands.md#post-tracker-command-create) | POST | Create a command |
| [`/tracker/command/update`](tracker/commands.md#post-tracker-command-update) | POST | Update a command |
| [`/tracker/command/execute`](tracker/commands.md#post-tracker-command-execute) | POST | Execute a command |
| [`/tracker/command/delete`](tracker/commands.md#post-tracker-command-delete) | POST | Delete a command |
| [`/tracker/batch_get_commands`](tracker/commands.md#post-tracker-batch_get_commands) | POST | Read commands for several trackers |

#### Geofence

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/zone/batch_convert`](zone/README.md#post-zone-batch_convert) | POST | Convert geofence batch |
| [`/zone/create`](zone/README.md#post-zone-create) | POST | Create geofence |
| [`/zone/delete`](zone/README.md#post-zone-delete) | POST | Delete geofences |
| [`/zone/list`](zone/README.md#post-zone-list) | POST | List geofences |
| [`/zone/read`](zone/README.md#post-zone-read) | POST | Read geofence |
| [`/zone/search_location`](zone/README.md#post-zone-search_location) | POST | Search geofences by location |
| [`/zone/update`](zone/README.md#post-zone-update) | POST | Update geofence |
| [`/zone/upload`](zone/README.md#post-zone-upload) | POST | Upload geofences |
| [`/zone/download`](zone/README.md#post-zone-download) | POST | Download geofences |

#### Geofence import

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/zone/import/start`](zone/import.md#post-zone-import-start) | POST | Start geofence import |
| [`/zone/import/read`](zone/import.md#post-zone-import-read) | POST | Read geofence import |
| [`/zone/import/list`](zone/import.md#post-zone-import-list) | POST | List geofence imports |
| [`/zone/import/download_failed`](zone/import.md#post-zone-import-download_failed) | POST | Download failed geofence rows |
| [`/zone/import/finish`](zone/import.md#post-zone-import-finish) | POST | Finish geofence import |

#### Geofence point

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/zone/point/list`](zone/zone_point.md#post-zone-point-list) | POST | List geofence points |
| [`/zone/point/update`](zone/zone_point.md#post-zone-point-update) | POST | Update geofence points |

#### Asset

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/asset/track/list`](asset/README.md#post-asset-track-list) | POST | List asset tracks |
| [`/asset/track/read`](asset/README.md#post-asset-track-read) | POST | Read asset track points |

#### Asset groups

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/asset_group/create`](asset/asset-groups.md#post-asset_group-create) | POST | Create asset group |
| [`/asset_group/list`](asset/asset-groups.md#post-asset_group-list) | POST | List asset groups |
| [`/asset_group/set`](asset/asset-groups.md#post-asset_group-set) | POST | Set assets in group |
| [`/asset_group/remove`](asset/asset-groups.md#post-asset_group-remove) | POST | Remove assets from group |
| [`/asset_group/update`](asset/asset-groups.md#post-asset_group-update) | POST | Rename asset group |
| [`/asset_group/delete`](asset/asset-groups.md#post-asset_group-delete) | POST | Delete asset group |

<!-- endpoint-reference:end -->
