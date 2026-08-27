---
title: Fuel sensor quality index
description: Contains API calls to interact with fuel sensor quality index.
---

# Fuel sensor quality index

## Resource

Resource path: `/trackers/$tracker_id/sensors/$sensor_id/quality`, on the `/eco_fleet/v1` base path.

Returns the fuel sensor quality index calculated from sensor readings within a specified datetime period.

`smoothness` is a score of the sensor readings. Higher values indicate reduced noise in sensor readings, while lower values suggest increased noise. It is a `Score`, an abstract measurement from 1.0 to 10.0 inclusive.

Note that a successful response is a bare JSON object, with no `success` field, and that errors follow RFC 7807 problem details rather than the numeric `status.code` the Platform API uses.

{% openapi-operation spec="eco-fleet" path="/trackers/{tracker_id}/sensors/{sensor_id}/quality" method="get" %}
[OpenAPI eco-fleet](../../../reference/Eco_Fleet.json)
{% endopenapi-operation %}

#### Errors

This API reports errors as RFC 7807 problem-detail strings, so the [general error codes](../../../../general/errors.md#error-codes) do not apply to it:

* `errors/entity/not-found` - Entity not found. Thrown if sensor or calibration table is missing.
* `errors/external-api/navixy` - Error accessing Navixy API. See `detail` field and consult [Backend API documentation](../../../../general/errors.md).
* `errors/sensors/quality/not-enough-readings` - Not enough sensor readings in given interval. Try using interval with enough vehicle usage or changing readings' sending frequency and waiting for data accumulation.
* `errors/default/unauthorized` - No credential supplied.
