---
description: Configure SSL certificates for Navixy on-premise. Choose a provider, meet domain requirements, and manage expiration using commercial or free Let's Encrypt certificates.
---

# SSL encryption

SSL encryption is a vital security measure to protect the web traffic between your customers and your server from malicious interception. This technology safeguards sensitive information from being accessed by unauthorized individuals, particularly man-in-the-middle attackers.

To enable SSL encryption, you will need a valid SSL certificate for your domain that is signed by a trusted Certificate Authority (CA). It is also essential to note that due to iOS security policy, the X-GPS Monitor for iOS app requires SSL encryption; otherwise, it won't work.

### Choosing SSL provider

Several companies act as certificate authorities that provide SSL certificates, including both paid and free options:

* Paid options usually follow stricter encryption rules, while free alternatives offer adequate protection. Some of the popular paid CA examples include [Comodo](https://ssl.comodoca.com/), [GoDaddy](https://www.godaddy.com/web-security/ssl-certificate), [DigiCert](https://www.digicert.com/), and [RapidSSL](https://www.rapidssl.com/).
* Popular free CA option: [Let's Encrypt](https://letsencrypt.org/).

### Domains to secure

Typically, three domains are used in working with Navixy, such as:

* [my.domain.com](http://my.domain.com) – for user web interface
* [panel.domain.com](http://panel.domain.com) – for Admin panel
* [api.domain.com](http://api.domain.com) – for API calls

You have several options to use SSL encryption for your domains, such as issuing a wildcard certificate for all subdomains **\*.domain.com**, separate certificates for each subdomain, or a multi-domain certificate that includes all three domains.

{% hint style="danger" %}
It is important to take note of the expiration date of your certificate, particularly when using a free certificate from [Let's Encrypt](https://letsencrypt.org/). This certificate has a fixed expiration date of three months, requiring manual updates or automatic updates setup. Also, ensure that the certificate contains a full chain of trust, including root and intermediate certificates, or features such as Navixy iOS monitoring app may not work.
{% endhint %}

After obtaining the SSL certificates, you need to install them, and you can refer to Navixy's simplified how-to instruction for certificate installation. CA support is available if you have questions regarding the certificate issuance process.
