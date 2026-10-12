---
description: Locate and analyze Navixy on-premise backend service logs for API-server, SMS-server, and TCP-server. Use command-line tools to search and filter log entries.
---

# Working with logs

Backend services of Navixy platform always collect logs when running. These logs contain entries on all actions performed by Java-services, as well as errors that occur during the platform operation. Log analysis is an integral part of troubleshooting process.

## Logs location

All of three backend Java services have their own logs. The default directories are the following:

| **Service**    | **Linux**                 | **Windows**            |
| -------------- | ------------------------- | ---------------------- |
| **API-server** | /home/java/api-server/log | C:\java\api-server\log |
| **SMS-server** | /home/java/sms-server/log | C:\java\sms-server\log |
| **TCP-server** | /home/java/tcp-server/log | C:\java\tcp-server\log |

{% hint style="info" %}
&#x20;The above directories are default, but on some servers logs can be stored in a custom directory at the request of the instance owner. For more details, check the [Service logs storage settings](service-logs-storage-settings.md) page.
{% endhint %}

## Logs contents

**API-server logs** are the first to analyze when any of the problems related to the operation of the platform as a whole and individual functions occur. If you see that any API request is not executed (during API integrations or in browser dev tools), search the log by the header or content of this request. If there are problems affecting a specific device or group of devices, search by IMEI or ID of that device.

**SMS-server logs** must be checked when any messaging troubles are encountered. This relies both to SMS and email messaging (regardless of the service name). Search for problems by phone number or e-mail.

**TCP-server logs** contain all the information related to devices operation, network connection and license checks. Here you can make search by device IMEI, network address or any other parameters related to the problem.

{% hint style="info" %}
If you know the circumstances under which the problem occurs, a good tactic is to trigger it and check what entries appear in the logs. This way you will "catch" the error at the moment it occurs.
{% endhint %}

## Useful tools

#### Linux

You can search for specific values in the logs with **grep** command. In this way, you will see the log entry(s) containing the value you are looking for. Example:

```
grep "12345678910" log.txt
```

To view new log entries in real time, use **tail** command. This is especially useful when you have the opportunity to trigger problems.

```
tail -f log.txt
```

To view the entire log, use any text editor you prefer, such as **nano** or **vim**.

#### Windows

The standard Notepad in Windows is incapable of handling large text files, so don't try to use it to view logs - it will just get stuck. Instead, use any third-party advanced text editor.

The most popular editors for Windows are:

* [**Notepad++**](https://notepad-plus-plus.org/)
* [**Sublime Text**](https://www.sublimetext.com/)

These editors have advanced search capabilities and can handle large file sizes, making them very useful when working with logs on large instances.
