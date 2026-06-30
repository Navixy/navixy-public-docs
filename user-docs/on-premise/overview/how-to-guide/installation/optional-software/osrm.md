---
description: Install OSRM for local routing in Navixy on-premise with no request limits. Choose a routing profile, check hardware requirements, and access routes via REST API.
---

# OSRM

OSRM stands for Open Source Routing Machine, which is an open-source software designed for finding optimal routes between two points. By using OSRM with Navixy On-premise solutions, you can deploy it on your local server without limiting the number of requests. OSRM is not restricted by any maps, giving you the freedom to choose your cartography provider.

Deploying a local OSRM server can be very beneficial in cases where network access is restricted, and it's preferable to keep the connection within the local network. Other routing tools may also be too expensive or may not provide accurate data for your region.

OSRM is currently available under the 2-clause BSD license, and you can check if the data is available for your region by visiting their [official website](http://project-osrm.org/) and viewing their demo. The installation instructions and FAQ can be found on their [GitHub page](https://github.com/Project-OSRM/osrm-backend/wiki).

It is essential to ensure that your server meets the hardware and software requirements before installing OSRM. The hardware requirements will depend on the amount of data you plan to maintain. For instance, installing data for only one country or one continent will require fewer resources than data for the whole globe.

Once you have installed OSRM, you can access its routing services by making requests to the server through the REST API. OSRM supports several routing profiles, such as driving, cycling, and walking, which you can choose based on your use case.