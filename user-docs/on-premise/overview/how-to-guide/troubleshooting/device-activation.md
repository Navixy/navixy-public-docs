---
description: Troubleshoot device activation failures on Navixy on-premise. Check SMS gateway configuration, backend service status, network settings, and platform version compatibility.
---

# Device activation

Activating Navixy on-premise devices follows the same process as the PaaS version, with one exception: activation occurs on your server, and data exchange and processing occur within your infrastructure. Ensuring seamless functionality while maintaining data security, this distinction offers you complete control and greater flexibility.

Hence, the fundamental troubleshooting steps remain consistent, and you can locate them on our Expert Center page.

[Possible issues during the device activation](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/gps-devices/add-and-manage-devices/troubleshoot-device-activation)

If you encounter activation issues, we advise referring to this comprehensive manual for troubleshooting guidance. It covers the most prevalent problems and their corresponding solutions.

Furthermore, specific challenges may arise with On-premise instances due to the unique aspects involved in the installation and operation of the platform on dedicated servers. In the following sections, we will delve into these issues and explore effective resolutions for each.

## SMS gateway

The On-premise platform lacks a pre-installed SMS gateway, even for trial purposes. Thus, it is necessary to have an active SMS gateway configured to enable automatic activation. Without it, only manual activation is possible.

In the event that you are attempting to auto-activate the device on a newly installed platform or if you have never configured an SMS gateway, the activation process will not take place. In such a scenario, you have two options: either configure the SMS gateway or activate the device manually.

If you have an SMS gateway and it was previously working, check its operation according to this page:

[On-Premise SMS gateway troubleshooting](on-premise-sms-gateway-troubleshooting.md)

## Backend services

Two backend Java services are involved in the device activation process: SMS-server and TCP-server. First of all, it is necessary to make sure that the services are up and running.

[Checking service statuses](checking-service-statuses.md)

When you have made sure that the services are operational, you can address the problem more precisely.

#### SMS-server

All you need to check for this service is its logs. There you need to look for entries containing the phone number of the device. If there are any problems on the part of SMS-server, you will see entries starting with `WARN` or `ERROR`. If contents of these entries do not reveal the essence of the problem, redirect them to technical support to receive further assistance.

#### TCP-server

Again, examine the service logs for entries starting with `WARN` or `ERROR`, but this time use device IMEI for searching. If you see any data processing errors in the records, report it to technical support.

Furthermore, TCP server initializes ports for connecting devices at startup. If some port could not be initialized, connection to it and device activation will be unavailable. A record of this problem will also be present in the log. The number of the required port can be found in [device description](https://www.navixy.com/devices/).

## Network

Activation is performed on a server located in a separate infrastructure, which is usually protected by a firewall and other security systems. Although security is crucial, it can sometimes prevent data from being received.

Make sure that the port for your device is available from the outside and that two-way communication with the server is allowed. Tools to help you make sure of this:

* _telnet_ - basic tool for checking port availability.
* _tcpdump_ - data analyzer useful to check whether data packets are coming to the server.

## Platform version

As the Navixy On-premise platform is only updated upon request, previously installed versions gradually become outdated. These outdated versions can pose various issues concerning device operation, and may not support activation of new models. Consequently, it becomes increasingly difficult to ensure optimal functionality and compatibility.

Check your platform version according to instruction on this page:

[Current version](../../../qa/software-updates/current-version.md)

If it is out of date, contact Navixy tech support to request a new build.
