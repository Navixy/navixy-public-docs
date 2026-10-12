---
description: Manually update Navixy on-premise on Windows. Apply database changes, replace Java service files, update web services, and reconfigure services for Java 21.
---

# Manual update - Windows

This page describes the process of manually updating the Navixy On-premise platform on Windows. Use this instruction if you need to have full control over the update process or if you are using custom service paths. Otherwise, in most cases it is recommended to resort to [the automatic update](automatic-update-windows.md).

In general, the Navixy platform update consists of three parts:

1. Updating the database.
2. Updating java services files.
3. Updating web services files.

Before installing, please make sure that your system meets the following prerequisites:

1. **Java Development Kit 21**\
   Starting from March 2025, the platform deprecated version 17 and older.
2. **MySQL 8.0**\
   Starting from March 2024, the platform deprecated MySQL 5.7. This version reached its EOL and is no longer supported.

{% hint style="danger" %}
Non-compliance with the required software will result in the new version of the platform being unable to start. However, a preliminary update of prerequisites will keep the platform functional.
{% endhint %}

The update will not go through on lower versions of MySQL and will end with an error.

## Beginning of the update

Extract a platform distribution package received from Navixy, typically it is a `.tar.gz` file. You can use any archiver capable of working with tar.gz files, for example [https://www.7-zip.org/](https://www.7-zip.org/). Inside the unpacked archive you will find a "navixy-package" directory, containing all the platform files in it. You can move it anywhere else for the installation paths to be shorter. Hereinafter this will be the main directory of the distribution. Before the update, it is strongly recommended to [stop Navixy java services](../../../maintenance/restarting-instance.md) in "Services" menu. For Windows on-premise instances, there is no automated update scripts. All the update is performed manually.

## Update process

### Step 1: Database update

Open the command prompt and navigate to the folder where you extracted the Navixy distribution package. For example, if you extracted the package to your Downloads folder, the command to navigate to the package's db directory might look like this:

```
cd C:\Users\Administrator\Downloads\navixy-package\db
```

Execute the **updates.sql** file with the following command:

```
mysql -uroot -p$ROOTPASSWORD google < updates.sql
```

(where **$ROOTPASSWORD** is MySQL root password)

Delete updates.sql and google.sql from db folder. It must be done not to override the database on the next step.

```
del updates.sql
del google.sql
```

Make sure these files are deleted and then execute all the other sql files:

```
type *.sql | mysql -uroot -p$ROOTPASSWORD google
```

### Step 2: Java services update

Updating the Java services involves replacing the files in the service directories located at C:\java, specifically api-server, sms-server, and tcp-server.

To update these services, locate their corresponding directories in the navixy-package and replace all the files in the conf folder except **config.properties** and **db.properties**.

Before replacing the **config.properties** files, compare the existing ones with those from the new distributive to ensure that any new parameters are added to the existing configuration.

### Step 3: Web services update

Proceed to _C:\nginx\www_ directory. Replace all files in _**panel-v2**_ and _**pro-ui**_ directories with files from the corresponding directories of the distribution package. This will not corrupt any settings, as the configuration files in the package are named as _example_, and will not overwrite the existing ones.

To ensure proper configuration, compare the following files with the examples in the distribution package:

* panel-v2\\**Config.js**
* pro-ui\\**PConfig.js**
* pro-ui\static\\**app\_config.js**

If there are any new parameters in the examples, add them to the corresponding files in your Navixy installation.

## Windows services configuration

f you are upgrading to Java 21 from a previous version, you will need to reinstall Navixy services as the wrapper app from the previous version will not work with Java 21. To do this, take the Wrapper folder from the installation package and follow the same steps as for the initial installation. Read more: [Install on Windows.](../../advanced-installation/windows-installation/)

The batch file will update the settings of the existing services, so there is no need to delete them beforehand.

## Final steps

To complete the update process, restart the Navixy Java services in the "Services" menu. Make sure that the services are restarted successfully and are running for at least a minute.
