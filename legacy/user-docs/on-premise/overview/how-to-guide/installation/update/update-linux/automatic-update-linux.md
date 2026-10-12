---
description: Run the update.sh script to update Navixy on-premise on Linux. The script checks prerequisites, updates the database and Java services, then restarts all components.
---

# Automatic Update - Linux

The Navixy platform update process typically consists of three parts: updating the database, updating Java service files, and updating web service files. It is important to follow the update instruction carefully and make sure that each part of the update is completed successfully, and no errors are encountered along the way.

{% hint style="danger" %}
If any errors or other difficulties occur during the update process, please contact [Navixy technical support](mailto:support@navixy.com) immediately. Started and incomplete upgrades may cause the platform to malfunction or become unavailable.
{% endhint %}

## Checking prerequisites

Before installing, please make sure that your system meets the following prerequisites:

1. **Java Development Kit 21**\
   Starting from March 2025, the platform deprecated version 17 and older.
2. **MySQL 8.0**\
   Starting from March 2024, the platform deprecated MySQL 5.7. This version reached its EOL and is no longer supported.

{% hint style="info" %}
Non-compliance with the required software will result in the new version of the platform being unable to start. However, a preliminary update of prerequisites will keep the platform functional.
{% endhint %}

## Beginning of the update

Extract a platform distribution package received from Navixy, typically it is a `.tar.gz` file.

```
tar -zxvf $PACKAGENAME
```

_(where `$PACKAGENAME` is the name of `tar.gz` file)_

It will be extracted to `/navixy-package` directory, containing all the platform files in it. Hereinafter this will be the main directory of the distribution.

## Automated update

For instances hosted on Linux servers, an automated update solution is available. It is strongly recommended to use the `update.sh` script. It performs a step-by-step update of the database and application files, and you do not need to make any internal operations manually.

Run the `update.sh` script from the `/navixy-package` directory. If your platform is hosted on two servers, run the script on the application server (where Java services run).

```
root@server:/home/navixy-package# ./update.sh
```

The script will start with a database update. If the database is on a separate server, the script will take the connection data from the Java services' configuration.

After the database update (which may take a while), the script will update the platform system files.

{% hint style="info" %}
If your instance hasn't been updated in a while, you may see the following message during the update: `It seems Navixy services is not under systemd control. Do you want to create systemd services (runit services will be removed)? (y/n)` It is recommended that you answer affirmatively. The point is that we deprecated **runit** method of starting services, and now use **systemd** for this purpose. The script will do everything for you. However, if you answer no for some reason, it will not crash the system, and runit will keep working. You can switch to systemd on the next update.
{% endhint %}

#### Database update (optional)

You can perform database upgdate separately from the rest of the platform if needed. To do this, run the `update-db.sh` script from the `/navixy-package` directory. This can be done either on the database server (localhost) or from another server, specifying the host address. After running the script, you will see the following dialog:

```
Enter mysql host [localhost]:
Enter mysql port [3306]:
Enter mysql user name [root]:
Updating Navixy db..
Enter password:
```

Default parameters are shown in square brackets. If they are the same as the actual ones (update is made within database server), you do not need to input anything - just press Enter. If you want to specify a different host, a custom port, or a different user, fill the appropriate parameters.

{% hint style="danger" %}
If anything is not working properly after the update, try restarting the platform services using `restart-navixy` script. Additionally, clear your browser data, or check the issues in incognito mode.
{% endhint %}

***

## Manual update

{% hint style="info" %}
Automated update of a platform deployed on Linux is a proven working solution and it is highly recommended to use it. Therefore, the following information is for your information only, and for cases of special non-standard installations.
{% endhint %}

#### Database update

Open `navixy-package/db` directory and execute `updates.sql` file with the following command:

```
mysql -uroot -p$ROOTPASSWORD google < updates.sql
```

(where $ROOTPASSWORD is MySQL root password)

Delete `updates.sql` and `google.sql` from db folder.

{% hint style="warning" %}
**This must be done** not to override the database on the next step.
{% endhint %}

```
rm updates.sql
rm google.sql
```

Make sure these files are removed, and then execute all the other sql files.

```
cat *.sql | mysql -uroot -p$ROOTPASSWORD google
```

#### **Java services update**

Updating the Java services simply means replacing the files in the service directories under `/home/java`. These directories are:

* `api-server`
* `sms-server`
* `tcp-server`

Find the corresponding directories in `navixy-package`. You need to replace all the files except `config.properties` and `db.properties` in the _conf_ folders. Compare the existing `config.properties` files with the ones from new distributive. If you see any new parameters - add them to the existing config.

#### Web services update

Proceed to _/var/www_ directory. Replace all files in _**panel-v2**_ and _**pro-ui**_ directories with files from the corresponding directories of the distribution package. This will not corrupt any settings, as the configuration files in the package are named as _example_, and will not overwrite the existing ones.

Compare these files:

* panel-v2/**Config.js**,
* pro-ui/**PConfig.js**
* pro-ui/static/**app\_config.js**

with the examples in distribution package.

If you see any new parameters, add them.

## Final steps

Restart Navixy java services. Typically this is done by this command:

```
restart-navixy
```

Verify that the services have restarted successfully and are running for at least one minute. This indicates that the update process is complete.
