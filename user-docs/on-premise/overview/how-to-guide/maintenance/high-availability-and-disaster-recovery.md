---
description: Set up high availability and disaster recovery for Navixy on-premise with distributed data centers. Covers licensing and ongoing monitoring requirements.
---

# High availability and Disaster Recovery

As your business grows and the amount of data stored gradually increases, the issue of preserving information and providing uninterrupted user access becomes crucial. A basic practice to maintain data safety and integrity is the performance of regular backups. However, to achieve optimal fault tolerance, it is advisable to develop a high availability and disaster recovery strategy.

High Availability (HA) and Disaster Recovery (DR) are two crucial concepts in the field of IT services, both aimed at minimizing downtime and data loss.

1. **High Availability (HA)**: This refers to the ability of a system or component to remain operational for a long period of time. The goal is to minimize downtime, ensuring that the system is available when needed. This is often achieved through redundancy and failover mechanisms. For instance, if one server fails, the system automatically switches to a backup server. The degree of availability is typically expressed as a percentage, with high availability systems often striving for 99.99% or higher (also known as "four nines" or "five nines" availability). High availability is important for IT services because it ensures that critical applications and data are accessible when users need them, which is essential for maintaining business operations and productivity.
2. **Disaster Recovery (DR)**: This is a set of policies, tools, and procedures that enable the recovery or continuation of vital technology infrastructure and systems following a natural or human-induced disaster. The goal is to minimize the impact of a disaster so that an organization can continue to operate or quickly resume mission-critical functions. A good DR plan includes regular backups, data replication, and a detailed recovery process. Disaster recovery is important because it helps organizations protect their data and IT infrastructure from the effects of major incidents, ensuring business continuity and minimizing the risk of data loss.

In summary, both high availability and disaster recovery are important for IT services to ensure that systems are always available when needed, and that data and operations are protected from disasters and disruptions. They help maintain business continuity, protect data, and minimize downtime, all of which are critical for the success and resilience of any organization.

{% hint style="danger" %}
The solution must not only be implemented once, but actively maintained by competent people on client side. HA and DR solutions require constant maintenance and monitoring to ensure the solution is up and running 24/7. In the absence of staff with the necessary qualifications, any such solutions make no sense. In addition, the implementation of such a solution will entail additional resource and monetary costs. Only with the right resources and expertise on your part it will make sense to undertake this.
{% endhint %}

HA and DR solutions are always designed and applied locally, depending on the way your server infrastructure is organized and the resources available. Unfortunately, there is no universal solution in this case, and a complex approach is required.

## Implementation

High Availability solution typically includes two geographically distributed data centers. One data center acts as the primary or active site, providing customers access to the platform. The second data center acts as the secondary or standby site, maintaining a constantly updated replica of all system components, including the database, backend services, and website. In the event of a failure at the primary site, the secondary site can take over, ensuring uninterrupted service.

Disaster Recovery involves creating a plan for the actions to be performed when the main infrastructure is damaged and not fully operable. This plan outlines the steps to recover the IT infrastructure and restore normal operations. Regular checks and updates to this plan are essential to ensure preparedness and the ability to perform the necessary actions in a timely manner to minimize downtime and data loss. The DR plan often includes procedures for data backup, system recovery, and failover to alternate sites or systems.

In addition to the above you need to have some kind of intermediate arbiter and monitoring system running separately, which will check the state of the servers, as well as deal with their switching in case of failure. Such a system is also not universally applicable and must be implemented locally using the tools and scripts you have available. Alternatively, you can manually switch data centers based on alerts from the monitoring system, but this will increase downtime according to the responsiveness of the people in charge.

To maintain user access and device connectivity, it is important to retain network access. For this purpose, it is necessary to make sure that the network address remains the same when the data center is changed. This can be achieved in two ways.

* The best way is to switch the IP address from the old server to the new one. This way the domain name will be redirected to the new server, trackers will communicate with the new server, and users will most likely not notice the failure. But with geographically distributed data centers, IP address migration is not always possible.
* If you have no option to migrate the IP address, you can reconfigure the domain name (DNS A-record) to the IP address of the new server. This will preserve user access as well as the connection of devices configured on the domain. Unfortunately, devices configured to transfer data to the IP will no longer be online and will need to be reconfigured.

{% hint style="info" %}
Planning and implementing High Availability (HA) and Disaster Recovery (DR) solutions is considered a bespoke activity that may require the involvement and consultation of Navixy technical specialists.
{% endhint %}

## Licensing

The key aspect of the platform licensing scheme is that a single license key applies to only one active instance at a time. You cannot have two or more Navixy instances running simultaneously with the same license key.

For this reason, it is recommended to keep a replica of the platform dormant or inactive. This replica should only be activated and brought online in case of a disaster or failure with the primary server. This approach ensures compliance with the licensing scheme while also providing a backup solution for business continuity.

## Maintenance

While implementing a high availability solution is a crucial step towards ensuring business continuity and minimizing downtime, it is not enough to simply set it up and leave it unattended. High availability architectural solutions require constant maintenance and monitoring to function effectively.

Maintenance involves regular checks and updates to ensure that all components of the system are working as intended. This includes checking the status of hardware and software components, performing regular backups, and applying patches and updates as needed. Performing regular DR tests of failover and recovery procedures is also essential to ensure that they work as expected in the event of a failure.

Monitoring is equally important, as it allows for early detection and resolution of potential issues before they cause system failures or downtime. Monitoring tools can be used to track system performance, detect anomalies, and alert administrators to potential issues. This enables proactive intervention to prevent or minimize the impact of failures.

In conclusion, maintenance and monitoring are critical components of high availability and disaster recovery solutions. They ensure that the system remains reliable and effective in the face of potential failures, and enable proactive intervention to prevent or minimize the impact of downtime. Regular maintenance and monitoring also help to ensure compliance with regulatory requirements and industry standards for data protection and business continuity.
