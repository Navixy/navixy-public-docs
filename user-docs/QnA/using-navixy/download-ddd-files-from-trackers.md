---
title: Download DDD files from trackers
description: "Download tachograph DDD files from supported trackers using Navixy Tacho Auth Client; vehicle ignition and active company card are required"
---

# Download DDD files from trackers

## Question

How do I download DDD files from trackers with Navixy?

## Answer

DDD files are tachograph data files used in commercial transport, mainly in the EU. DDD is a standard binary format for downloading data from a digital tachograph. With Navixy, files can be requested for sending to a list of emails specified in the **DDD file recipients** block.

![](<../.gitbook/assets/Unknown image (35)>)

It's important to understand that the platform doesn't process DDD files in any way (no reading or changing the data). The platform just sends DDD files via email to the list of recipients specified in the user account. This is the only available functionality related to DDD files on the platform.

To download a DDD file, the following conditions must be met:

* Vehicle ignition must be ON.
* The company card is inserted into the card reader.
* An EU, MENA, or US server is set in the Tacho Auth Client app configs.
* The Tacho Auth Client application must be running on the same computer where the card reader is connected. The app status should be Connected.

The procedure can take up to 15 minutes.

If you don't have the Tacho Auth Client application, feel free to reach out to our support team.

Once you receive the Tacho Auth Client application, you need to unpack the ZIP archive on the same PC where the card reader with the company card is connected. Also, you need to install Java (17+) before running the app. Once you start the application, a log file is created. The log file may be helpful if any issue occurs with the app. If you face an issue related to the application, please feel free to reach out to our support team.

Navixy supports the following device models for DDD file downloading:

* Galileosky v5.0
* BCE FM Tacho
* BCE FMS500 Tacho
* Teltonika FMB630
* Teltonika FMB640
* Teltonika FMC640
* Teltonika FM6300
* Teltonika FM6320

If you use Teltonika, make sure that WEB Tacho Settings Status is disabled:

![](<../.gitbook/assets/Unknown image (36)>)

If the customer has scheduled DDD file downloads, they need to insert the card each time a DDD file request is made to the tachograph.

Before starting the application, open the `config.properties` file in a text editor. Once opened, make sure that the `server.hostname` parameter points to the platform tracking address.

![](<../.gitbook/assets/Unknown image (37)>)

A tracking server address can be clarified on the device list page. Select a device, and you will find a server address corresponding to your instance (EU, US, MENA).

Once the server address is set (it can be a domain or an IP), you can start the application.

If you use Windows, right-click the `start.bat` file and select "Run as administrator":

![](<../.gitbook/assets/Unknown image (38)>)

Once you have successfully run the application and all conditions above are met, you can proceed to the user interface and request DDD files to be sent via email to the recipient list.

## Links

* [Tachograph DDD files block](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/tachograph-ddd-files-block)
* [Device list](https://www.navixy.com/devices/)
