---
title: What hardware to use?
description: "Minimum: 4 CPU cores, 8 GB RAM, 500 GB SSD storage. Physical or virtual servers work; use dedicated DB for 5000+ active devices"
---

# What hardware to use?

## Do I need a physical server, or can I use a virtual machine?

For Navixy platform to work, it doesn't matter whether you are using a physical or virtual server. Either type of server is appropriate as long as it shows sufficient performance and reliability.

Most current customers use virtual servers, both in cloud and on-premises data centers, as it gives more flexibility in administration and resource management. However, running the platform on a physical local server is also possible and is not associated with any extra difficulties.

## What are the minimum hardware requirements for running Navixy On-Premise?

The amount of resources directly depends on the intensity of Navixy platform usage, mainly on the number of tracked objects.

The minimum requirements are as follows:

* Processor (CPU): 4 cores (Intel/AMD 64-bit architecture)
* Memory (RAM): 8 GB
* Storage (Disk Space): At least 500 GB of free space, SSD recommended for faster performance.

As the number of tracked assets grows, so does the consumption of resources, so you need to allocate them accordingly.

## Should I use a dedicated server to store the database?

This is not a strict recommendation, but a best practice for large size instances with more than 5000 simultaneously active tracking devices. This way, you can distribute the resources more efficiently.

## Links

* [Server hardware](https://app.gitbook.com/s/kUnMmePH99SsdChtqqu7/on-premise/how-to-guide/requirements/server-hardware)
