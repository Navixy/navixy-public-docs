---
description: Migrate the full Navixy on-premise platform to a new server. Covers prerequisites, database migration strategies, service transfer, IP management, and startup procedures.
---

# Entire platform migration

Platform migration essentially consists of database migration and service migration. In this case, you need to install all the accessory software on the new server, then move the database and copy the services.

The steps described below apply equally to Linux and Windows installations. Moreover, they can be used for migration from Windows to Linux, or vice versa.

## Step 1 - pre-requisites

First of all, you need to prepare the server (or servers) to run the platform. In general, production capacity should be no weaker than existing one, i.e. you need to make sure that the server has the same or superior number of CPU cores, RAM and disk space. The network infrastructure should have sufficient internet speed, and the same ports should be open for the platform to run as on the previous server.

In general, when calculating the parameters of the new server, you can rely on the [hardware requirements](../../requirements/server-hardware.md) given in the corresponding section of our website.

From a software standpoint, the same third-party software must be installed as on the source server. This includes:

* Java SE Development Kit (JDK) 17
* MySQL Server 8.0
* NGINX 1.2 or newer

Only major versions matter here, and the difference in minor versions will have no effect on functionality.

## Step 2 - database migration

This is the longest and most resource-intensive step because the database grows over time and can reach significant size.

Basic strategy here is to take a backup of the existing database, then move it to a new server and restore the database from the backup there. This is a simple, reliable and proven working method, but the disadvantage is the long down time, which becomes even longer the larger database is.

If you have a separate disk on your old server to host the database, you can simply unmount it and then mount it on the new server. This way the database will move almost instantly. Unfortunately, this method only works when virtualizing within the same data center or cloud provider (AWS, Azure, etc.). If you change data centers, this method is not applicable.

For large databases and in cases where minimizing downtime is crucial, an advanced method known as database replication should be used. In this case, a database replica is created on the target server, and it is gradually populated with data and runs as a slave. This procedure is slow, but allows the master database to continue to run normally without downtime. When the slave catches up with the master in terms of population, all that is left is to switch the master-slave, and thus the database will be completely migrated to the new server with all the actual data.

Replication is not a standard MySQL feature, so it requires the use of third-party software for this purpose. There are different solutions and each system administrator has their own preferences in tools. Our preferred tool is [XtraBackup](https://www.percona.com/software/mysql-database/percona-xtrabackup) application from Percona which allows to make hot backups and replication of a working database.

{% hint style="danger" %}
When copying the database, the license key is corrupted because it is only usable on one server. You may need to backup the license key separately before switching to the database on the new server. This is the symbol string in the `fingerprint` line of the `google.variables` table. You need to save this value and substitute it in the same place before starting the new database. If the key was not backed up and got corrupted, Navixy tech support will reissue it for free.
{% endhint %}

## Step 3 - services migration

The only constantly changing place of the platform is the database. At the same time, the platform services and the website remain static, and changes occur only during the platform updates. So all you need to do is copy the necessary directories including their contents to the same paths.

On Linux these are:

* /home/java/
* /var/www
* /etc/nginx/ (all configurations and SSL certificates)

On Windows these are:

* C:\java
* C:\nginx

After that, you need to configure Navixy services to run. On Linux this is done with _systemd_, on Windows with _Java Wrapper_. The configurations on the old and new server should be the same. If you don't know how to configure these services, contact Navixy tech support.

{% hint style="info" %}
If operating systems on the old and new servers are not the same (for example if you are migrating from Windows to Linux), then Java service configurations will be completely different, as well as Nginx web server configuration. Contact Navixy support for details on how to configure the services.
{% endhint %}

## Step 4 - IP address change

Once you have moved the database and all services to the new server, you need to make this server primary and available to clients and their tracking devices.

To do this, you need to transfer the public IP address from the old server to the new one.

If the IP-address transfer is impossible (which can be the case when changing data centers), you need to make changes in the DNS configuration, binding your domain to the new public IP-address of the server.

{% hint style="info" %}
If the IP address changes, all devices configured for operation on IP will stop reporting data to the server and will require reconfiguration. All devices configured to use domain will keep working as before.
{% endhint %}

## Final steps

After changing the IP address, you need to stop running java services on the old server.

Then you can start all services on the new server. This completes the migration and you can check the availability of the migrated platform.

{% hint style="danger" %}
If you experience any problems during or after the migration, you can contact Navixy tech support at support@navixy.com for further consultation.
{% endhint %}
