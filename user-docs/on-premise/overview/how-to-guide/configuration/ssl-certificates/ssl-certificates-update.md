---
description: Update expired SSL certificates on Navixy on-premise running Nginx. Locate certificate and key files, then replace them with the new certificate chain.
---

# SSL certificates update

## Installation of the SSL certificate

This page provides instructions on how to update your expired SSL certificate. If you need instructions on how to install a certificate for the first time (switch from HTTP to HTTPS), please refer to the [installation](ssl-certificates-installation.md) page.

SSL certificates have an expiration date, which means they need to be re-issued and updated on your server before they expire. Updating SSL certificates is a simple process that doesn't require advanced system administration skills.

The Navixy platform uses Nginx as its web server. To update your SSL certificates, locate the current certificate and private key files in the Nginx config and replace them with the new ones. This page explains the SSL certificate update process in detail.

{% hint style="danger" %}
To ensure proper functioning of the web server, it is important to have not only an SSL certificate, but also a matching private key file. Without the correct private key, the web server will fail to start. Both the certificate and the private key are typically provided by your SSL certificate authority.
{% endhint %}

## Updating the certificates

### Nginx configuration on Linux server

Nginx configuration for your website is located at the path below. Read the file contents with any text editor.

`/etc/nginx/sites-available/navixy.conf`

Inside you can see lines like this:

```
ssl_certificate /etc/nginx/ssl/certificate.crt;
ssl_certificate_key /etc/nginx/ssl/private.key;
```

This means that the certificate files are located at `/etc/nginx/ssl/`. The path can also be shortened to `ssl/certificate.crt` if the files are in the above directory. Both are correct.

### Nginx configuration on Windows server

Nginx configuration for your website is located at the path below. Read the file contents with any text editor.

`C:\nginx\conf\sites-enabled\navixy.conf`

In the file you can find the lines like this:

```
ssl_certificate /ssl/certificate.crt;
ssl_certificate_key /ssl/private.key;
```

The `/ssl/` entry here is short for `C:\nginx\conf\ssl`. It is recommended to use this path, but if your certificates are located in a different folder, you need to specify the full path in the config file.

If you have multiple domains configured on your Navixy platform, you'll typically have separate domains for API, admin panel, and user interface. This will result in the Nginx configuration containing several blocks starting with "server" and specifying the path to the certificate for each block. It's important to note that you'll need either a separate SSL certificate for each domain name or a wildcard certificate (for \*.domain.com) to work with all third-level domains.

## Certificate requirements

### Chain of trust

In order for your SSL certificate to work properly, it must include a full chain of trust. This means that the certificate file(s) should not only contain the primary certificate, but also any intermediate and root certificates necessary to establish a chain of trust with the certificate issuer. Such certificates are known as full chain certificates.

It is important to ensure that the certificate you obtain includes the full chain, as some features such as mobile applications may not function properly without it. If you encounter any difficulties in building the full chain, you can contact the certificate issuer for assistance or use online tools like this one to resolve the chain of trust. For more information about the chain of trust, you can refer to an explanation provided by the SSL issuer.

{% hint style="info" %}
* [How certificate chains work](https://knowledge.digicert.com/solution/SO16297.html)
* [SSL certificates chain resolver](https://www.leaderssl.com/tools/cert_chain_resolver)
{% endhint %}

### Private key requirement

For the Nginx web server to start correctly, the private key must match the SSL certificate. Usually, the certificate issuer provides the private key along with the certificate. If you reissue a certificate from the same authority, the private key often stays the same and does not need to be replaced.

{% hint style="info" %}
[Tool to confirm the certificate matches the private key](https://www.sslshopper.com/certificate-key-matcher.html)
{% endhint %}
