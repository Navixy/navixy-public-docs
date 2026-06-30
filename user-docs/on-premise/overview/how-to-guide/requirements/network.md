---
description: Configure inbound and outbound firewall rules for Navixy on-premise. Includes port requirements and estimated bandwidth for deployments with 2,000 active GPS devices.
---

# Network

## Network ports

The following **inbound** ports must be opened on the server:

* 80 TCP - HTTP communication
* 443 TCP - HTTPS communication. If you don't plan to use HTTPS this is not necessary
* 8383 TCP - Websocket, necessary for the GPRS air console
* 4779, 6994, 7669, 7677, 7685, 7761 and ranges 46982-47000 and 47650-47780 TCP/UDP - for tracker communications

List of network ports used for devices can be shortened to only allow [ports actually used by your devices](https://www.navixy.com/devices/).

Additionally, the following **outbound** ports needs to be open:

* Ports 443 and 32233 to [auth.navixy.com](http://auth.navixy.com) and [httpauth.navixy.com](http://httpauth.navixy.com) hosts - mandatory for license authorization and tracking service operation
* Port 443 to [sms.navixy.com](http://sms.navixy.com) and [geocoder.navixy.com](http://geocoder.navixy.com) - required for operation of application push service and geocodr service (if applicable according to tariff)
* Port 123 to public network, or to selected NTP server - For time synchronization (correct time is critical for platform functioning)

{% hint style="danger" %}
Connection to [auth.navixy.com](http://auth.navixy.com) is crucial. Services will not work without proper authorization.
{% endhint %}

## Traffic

Navixy platform requires a constant exchange of data with GPS trackers, as well as downloading map tiles from map providers, resulting in a steady network traffic. However, the average network load is relatively small, and most standard ISP's unlimited plans should suffice. For 2000 GPS tracking devices, the approximate network load is as follows:

**Incoming:**

Average 200-400 kBit/s

With occasional peaks up to 800 kBit/s

**Outgoing:**

Average 300-800 kBit/s

With occasional peaks up to 6 MBit/s

It's important to note that these are estimates and may vary depending on the actual usage of the GPS devices and the number of map tiles being downloaded. Therefore, it's recommended to monitor the network traffic regularly and adjust the network bandwidth accordingly. Keep in mind that the number 2000 was taken as an example, but it should give a general idea of the network consumption. However, the actual traffic consumption will depend on the used trackers, so it's important to consider some overhead, especially if you plan to use more than 2000 trackers. Additionally, since outgoing traffic is prevalent, it may be advantageous to select an ISP plan that corresponds with your anticipated network usage.
