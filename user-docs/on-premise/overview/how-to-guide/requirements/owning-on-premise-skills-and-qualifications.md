---
description: "Review the IT skills required to operate Navixy on-premise: server administration, networking, Linux or Windows management, and database operations."
---

# Owning On-Premise: skills and qualifications

## Owning On-Premise: skills and qualifications

Since the Navixy On-premise solution is hosted by customers, they are expected to be highly responsible and professional during the exploitation of the platform. Unlike the PaaS version, where all system administration is done on the Navixy side, in case of On-premise it all lies on the customers' shoulders. Like any product designed to be constantly available to end users, Navixy On-premise cannot exist just "as is" and requires 24/7 monitoring and maintenance. Only in this case will the platform be accessible and user-friendly, and your business will prosper.

Below we'll look at the core skills needed to keep the Navixy On-premise platform running efficiently, stably, and for a long time.

## **Server**

Since Navixy On-premise is a server-based solution, providing a properly functioning server (or servers) in the long run is the key to stable and uninterrupted operation of the platform. Therefore, knowledge of the following areas is crucial:

* **Data center administration**.
  * **Knowledge of cloud platforms** (AWS, Azure, etc.) - required in case of Navixy cloud deployment. Understanding the types of instances, their configurations and capabilities.
  * **Server hardware knowledge** - required if Navixy is hosted in a local physical data center.
* **Virtualization**. It is recommended to host Navixy on a virtual machine for ease and flexibility of future maintenance.
* **Server resource management**.
  * **Disks**. Understanding RAID, types and parameters of disks, allocating additional disk space for a growing database.
  * **RAM**. Increasing the amount of memory with growing platform demands.
  * **CPU**. Primary configuration and provisioning of additional processor capability.
* **Backup and replication**. Critical to keeping your data safe and protecting your system from failure. A good practice is to combine these processes.
  * **Backup**. A set of procedures for copying physical and virtual data for use in case of problems with the original.
  * **Replication**. Creating a copy of a virtual machine and then keeping the replica in sync with the original machine.

## **Network**

Navixy is unimaginable without a network connection. The platform functions as a website, so users access the platform via the Internet. Devices send telematics data over the network. Mobile applications require a stable connection to your server. License verification is done by connecting to the authentication server.

The On-premise platform owner is required to:

* Provide availability of ports required for the system to function.
* Set up a firewall and other protection systems for normal traffic passage, eliminating the possibility of losing useful data packets.
* Provide normal channel bandwidth, excluding delays at network level.
* Properly configure addressing and DNS records.
* In the case of multiple servers - provide exceptional channel capacity between servers for stable work with the database.

## **Operation system**

Navixy platform, being software, is installed on the server operating system. Therefore, the server owner (administrator) is required to be skilled and experienced in working with this OS.

In most cases Navixy is installed on Linux (Ubuntu or Debian) and we strongly recommend doing so because the support software is primarily designed for Linux, all automation scripts are written for it, and it is more flexible for further administration. However, some customers have various reasons to use Windows, so we preserve the possibility to run the platform on this OS.

In general, the following is required from the server administrator in terms of OS:

* Understanding of the system architecture, knowledge of directories structure.
* Proficiency with bash/CLI.
* Installing programs, working with repositories.
* System resource management.
* Processes and services management.
* System logs searching and reading.

## **Software**

Although our instructions primarily cover the features of the Navixy platform, the normal operation of the platform is inconceivable without additional software.

First of all, we are talking about the software which is directly necessary for the operation of the platform. The owner of the server must have sufficient expertise to work with this software.

* **Java**. The setup process is disclosed in the installation instructions. All that is needed is to have the right version.
* **Nginx**. Typical configurations are given on our website. However, you can use your own configurations in unusual usage scenarios, such as when the server hosts two websites (not recommended).
* **MySQL**. For proper database operation, overall platform performance and even data security, proper DBMS configuration is crucial. The configuration parameters generally recommended are given in the installation instructions. However, fine-tuning for best performance is always up to the customer, as it strongly depends on specific server parameters and hosting peculiarities.

In addition, there is a vast amount of auxiliary software which can be useful to system administrators. You are free to use any desired software on the server as long as it does not interfere with the normal operation of the platform. For example:

* File managers.
* Archivers.
* Text editors.
* Monitoring tools.
* Any other utilities to make your work easier.

## **Database**

Probably one of the most important skills of a Navixy On-premise owner is the ability to work with the database. Since the database is a storage of all the information on the platform, the importance of its proper administration can hardly be overestimated. And the loss of crucial information, especially in large volumes, can be devastating to your business.

#### **Backup and replication**

Simply writing it to disk is not enough to ensure the safety of your information. It is also necessary to provide for situations when this disk or the entire server for some reason is damaged or becomes unavailable. Here, as with the server, **backup** and **replication** come to the rescue, and the quality of these processes directly affects the safety of data. For small servers is usually enough to just save the DB dumps, but for a large high-loaded database, it is better to set up replication, so that in the case of problems there was a ready-to-use copy. Combining these two methods together with a highly available server gives the best results in terms of fault tolerance. If the server owner has the necessary skills to implement such a scheme, his business is insured against data loss.

#### **SQL experience**

Since Navixy platform works with MySQL database, the server owner must have experience with SQL, as well as an understanding of SQL query structure and syntax. Although most of the necessary queries are described in the instructions, applying them thoughtlessly can be futile and even harmful to data. For this reason, knowledge of SQL is undeniably important, and having someone with DBA skills on your team would be a significant advantage.

In conclusion, a comprehensive set of technical skills is necessary for maintaining and supporting infrastructure in today's rapidly evolving technological landscape. Proficiency in operational systems, databases, connectivity, web services, security, and other relevant areas ensures that IT professionals can effectively manage and troubleshoot various components within standalone infrastructure. Furthermore, possessing a diverse skillset allows customer IT professionals to collaborate with Navixy Support Team in a more seamless and efficient way, fixing issues quicker and reducing possible risks of failure.
