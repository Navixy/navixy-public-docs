---
description: Diagnose devices going offline on Navixy on-premise. Covers TCP-server failures, network issues, mobile connectivity problems, and data processing troubleshooting.
---

# Device operation and connection

Data acquisition from devices is integral to the functioning of Navixy, a GPS tracking and telematics platform. Consequently, encountering multiple devices unexpectedly going offline or failing to update their status poses a critical issue that necessitates an immediate resolution.

## All devices are offline

If you observe that all devices on your server have gone _**offline**_ without any data being received, it indicates critical issues hindering data reception. Immediate attention is imperative to address these problems. There are three potential types of problems that might be encountered:

1. **TCP-server is down**. [Check its status](checking-service-statuses.md). If it is actually down - try [restarting it](../maintenance/restarting-instance.md).

* If it fails to start or crashes after a short while - [check its log](working-with-logs/) to find any related errors.

2. **Server network problems.**

* Check whether network is actually working. If you find any problems, contact your ISP.
* Check that the ports required for the device are available. They may be closed either on the server side (when TCP-server failed to initialize them) or within your infrastructure (blocked by firewall).
* Check that your server domain name is registered and available. If anything is wrong, contact your DNS hosting provider.

3. **Mobile network issue.**

* If the services are up and running and there are no problems at the network level, there may be an issue with your mobile provider. This is particularly relevant when all devices use SIM cards from the same carrier.

## All devices are “not updated”

Occasionally it may happen that all devices have the _GPS not updated_ status instead of _Online_ (while some devices may be offline).

In this case it is necessary to check the system clock of the server - probably it is slow or fast for a significant amount of time. Thus, for the server all devices are in the past or in the future, and despite correct data, they cannot be displayed _Online_.

## Some devices are offline

If you notice that **some** of the devices are offline, this narrows down the search for the problem. The initial step is to verify if the offline devices share the same model or manufacturer. For instance, while devices from one manufacturer function properly, devices from another manufacturer are offline and fail to transmit any data.

**Devices belong to the same model or manufacturer**.

1. Check whether [port(s) used for these devices](https://www.navixy.com/devices/) is available (use _telnet_ command to find it out).
2. Check mobile network. It happens quite often that there are mass problems with mobile operators, leaving a large number of devices unconnected. Additionally, server address and port may not be available from the mobile network. Try installing a SIM card of another carrier and see if it helps.
3. Check device firmware. Contact the manufacturer to find out if there were any firmware bugs or faulty upgrades.

**Devices have different models and manufacturers**.

1. Check mobile network. If all the problematic devices have SIM cards of the same carrier, this is the most likely issue. Try installing another SIM card and see if it helps.
2. Check the port availability. It happens that different device manufacturers share the same protocol, and therefore the same port is used.

## Data processing issues

In rare cases, there may be problems with initial integration of a device, or the newest firmware may use an updated and modified protocol that is not yet supported by the platform.

If you suspect that some data from devices is not supported or parsed by the system, do the following.

1. Check whether other devices of the same model have the problem. If not, check the difference in firmware.
2. [Check TCP-server logs](working-with-logs/) for errors related to the problematic device. Use device IMEI to search for related entries.
3. Collect several incoming raw data packets from the Air console.
4. Enable and collect [extended logs](working-with-logs/extended-logging.md) for the device.

Share all your findings with Navixy technical support team.
