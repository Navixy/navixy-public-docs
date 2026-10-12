---
title: BLE beacon data
description: What a tracker saw of the radio tags around it, as history or as last known values.
---

# BLE beacon data

BLE beacon data is what a tracker saw of the radio tags around it: iBeacon, Teltonika EYE Beacon and Sensor, Eddystone, and similar devices. Each scan records which tag was seen, how strong its signal was, and where the tracker stood at the time.

Two operations read that data. `beacon/data/read` returns the scans recorded between `from` and `to`, sorted by `get_time`. `beacon/data/last_values` returns the most recent scan of each beacon currently visible to the given trackers, and with an empty body it covers the last hour across every tracker the caller can reach.

## BLE beacon data entry

{% openapi-schemas spec="navixy-platform" schemas="BeaconDataEntry" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/beacon/data`.

***

{% openapi-operation spec="navixy-platform" path="/beacon/data/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 208 - One of the trackers is blocked, normally because its tariff ended.
* 217 - One or more of the tracker IDs does not exist or belongs to another user.

***

{% openapi-operation spec="navixy-platform" path="/beacon/data/last_values" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 208 - One of the trackers is blocked, normally because its tariff ended.
* 217 - One or more of the tracker IDs does not exist or belongs to another user.
