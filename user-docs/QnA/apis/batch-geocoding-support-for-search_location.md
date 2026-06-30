---
description: Loop through coordinates in your code to call search_location for each pair. Batch geocoding is not supported by the geocoder endpoint
---

# Batch geocoding support for search\_location

## Question

Does `geocoder/search_location` support batch coordinate geocoding?

## Answer

No.

Geocoders typically handle one coordinate pair per request.

If you need to process multiple coordinates, do it in your code:

* loop through the coordinate list in your code
* call `search_location` per coordinate
* collect results

## Linksss

* [`geocoder/search_location`](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/tracking/geocoder#search_location)
