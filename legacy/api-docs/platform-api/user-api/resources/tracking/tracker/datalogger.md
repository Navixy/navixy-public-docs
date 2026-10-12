---
title: Datalogger
description: Upload track data a device recorded offline, so it joins the tracker's history.
---

# Datalogger

A datalogger is a device that records track data itself rather than streaming it. This operation uploads what it recorded, as a CSV file in a multipart request, so the points join the tracker's history.

The tracker has to be a datalogger. Other models answer error 214.

## API actions

API base path: `/tracker/datalogger`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/datalogger/upload" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 214 - The tracker is not a datalogger.
* 219 - The tracker is a clone.
* 233 - No file part was sent.
