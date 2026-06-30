---
title: Network requirements for On-premise
description: "Open ports: 80, 443 (HTTP/HTTPS), 8383 (WebSocket), 32233 (license), 123 (NTP), device ports. Allow outbound access to Navixy servers"
---

# Network requirements for On-premise

## What are the network requirements for On-Premise platform?

The main network requirements are server availability from client PCs, server availability for tracking devices and outbound access to the authentication server. For more information, check the instructions on the Navixy website.

## Which ports must be open?

The following ports are used:

* 80 and 443 - for HTTP/HTTPS connections, e.g. for client access or API operation
* 8383 - for WebSockets operation in Air Console
* 32233 - for checking the platform license
* 123 - for public NTP server time synchronization (if applicable)
* 4779, 6994, 7669, 7677, 7685, 7761 and ranges 46982-47000 and 47650-47780 - for tracking devices connection.

{% hint style="info" %}
You do not need to open the entire pool of ports to connect devices. You can reduce the list of ports to only the necessary ones - check which ports your devices use on our website: [https://www.navixy.com/devices/](https://www.navixy.com/devices/)
{% endhint %}

## What external resources does the platform use?

During its operation, the platform accesses the following external network resources:

* auth.navixy.com and httpauth.navixy.com - licensing server;
* sms.navixy.com - push notification server;
* geocoder.navixy.com - geocoding server (converting coordinates to addresses);
* {a,b,c}-tiles.x-gpsmail.com, tiles.x-gpsmail.com - OSM map data server (map tiles);
* maps.googleapis.com - Google Maps, only applicable if you are using the Premium GIS package.
* Any third-party optional mapping and geocoding resources, if applied.
* SMS and email message gateways, if applied.
* Third-party user applications and iframe web-pages, if applied.

## Links

* [Network](https://app.gitbook.com/s/kUnMmePH99SsdChtqqu7/on-premise/how-to-guide/requirements/network)
