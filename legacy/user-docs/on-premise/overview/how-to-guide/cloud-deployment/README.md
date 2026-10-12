---
description: Deploy Navixy on-premise on a cloud virtual server. Guides for AWS, Google Cloud, and Microsoft Azure cover network setup, VM provisioning, and SSH access.
---

# Cloud deployment

Cloud-based virtual server deployment solutions are increasingly embraced by companies today. These solutions offer greater flexibility and scalability, along with a qualitatively new level of fault tolerance. Additionally, they eliminate the need for local data centers, such as purchasing hardware servers and building local networks.

Nowadays, there are dozens of cloud providers offering virtual server hosting services, and you are free to choose any of them. These can be either worldwide providers or local solutions in your country. The main thing is that the service provided should meet your security and other requirements, as well as be able to deploy a server that meets the necessary requirements for the installation of the platform.

**Navixy is compatible with various server environments and can be seamlessly deployed on both physical and virtual servers.** In this section, we will take a look at cloud servers deployment for further installation of Navixy On-Premise solution on the most popular cloud platforms:

* [Amazon Web Services (AWS)](amazon-web-services-aws.md)
* [Google Cloud Platform (GCP)](google-cloud-platform.md)
* [Microsoft Azure](microsoft-azure.md)

Although we only describe server deployment for the listed platforms, the basic principles remain the same for any cloud solution. Here's what needs to be done:

1. Create a virtual local network environment (in some platforms done automatically when the server is created).
2. Create a virtual server (or servers) with the necessary hardware settings.
3. Assign an external IP address to the server for client access and device connection.
4. Open the necessary ports for external connections.
5. Install and configure the platform.

{% hint style="info" %}
Kindly take note that these are only basic steps to ensure a reliable and functional cloud server setup. Fine-tuning the cloud server and network is an extensive task for a system administrator. If you need more information on any of the features, please refer to the manuals for the cloud platform you selected.
{% endhint %}
