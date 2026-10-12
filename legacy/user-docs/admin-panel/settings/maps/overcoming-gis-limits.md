---
description: Avoid GIS API rate limits by using Premium GIS, enabling paid Google services, or self-hosting Nominatim
---

# Overcoming GIS limits

{% hint style="info" %}
In this article, GIS services (or Geographic Information Systems services) refer to combination of services like geocoding, routing, distance calculations, street view, and similar.
{% endhint %}

When relying on free or public GIS services, such as Google API keys or OpenStreetMaps servers, you may encounter usage limits. This can lead to services like geocoding, routing, or street maps becoming unavailable or functioning very slowly. To avoid these issues, Navixy recommends using Navixy's **Premium GIS** package or a similar paid GIS service.

There are several solutions to solve the issue of API limits:

* **Enable the Navixy GIS package (recommended)**\
  This is the most convenient and inexpensive method. See [Navixy GIS packages](navixy-gis-packages-compare.md) for more information.
* **Enable paid services in your Google Maps account**\
  Activating paid subscriptions for Google Maps APIs or similar paid services increases your usage limits. For information on prices and conditions, see Google's website. In this case, Navixy recommends creating two separate Google API keys and restrict them accordingly. The first key should only allow access to the Google Maps JavaScript API and Google Street View Image API, while the second key should only allow access to the Google Maps Geocoding API, Google Maps Geolocation API, and Google Maps Directions API. You can monitor usage statistics in your Google Developer Console.
* **Install your own Nominatim server**\
  For more information about Nominatim server, see [Nominatim](https://app.gitbook.com/s/kUnMmePH99SsdChtqqu7/on-premise/how-to-guide/installation/optional-software/nominatim).

If you have any questions regarding these solutions, don't hesitate to contact the Navixy customer success team.
