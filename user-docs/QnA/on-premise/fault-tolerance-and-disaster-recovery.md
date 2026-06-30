---
title: Fault tolerance and disaster recovery
description: Back up and replicate the database to separate servers. Maintain a standby application copy and DB replica in remote infrastructure
---

# Fault tolerance and disaster recovery

## Does the platform have a built-in fault tolerance mechanism?

No, the platform is just software installed on the server. All fault tolerance must be implemented at the server infrastructure level.

## What steps should I take to ensure fault tolerance?

The main thing you need to take care of is the safety of your database, as it stores all your tracking history. This can be done by different methods, which are best combined:

* Backing up your database.
* Replicating the database to a separate server, preferably isolated from the current infrastructure.

It is also a good practice to maintain backup servers that can be easily brought online in case the primary nodes fail. For this purpose, it is recommended to have a standby copy of the application and a working database replica. Preferably, these instances should be located in a separate infrastructure, geographically remote, to insure against any kind of natural or human-made disaster.

## Can I automate hot-switching between the main node and replica?

Unfortunately, the platform doesn't offer such features. Switching must be done manually under the supervision of a qualified technician.

## Can I have more than one platform instance to provide fault tolerance?

Only a standby copy. Maintaining multiple active instances is impossible due to licensing peculiarities. The license can only be used on one active server, otherwise the license key will be corrupted.

## Links

* [High availability and Disaster Recovery](https://app.gitbook.com/s/kUnMmePH99SsdChtqqu7/on-premise/how-to-guide/maintenance/high-availability-and-disaster-recovery)
