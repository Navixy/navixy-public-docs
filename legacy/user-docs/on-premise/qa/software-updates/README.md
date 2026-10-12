---
description: >-
  Keep Navixy up to date on PaaS or on-premise. Covers update frequency,
  eligibility requirements, and links to OS-specific update procedures for Linux
  and Windows.
---

# Software updates

The Navixy platform is regularly updated with new features, improvements, and fixes. The following section provides instructions on how to update your Navixy instances.

## Updates for PaaS instances

With the PaaS version, Navixy automatically updates the software on its servers to the latest version with new features, improvements, and fixes. This happens without any action required from the PaaS customers. Therefore, PaaS customers always have access to the latest version of the software without needing to download or install anything themselves.

## Updates for On-Premise instances

Navixy On-Premise instances can be updated by obtaining and installing new distribution packages released regularly. Navixy releases major builds every 2-3 months.

### When it is the proper time to update an on-premise instance

You should consider updating your Navixy instance if you lack necessary functionality available in the current PaaS version, experience technical issues that can be resolved in a newer build, or your software version is outdated. It's not necessary to update with every new build if you're content with the current functionality.

* Getting information about your [Current version](current-version.md)

### Who is eligible for receiving updates

Only Navixy On-premise instances with an active subscription are eligible to receive the distribution package for updating. If an On-premise instance does not have an active subscription or has an overdue payment, the distribution package will not be provided for updating.

### How to receive package for updating

To perform an upgrade for your Navixy On-Premise instance, you need the latest distribution package of the platform. You can contact technical support to get the link to download the latest build. If you are performing the update for the first time or are unsure of the process, it is recommended to inform the support engineer who provided the build and coordinate with them on the time of the update. Although support engineers do not connect to client servers during the upgrade process, they are available to provide necessary advice and emergency help if something goes wrong.

### How do I perform an update?

To perform updates on Navixy platform, first, upload the distribution package received from technical support to your server(s). Then, follow the update instructions for your specific operating system carefully:

* [Linux – Update](../../overview/how-to-guide/installation/update/update-linux/)
* [Windows – Update](../../overview/how-to-guide/installation/update/update-windows/)

It is important to note that updating involves making changes to the database, replacing system files, and restarting services, which may result in users having trouble accessing the platform during that time.

Therefore, it is recommended to make system backups and inform users in advance about the planned update and to perform the update at a time when there is the least user activity, such as at night or on weekends.
