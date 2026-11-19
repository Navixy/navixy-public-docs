---
description: Install Navixy On-Premise on Ubuntu, Debian, AlmaLinux, Rocky Linux, or Red Hat Enterprise Linux with the installation script, then open the Admin Panel.
---

# Linux installation script

This guide describes Navixy On-Premise platform installation using automated scripts. This type of installation is designed for and supported only on these 64-bit Linux distributions:

* Ubuntu 22.04, 24.04, and 26.04
* Debian 12 and 13
* AlmaLinux, Rocky Linux, and Red Hat Enterprise Linux 9 and 10

On AlmaLinux, Rocky Linux, and Red Hat Enterprise Linux, SELinux stays in enforcing mode. If firewalld is active, the script asks whether to open the HTTP and HTTPS ports. Open the ports for your GPS devices yourself. Store your own HTTPS certificates under `/etc`: SELinux doesn't let Nginx read certificates from other folders.

For other Linux distros, only manual installation is available.

For Windows servers, navigate to [the relevant section](../windows-installation/).

## Checking prerequisites

First, you need to have the following before you start the installation:

* A server (or servers) meeting all the [Server hardware](../../../requirements/server-hardware.md) requirements
* Registered domain names that you will use for your Navixy instance
* Navixy software package and License key provided by the Navixy team
* Internet access from the server to the repositories that the script installs software from:
  * `repo.mysql.com`, for MySQL 8.4
  * `packages.adoptium.net`, on Debian 12, for Temurin JDK 25
  * the EPEL and CRB repositories, on AlmaLinux, Rocky Linux, and Red Hat Enterprise Linux. On Red Hat Enterprise Linux, also `dl.fedoraproject.org`.

This automatic process will install all the software prerequisites listed in [Server software](../../../requirements/server-software.md) document meaning that you don't need to install them yourself manually.

## Navixy package

The Navixy On-Premise distribution package is always available for download at the [direct link](https://get.navixy.com/latest). On Linux, you can download it with `wget` using the following command:

```
wget https://get.navixy.com/latest --content-disposition
```

## Wizards for installation and configuration

Script for [automatic initial installation](installation-wizard.md)

Script for [automatic configuration](configuration-wizard.md)

## **Final steps**

When the instalation is done, you can login to the admin panel using domain name you have specified during the installation. Default login is '**admin**' and password is '**admin**'. To make additional settings, you can run the [Configuration wizard](configuration-wizard.md) again and choose other options.

If you have any questions regarding the installation or configuration of the product, please contact Navixy technical support at [support@navixy.com](mailto:support@navixy.com).
