---
title: Geocoder
description: Turn an address into map locations, and a location back into an address.
---

# Geocoder

The geocoder converts between addresses and coordinates. `geocoder/search_address` takes an address and returns the locations that match it, most relevant first. `geocoder/search_location` takes a coordinate pair and returns the address at that point.

The two directions overlap in one place: a `q` value that is two decimal numbers separated by a comma, such as `37.825014,-122.372020`, is read as a coordinate pair, so `geocoder/search_address` performs a reverse lookup instead and returns a single result. Both numbers must carry a decimal point for that to happen.

Each request names the provider it wants in its `geocoder` parameter. Accounts with premium GIS always use Google, whatever the request asks for.

## API actions

API base path: `/geocoder`.

Operations that list no errors of their own return only the [general error codes](../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/geocoder/search_address" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 210 - No location matched the query.

***

{% openapi-operation spec="navixy-platform" path="/geocoder/search_location" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
