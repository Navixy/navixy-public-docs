---
description: Set up basic backups for Navixy on-premise using MySQL dump scripts and file system snapshots. Always back up the license key file separately from database data.
---

# Backup basics

This page covers most popular backup strategies for Navixy platform. It is assumed that all components are running on one server, and the database is running either on the same server or on the separate one.

The platform consists of the following components, listed by their default install path:

### Application

Backend:

* /home/java/api-server
* /home/java/sms-server
* /home/java/tcp-server

Frontend:

* /var/www/panel-v2
* /var/www/pro-ui

### Database

The following MySQL databases are used by Navixy:

* google
* tracking

If your Navixy platform runs on a VM in a cloud platform, you can make a periodic snapshots of the machine, and additionally perform a MySQL dump. Creating a dump is required to maintain database consistency. You can find more information on MySQL dump here:

[https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)

Both "google" and "tracking" databases should be backed up. You can create MySQL dump on a running database without locking tables and interrupting the service by using `--single-transaction` option.

If you run Navixy on a physical server, you can backup the components only once after installation, and then once after each platform update, so you always have the latest version of the backend and frontend components backed up. Then, you only need to make a periodic database backups using MySQL dump.

Below is an example bash script that creates MySQL dumps of both databases, passes it through gzip to reduce size, then deletes all backups in the backup directory older than 1 year. Feel free to modify the script according to your needs.

{% code overflow="wrap" %}
```bash
#!/bin/bash

bak_dir=/home/navixy-backups
sql_user=root
sql_passwd=rootpasswd
now=$(date +"%Y-%m-%d")

mysqldump -u$sql_user -p$sql_passwd --single-transaction google | gzip > $bak_dir/google-bak-$now.sql.gz
mysqldump -u$sql_user -p$sql_passwd --single-transaction tracking | gzip > $bak_dir/tracking-bak-$now.sql.gz

find $bak_dir -maxdepth 1 -type f \( -name "google-bak-*.sql.gz" -o -name "tracking-bak-*.sql.gz" \) -mtime +365 -delete
```
{% endcode %}

{% hint style="info" %}
You can also use third-party solutions for MySQL backups, for example: [https://www.percona.com/doc/percona-xtrabackup/LATEST/index.html](https://www.percona.com/doc/percona-xtrabackup/LATEST/index.html). It is a proven working solution that gives advanced database backup options. It is successfully tested and used by many On-premise instances system engineers. To find a MySQL backup best practice recommended by Navixy, please refer to [this page](mysql-backup.md).
{% endhint %}

## License key backup

When you planning backups for Navixy platform, an important thing to take into account is the license key. The key (also known as fingerprint) is updated against our license server approximately once a week. That means if you restore a backup which was made before the latest key update, the platform will not work and you will need to contact our tech support to get the new key.

To avoid this, we recommend backing up the license key separately from the main backup. They key can be selected from database using the following SQL query:

```sql
SELECT value FROM google.variables WHERE var='fingerprint';
```

The result is a simple text string which can be saved in a file, or in another database. We recommend backing up the key at least 2-3 times a day.

If you had to restore the platform from backup, you only need to write the key back into database, restart the services and the platform should start working:

```sql
UPDATE google.variables SET value='<your_key>' WHERE var='fingerprint';
```
