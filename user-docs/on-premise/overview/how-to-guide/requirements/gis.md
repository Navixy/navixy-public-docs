---
description: "Configure GIS services for Navixy on-premise: geocoding, routing, distance matrix, and LBS. Supported providers include Navixy Premium GIS, Google Maps, and Nominatim."
---

# GIS

## How Navixy uses GIS

Navixy platform integrates with supplemental GIS (Geographic Information Services) to provide accurate and efficient location-based tracking and monitoring services for fleets and assets. The examples of such services include:

* **Geocoding** is the process of converting addresses, such as a street address, into geographic coordinates like latitude and longitude, and vice versa.
* **Routing** involves creating a path between two points on the map. To enable geocoding and routing, you need a geocoder/route provider.
* **Distance matrix** is used to calculate the distance and travel time between multiple points on the map. This information can be used to optimize routes, estimate travel time and fuel consumption, and to provide location-based analytics.
* **LBS/Cell ID databases** are used to provide location data for devices that do not have GPS capabilities or they are temporarily limited. In these cases, Navixy uses the cell tower data to triangulate the position of the device. This technology is useful in areas with weak GPS signals or for devices that are inside buildings or other structures.

## Popular options for GIS

Navixy works with a range of GIS providers and below you will find information on how to integrate with the most common choices.

### Navixy Premium GIS

Navixy Premium GIS is an add-on package for the Navixy GPS tracking platform that offers advanced GIS features for businesses that require more precise location data and mapping capabilities. The package includes access to premium geocoding and routing services from Google, advanced mapping features, and the ability to create custom map layers.

With Premium GIS, businesses can get more accurate and detailed location information, optimize their routes and navigation, and create customized maps to suit their specific needs. The package is available as an add-on to Navixy's basic GPS tracking platform, and users can choose to add it to their subscription if they need advanced GIS functionality.

Integrating your On-Premise instance with Navixy Premium GIS is a simple and cost-effective process that covers most of the needs.

### **Google Maps**

Google Maps are one of the most popular choices around the world for its powerful geocoder and routing capabilities. If you prefer not to use Navixy Premium GIS, but to purchase Google Maps license separately, you need to follow some security procedures as described below.

For security reasons we recommend creating three Google API keys and restrict them as follows:

Google API key #1

Should be restricted by HTTP referrer as follows:

_.contoso.com/_

(Use your own domain instead 'contoso')

Should be restricted by following APIs:

* Google Maps JavaScript API
* Google Street View Image API

Google API key #2

Should be restricted by your server's IP address only, and by the following APIs:

* Google Maps Geocoding API
* Google Maps Geolocation API
* Google Maps Directions API

Google API key #3

is used for maps in email notifications. It should be restricted by HTTP referrer as follows:

_.contoso.com/_ and

* Google Static Maps API

You should also generate URL signing secret for this key.

Provide the above keys to Navixy tech support so that we can install them for you, or you can refer to our how-to guide if you wish to install them yourself.

If you need help with creating your own Google API keys, please refer to [this](../configuration/maps-and-gis/google-maps-and-geocoding.md) article.

Please note that according to Google usage policy, Google Premium plan is required to be able to use Google API for asset tracking purposes.

You can get more information from Google using the link below:

[https://developers.google.com/maps/pricing-and-plans/](https://developers.google.com/maps/pricing-and-plans/)

### OpenStreetMap

OpenStreetMap (OSM) and OpenSourceRoutingMachine (OSRM) are free and open-source alternatives to Google API for geocoding and routing. OSM is a collaborative project to create a free editable map of the world, while OSRM is a routing engine that can use OSM data to create routes between two points on the map.

The downside of using OSM is that it's less accurate than Google in some parts of the world, and it has usage limits. If you send too many geocoding requests per second, your IP might be blocked by OSM servers. In practice, you will start hitting this limit if you have approximately 700 devices and more.

However, if you're going to use a small number of devices, OSM geocoder and OSRM routing might be a viable option. Additionally, using OSM and OSRM is a good way to support open-source initiatives and contribute to the global mapping community.

### Nominatim

Nominatim is a free software that can provide geocoding capabilities using OpenStreetMap data. You can find more information on our [their official website.](http://nominatim.org/release-docs/latest/)

### Mapbox

Mapbox provides a highly accurate geocoding and routing service with global coverage, and it can also integrate with Navixy to provide additional features such as custom map styling and map data analysis. However, Mapbox is a paid service and may not be suitable for those looking for a free alternative.
