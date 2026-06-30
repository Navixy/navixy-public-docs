---
title: Missing Customer ID and Customer Name in RC-Walmart
description: Configure the External ID field with the full Recurso Confiable customer format to include Customer ID and Customer Name in RC-Walmart exports
---

# Missing Customer ID and Customer Name in RC-Walmart

**Question: Why are the Customer ID and Customer Name not displayed in Walmart?**

**Answer**: The main reason why the Customer ID and Customer Name are not being included in the Walmart retransmission is due to an incorrect configuration of the External ID field.

For this type of integration, it is necessary to follow the standard Recurso Confiable format:\
License Plate | Route ID | Company ID | Company Name

It is important to note that the configuration must follow the Trusted Resource documentation. If only a single number is entered in the External ID field, the system will not be able to transmit all the required information, which results in missing fields such as Customer ID and Customer Name.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**Links:**

[Waltmart RC blogpost](https://www.navixy.com/es/blog/ayudando-a-transportistas-logisticos-a-cumplir-con-el-cumplimiento-de-recurso-confiable-de-walmart-superando-desafios-de-transmision-de-datos)&#x20;

[Recurso Confiable](https://app.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/connectivity/data-forwarding/recurso-confiable)
