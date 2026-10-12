---
title: Run instance in a classified environment
description: Isolate from external network with a private device subnet. Licensing, maps, and geocoding require outbound access to Navixy servers
---

# Run instance in a classified environment

## Can I isolate my instance from the external network?

Yes, but not fully. There are several crucial resources that still need to be accessible:

* Navixy authentication server (`auth.navixy.com` and `httpauth.navixy.com`). It is required to check your instance license and exchange statistics for billing.
* Map resources. The platform does not have built-in maps, and all maps are taken from external resources (OSM, Google and others).
* Geocoding resources. To convert coordinates into addresses and vice versa, the platform needs to do requests to external geocoders - either provided by Navixy or by third parties.

## How do tracking devices connect to my platform if it's in an isolated infrastructure?

The best scenario is when tracking devices are on the same private network as the server. Many mobile internet providers offer the option of setting up private subnets for such purposes. However, there is no universal solution. It depends on the available options.

If you can't unite the server and tracking devices into a single private network, the connections from the devices will be external. In this case, the server must have a public IP address, and you need to open external incoming connections on the required ports.
