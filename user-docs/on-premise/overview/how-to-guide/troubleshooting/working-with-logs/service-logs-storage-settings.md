---
description: Configure log storage location, compression archiving, and retention period for Navixy on-premise backend services. Edit logback.xml in each service directory.
---

# Service logs storage settings

By default, log storage is configured as follows:

* Logs are stored in the `/log` subdirectory inside the directory of each of the backend services.
* Logs are stored as plain text files.
* Logs are stored for one week, then they are rotated and the old ones are deleted.

All of three backend Java services have their own logs. The above parameters can be changed if necessary. They are set in the **logback.xml** file of each service. You can find this file in a `conf` directories of services:

* Linux: `/home/java/{service-name}/conf/logback.xml`
* Windows: `C:\java\{service-name}\conf\logback.xml`

Since such file is contained in each of the service directories, the settings below must be applied separately for each of the files.

{% hint style="info" %}
The settings can be applied individually or in any combination. Select the desired settings depending on your purposes.
{% endhint %}

## Logs location

By default, logs are stored in the services folders in the log subdirectory, but for whatever reason you may want to store them separately in a custom directory.

To configure this, open the **logback.xml** file and find the following lines:

```
<file>log/log.txt</file>

<fileNamePattern>log/log.%d{yyyy-MM-dd}.log</fileNamePattern>
```

Change the `log/` entry to the absolute path to your custom logs directory.

For example, if the directory is `/my/directory/for/logs/`, then the lines must look this way:

```
<file>/my/directory/for/logs/log.txt</file>

<fileNamePattern>/my/directory/for/logs/log.%d{yyyy-MM-dd}.log</fileNamePattern>
```

Save the file and restart the platform to apply changes. From now on, the logs will be recorded to the custom directory.

### Symbolic links

On Linux systems, there is an alternative way to store logs in a custom directory. Instead of changing the service configuration, you can create symbolic links to the desired custom directories. This is how it is done:

1. Stop Navixy services.
2. Create new subdirectories in your new custom directory (e.g. `/my/directory/for/logs/`):

```
mkdir -p /my/directory/for/logs/api-server/log
mkdir -p /my/directory/for/logs/sms-server/log
mkdir -p /my/directory/for/logs/tcp-server/log
```

3. Move existing service logs to your new directories:

```
mv /home/java/api-server/log/* /my/directory/for/logs/api-server/log/
mv /home/java/sms-server/log/* /my/directory/for/logs/sms-server/log/
mv /home/java/tcp-server/log/* /my/directory/for/logs/tcp-server/log/
```

4. Delete old directories:

```
rmdir /home/java/api-server/log
rmdir /home/java/sms-server/log
rmdir /home/java/tcp-server/log
```

5. Create symbolic links:

```
ln -s /my/directory/for/logs/api-server/log /home/java/api-server/log
ln -s /my/directory/for/logs/sms-server/log /home/java/sms-server/log
ln -s /my/directory/for/logs/tcp-server/log /home/java/tcp-server/log
```

6. Restart the services.

After this is done, the logs will be recorded to your new custom directories.

## Logs archiving

On instances with a large number of devices (when there are more than several thousand of them), backend services logs can occupy a significant amount of disk space. Archiving logs from previous days can be a great solution for saving disk space.

Find the following line in **logback.xml** file:

```
<fileNamePattern>log/log.%d{yyyy-MM-dd}.log</fileNamePattern>
```

Add `.gz` after `log`, so the line looks like this:

```
<fileNamePattern>log/log.%d{yyyy-MM-dd}.log.gz</fileNamePattern>
```

Save the file and restart the platform to apply changes. Now, the log files for previous days will be compressed to `.gz` archives, consuming far less disk space than plain text files. However, the current day's `log.txt` file will still be uncompressed since it keeps being filled by the platform.

## Logs life span

The default life span of logs is 7 days. In most cases this is sufficient for any troubleshooting. However, you may want to keep logs for a longer period or, alternatively, shorten it. Remember: the longer logs are stored, the more disk space they consume, so handle this value with care.

To change logs ife span, find he following line in **logback.xml** file:

```
<maxHistory>7</maxHistory>
```

Change `7` to any desired value (in days).

Save the file and restart the platform to apply changes.
