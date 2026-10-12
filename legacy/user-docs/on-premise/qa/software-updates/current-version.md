---
description: Find the Navixy on-premise platform version number in the VERSION file on your server. Understand the major and minor build numbering scheme.
---

# Current version

The information about the Navixy platform version deployed on your On-Premise instance can be found in the system files of the backend services.

To locate the version, navigate to the directory of any Java-service and look for the file named **VERSION**. Typically these folders are:

- Linux: `/home/java/api-server/VERSION`
- Windows: `C:\java\api-server\VERSION`

The VERSION file is a plain text file that can be opened and read with any text editor. In the file, you will find technical information, and at the end, there will be a line indicating the platform version. The line contains the following details:

**navixy-standalone-provider.2023.02.166**

- navixy-standalone – indicates that this is the On-Premise instance;
- provider or enterprise – package type;
- 2023 – the year of release;
- 02 – the major build version, which typically corresponds to the release month
- 166 - the minor build version, numbered consecutively

Major versions of the platform are released every 2-3 months and contain significant innovations and new features.

Minor versions can be released multiple times per month, and include small improvements and bug fixes that do not require significant changes to the platform's code.