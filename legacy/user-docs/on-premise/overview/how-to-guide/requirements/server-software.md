---
description: "Check which operating system and which Java, MySQL, and Nginx versions a server needs before you install Navixy On-Premise."
tags:
  - legacy
---

# Server software

## Operating systems

Navixy's backend services are developed using Java programming language, while the frontend is built with pure Javascript. As a result, the software is cross-platform compatible, which means it can run on various operating systems. However, for optimal performance and compatibility, we recommend using the following operating systems:

* Linux, 64 bit:
  * Ubuntu 22.04, 24.04, or 26.04
  * Debian 12 or 13
  * AlmaLinux, Rocky Linux, or Red Hat Enterprise Linux 9 or 10
* Windows Server 2016 or newer, 64 bit

These operating systems have been extensively tested and optimized for use with Navixy, ensuring a seamless user experience and minimal technical issues.

## Environment requirements

Additionally, to install and run Navixy On-Premise edition, you will require the following environment. It is all provided under GNU or similar licenses, and there is no need to spend any money on it.

* ﻿[**Java SE Development Kit (JDK) 25**](https://www.oracle.com/java/technologies/downloads/#java25) by Oracle, or OpenJDK 25 from the distribution repositories. The package is `openjdk-25-jdk` on Ubuntu and Debian 13, and `java-25-openjdk-devel` on AlmaLinux, Rocky Linux, and Red Hat Enterprise Linux. On Debian 12, which has no OpenJDK 25 package, the installation script offers Temurin JDK 25. All the other JDK 25-based distributions are also compatible.
* [**MySQL Server 8.4**](https://dev.mysql.com/downloads/mysql/8.4.html)**.** Existing installations can keep MySQL 8.0. The platform doesn't support other DBMS, including PostgreSQL and MariaDB.
* [**NGINX**](https://nginx.org/en/download.html) of any actual version - **1.2** or newer (If you wish to use image previews in task forms, nginx must have [image filter](http://nginx.org/en/docs/http/ngx_http_image_filter_module.html) module)

{% hint style="info" %}
Automatic platform installation scripts for Linux install the above software if it is not found on the server.
{% endhint %}

* Servers (except MySQL server) must have access to the Internet and have static IP address.
* Installation process requires root access (Unix systems) or administrative user (Windows). During the installation of databases engines you need to be granted with MySQL's root access.
* For HTTP services we recommend to use domain names, e.g. for API – api.domain.tld, for User interface – my.domain.tld and panel.domain.tld for Admin panel. TLD means any top level domain (com, net, edu, etc).
* High-speed and reliable internet connection at least 10 Mbit/s
* Monitoring system that you like. It is not required but recommended
* Recommended filesystem is ext4
* For sending email from localhost you should have got an configured MTA (Mail transfer agent), e.g. Postfix
  * It is also possible to send email through another services, e.g. gmail, but in that case email "from" field substitution won't work
