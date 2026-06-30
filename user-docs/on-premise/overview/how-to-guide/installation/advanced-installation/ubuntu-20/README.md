---
description: Install Navixy on-premise on Ubuntu 20, 22, or 24 using an automated script. Complete the installation and configuration wizards, then log in to the admin panel.
---

# Ubuntu 20+

This guide describes Navixy On-Premise platform installation using automated scripts. This type of installation is designed for and supported only on **Ubuntu 20, 22 and 24**.

For other Linux distros, only manual installation is available.

For Windows servers, navigate to [the relevant section](../windows-installation/).

## Checking prerequisites

First, you need to have the following before you start the installation:

* A server (or servers) meeting all the [Server hardware](../../../requirements/server-hardware.md) requirements
* Registered domain names that you will use for your Navixy instance
* Navixy software package and License key provided by the Navixy team

This automatic process will install all the software prerequisites listed in [Server software](../../../requirements/server-software.md) document meaning that you don't need to install them yourself manually.

## Navixy package

Navixy On-premise distribution package is always available for download at the [direct link](https://get.navixy.com/latest). On Ubuntu, you can download it with `wget` using the following command:

```
wget https://get.navixy.com/latest --content-disposition
```

## Wizards for installation and configuration

Script for [automatic initial installation](installation-wizard.md)

Script for [automatic configuration](configuration-wizard.md)

## **Final steps**

When the instalation is done, you can login to the admin panel using domain name you have specified during the installation. Default login is '**admin**' and password is '**admin**'. To make additional settings, you can run the [Configuration wizard](configuration-wizard.md) again and choose other options.

If you have any questions regarding the installation or configuration of the product, please contact Navixy technical support at [support@navixy.com](mailto:support@navixy.com).
