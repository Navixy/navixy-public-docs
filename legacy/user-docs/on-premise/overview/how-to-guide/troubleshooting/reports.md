---
description: Diagnose report generation issues on Navixy on-premise. Disk space shortages cause timeout errors, and hardware resources directly affect report generation speed.
---

# Reports

Generating reports is a process that demands substantial resources. It entails gathering diverse information from multiple tables in a database and merging it into a unified document. Let's explore the challenges that can arise when creating reports on an On-Premise instance. These issues primarily stem from the server hardware, yet their detection is often inconspicuous. Therefore, a comprehensive understanding of the underlying causes becomes imperative.

## Reports are not created

This is a quite common problem on Navixy standalone instances which are not properly administered. When you try to generate a report, you see a loading indication, but it takes infinitely long and the report is not actually generated. If you open the Network tab of your browser's developer tools, you can see that the API request for generating the report fails with `504 Gateway Time-out` error.

The reason is insufficient free disk space. If your platform is running on two servers, here we are talking about the application server (where Java services are running).

The platform is configured to suspend resource-intensive processes when the disk is 99% full. In some cases (on instances deployed before 2022), this happens when the disk is over 90% full. This is to preserve basic functionality, avoid a full disk overflow and give you time to solve the problem.

The first thing to do is to make sure that the disk is actually full. If it is not, it could be a platform failure which should be reported to technical support.

Make sure that the disk is the recommended size according to [the system requirements for server hardware](../requirements/server-hardware.md). If the disk space is less than recommended, you should take emergency measures to increase it.

The next step is to find out what caused the disk overflow. If you only have Navixy platform on your server, the free space is usually taken up by the platform logs. The database might also be taking up space if it is located on the same drive - it inevitably grows in size over time. In the latter case, you should act immediately, because the lack of free space for writing to the database can lead to its inoperability and loss of information.

If you have many devices registered on your server (over several thousands), backend service logs can take up a significant amount of space - sometimes a single file can be over 1 GB in size. However, the logs do not grow indefinitely, as their default life cycle is 7 days, after which they are deleted.

As a temporary (and not recommended) solution to the space problem in emergency cases you can delete log files from previous days, since they are not needed for the current functioning of the platform. However, consider that within a week the logs will accumulate again and take up about the same amount of disk space.

## Reports take long time to generate

When faced with this scenario, it becomes crucial to assess the issue by considering both the timeframe and the number of devices for which the report is generated. Naturally, as the number of devices and the duration of time increases, the platform requires additional time and resources to retrieve the requisite data from the database and generate the report.

Possible reasons for slow report generation:

* **Lack of RAM or insufficient disk speed** is the most common reason. With an increase in the number of devices and users, your server may start to lack the resources to work efficiently. Your system administrators need to analyze server performance.
* **High load on the server during working hours**. If your users build a lot of reports simultaneously, or if a lot of data is requested via API, this can affect the overall performance of the platform.
* **Excessive limits for reports.** If you have previously [changed the default limits](../configuration/system-limits.md), the load may increase dramatically.
* **Incorrect MySQL configuration.** If the configuration was changed for any purpose and differs from the standard, the database itself may not work efficiently.
* **Extraneous software on the server or external load on the database.** Sometimes customers use some third-party software (unrelated to Navixy) on the same server as the platform to perform some specific tasks. It even happens that third-party software collects information from the database directly, generating unpredictable loads. This is strongly inadvisable.
* **Platform malfunction.** Unfortunately, software failures sometimes occur, but they are quite rare, so you should first rule out all of the above causes. If you are sure that the slow report generation is caused by a platform failure, report it to technical support with all the information that allowed you to make such a conclusion.
