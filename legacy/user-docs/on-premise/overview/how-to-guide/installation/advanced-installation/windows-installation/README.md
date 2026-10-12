---
description: Install Navixy on-premise on Windows Server using automatic or manual deployment. Covers OS version requirements and third-party software coexistence considerations.
---

# Windows

This section describes the step-by-step installation of the Navixy On-premise solution on Windows. Depending on your requirements, you can resort to:

* [Automatic installation](automatic-installation-windows.md) - to perform a standard deployment with less effort.
* [Manual installation](manual-installation-windows.md) - to control each step and get a full understanding of the platform structure.

In both cases, the result will be an installed and operational platform on your server.

## Operation system version

Navixy is cross platform software and has no strict OS version requirements. However, the best solution is to use the latest version of **Windows Server**, as this guarantees better stability and security.

Using the desktop version of Windows is highly not recommended, although it remains compatible. This version lacks certain administrative features, and in addition, the desktop OS typically resides on desktop hardware, which is never reliable for use as a server.

## Third-party software

From the experience of many clients, we know that Windows servers often share their capacity between Navixy platform and other software such as antiviruses, web servers, accounting systems and other service programs. This is not prohibited, but it is important to allocate server resources appropriately and ensure that third-party software does not interfere with Navixy by occupying ports or in any other ways.
