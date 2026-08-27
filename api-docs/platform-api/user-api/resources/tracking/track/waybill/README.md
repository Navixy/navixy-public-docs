---
title: Waybill
description: Build a waybill document from a tracker's tracks over a period, returned as a DOCX file.
---

# Waybill

A waybill is a printable trip document built from a tracker's tracks over a period, returned as a DOCX file rather than as JSON.

The call has a side effect worth knowing about: it stores the waybill number it used, which [Waybill settings](settings.md) then reads back. A number consisting only of digits is incremented before being stored, so consecutive waybills carry on a sequence.

Waybills are a tariff feature and need the `app_fleet` feature on every tracker in the request.

## API actions

API base path: `/track/waybill`.

***

{% openapi-operation spec="navixy-platform" path="/track/waybill/download" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 236 - Feature unavailable due to tariff restrictions, when one of the trackers is on a tariff without the `app_fleet` feature.

## More in this section

<!-- endpoint-reference:start -->

#### Waybill settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/track/waybill/settings/read`](settings.md#post-track-waybill-settings-read) | POST | Read last waybill number |

<!-- endpoint-reference:end -->
