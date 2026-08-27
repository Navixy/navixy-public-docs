---
title: Raw IoT Data API
description: Overview of Navixy Raw IoT Data API.
---

# Raw IoT Data API

The Navixy Raw IoT Data API is for developers and data engineers who need access to raw data from GPS and telematics devices. It extracts unprocessed data with high granularity, which supports detailed analysis and customized solutions. Use it to retrieve data for applications such as analytics, reporting, and data science.

## Overview

The structure of the Raw IoT Data API is mostly similar to the [Backend API](../../getting-started.md). If you're familiar with the basics of the user API, you will find this API intuitive and easy to work with. The Raw IoT Data API provides methods to access raw, unprocessed data from your connected devices.

## Time frame limits

The Raw IoT Data API allows you to request raw data for periods ranging from one to several months, depending on your plan restrictions. The maximum time frame for data retrieval is determined by your subscription plan, with a common limit for lower tiers being 30 days from the current date. Data stored beyond this period cannot be requested through the Raw IoT Data API.

## Base URL

Raw IoT Data API resides in the `dwh` subsection of the API URL and does not belong to backend APIv2. You need to determine the URL to API calls like this:

* `https://api.eu.navixy.com/dwh/v1` for the European Navixy ServerMate platform.
* `https://api.us.navixy.com/dwh/v1` for the American Navixy ServerMate platform.
* `https://api.me.navixy.com/dwh/v1` for the Middle East Navixy ServerMate platform.

For example, to make raw data readings API request in the European Navixy ServerMate, you need to use this URL:

```
https://api.eu.navixy.com/dwh/v1/tracker/raw_data/read
```

## Authentication and authorization

### Authentication

Authentication is handled by the [Backend API](../../getting-started.md).

### Authorization

Requests to the Raw IoT Data API are made using a user session hash or API key. It can be passed as the Authorization HTTP header with the NVX auth scheme or within a `-d` (data) command.

Example:



{% tabs %}
{% tab title="With Authorization header" %}
```shell
curl --request POST \
  --url 'https://api.eu.navixy.com/dwh/v1/tracker/raw_data/get_inputs' \
  --header 'accept: text/csv' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: NVX 8a41497ed8e77fa68b9c4a9420971fdb' \
  --data '{"tracker_id": 123456}'
```
{% endtab %}

{% tab title="With hash" %}
```shell
curl --request POST \
  --url 'https://api.eu.navixy.com/dwh/v1/tracker/raw_data/get_inputs' \
  --header 'accept: text/csv' \
  --header 'Content-Type: application/json' \
  --data '{"hash": "6dc7d304dec4434f4c4202ec42817f83","tracker_id": 123456}'
```
{% endtab %}
{% endtabs %}

## Response format

Depending on the API request, responses can be in `application/json` or `text/csv` content types.

### Errors

Errors are distinguished by HTTP status codes (>= 400) and follow [RFC 7807](https://datatracker.ietf.org/doc/html/rfc7807).

Example:

```json
{
  "type": "errors/default/bad-request",
  "title": "Bad Request",
  "status": 400,
  "detail": "id: must be greater than or equal to 1"
}
```

#### Common error types

* `errors/default/bad-request` - Causes: missing or invalid parameter value.
* `errors/default/unauthorized` - Causes: missing `Authorization` header or credentials are insufficient or expired.

### Date/time formats

The API uses date/time formats according to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). For detailed information on date/time formats and examples, refer to [Raw Data Request - Specifying Time Frame](../../guides/raw-data.md#specifying-time-frame).

## Examples

### Example of getting inputs

This example shows how to use the `get_inputs` method to retrieve available metering inputs and state fields of a device.

```shell
curl --request POST \
  --url 'https://api.eu.navixy.com/dwh/v1/tracker/raw_data/get_inputs' \
  --header 'Content-Type: application/json' \
  --data '{
    "hash": "6dc7d304dec4434f4c4202ec42817f83",
    "tracker_id": 123456
  }'
```

### Example of reading Raw IoT Data

This example shows how to use the `read` method to fetch parsed raw data values received from tracking devices and decoded by the Navixy platform.

```shell
curl --request POST \
  --url 'https://api.eu.navixy.com/dwh/v1/tracker/raw_data/read' \
  --header 'accept: text/csv' \
  --header 'Content-Type: application/json' \
  --data '{
    "hash": "6dc7d304dec4434f4c4202ec42817f83",
    "tracker_id": "123456",
    "from": "2023-11-30T07:13:00.000Z",
    "to": "2023-11-30T07:15:00.000Z",
    "columns": [
      "lat",
      "lng",
      "speed",
      "inputs.ble_lls_level_1",
      "inputs.hw_mileage",
      "discrete_inputs.*"
    ]
  }'
```

With the Navixy Raw IoT Data API, developers and data engineers can access and analyze detailed raw data from their GPS and telematics devices.

## More in this section

<!-- endpoint-reference:start -->

#### Raw IoT Data

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/raw_data/get_inputs`](raw-data.md#post-tracker-raw_data-get_inputs) | POST | Get available device attributes |
| [`/tracker/raw_data/read`](raw-data.md#post-tracker-raw_data-read) | POST | Export parsed raw data |

<!-- endpoint-reference:end -->
