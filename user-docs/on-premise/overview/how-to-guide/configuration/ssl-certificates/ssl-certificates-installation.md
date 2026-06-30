---
description: Install SSL certificates on Navixy on-premise and convert HTTP to HTTPS. Covers certificate preparation, Nginx configuration, and platform configuration updates.
---

# SSL certificates installation

This page describes how to initially install an SSL certificate and convert your website to use HTTPS. If you are already using an SSL certificate and it has expired, refer to the [Update](ssl-certificates-update.md) page.

The the Navixy platform uses [Nginx](../nginx-web-server.md) as the web server. It is compatible with both Linux and Windows, therefore, the steps provided below are applicable to any operating system.

## Step 1. Prepare SSL certificates

To ensure SSL protection for your website, you will require an SSL certificate along with its private key. If your website has multiple domain names, you will need a certificate for each domain or a multi-domain or wildcard certificate.

{% hint style="danger" %}
To ensure proper functioning of the web server, it is important to have not only an SSL certificate, but also a matching private key file. Without the correct private key, the web server will fail to start. Both the certificate and the private key are typically provided by your SSL certificate authority.
{% endhint %}

To obtain a certificate, you can reach out to any certificate authority. The platform supports valid certificates from any issuer. For more details on SSL protection, refer to this page: [SSL encryption](../../requirements/ssl-encryption.md).

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
* [Tool to confirm the certificate matches the private key](https://www.sslshopper.com/certificate-key-matcher.html)
{% endhint %}

## Step 2. Install SSL certificates

### Automatic SSL configuration with LetsEncrypt

If your platform was installed automatically (see [Automatic installation](../../installation/advanced-installation/)), you can install free LetsEncrypt certificates using the [Configuration Wizard](../../installation/advanced-installation/ubuntu-20/configuration-wizard.md).

### Manual SSL configuration with any certificates

To manually install SSL certificates on your server, start by obtaining the certificate and private key files. These can be obtained from any certificate authority. Once you have the files, you need to place them in a directory on your server. It is recommended that you use the standard paths, which are `/etc/nginx/ssl/` for Linux and `C:\nginx\conf\ssl\` for Windows.

After placing the files in the appropriate directory, you can specify the path to the certificate and private key in the configuration of your website. It is recommended to use short paths like `ssl/name.crt` and `ssl/name.key`. By using these standard paths, you will not need to specify the full path to the certificate and private key in the website configuration.

## Step 3. Update configuration

### Update Nginx configuration

Find your website configuration file. It is usually called **navixy.conf** and is located at `/etc/nginx/sites-available/` (Linux) or `C:\nginx\sites-enabled\` (Windows).

{% hint style="info" %}
It is recommended to proceed with ready-to-use Nginx configurations provided on the [Nginx](../nginx-web-server.md) page. Simply choose a HTTPS configuration that corresponds to your operating system and the number of domains, and replace the current contents of navixy.conf with it. Then, you only need to specify the domains and paths to the certificates and private keys. By doing this, you can avoid possible errors since the rest of the configuration is already proven to work. However, you can also choose to edit the Nginx configuration manually if it suits your needs better.
{% endhint %}

Change the listening port from "80" to "443 ssl", and add the SSL-related lines to each site's configuration, specifying the correct path to your fullchain certificate and key files.

```sql
listen 443 ssl; ## listen for ipv4
ssl_certificate /ssl/certificate.crt;
ssl_certificate_key /ssl/private.key;
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
```

Here's an example for API site configuration:

```sql
server {
listen 443 ssl; ## listen for ipv4
server_name api.domain.com;
access_log /var/log/nginx/api.domain.com_ssl.access.log;
client_max_body_size 20m;
ssl_certificate /ssl/certificate.crt;
ssl_certificate_key /etc/nginx/ssl/private.key;
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
location / {proxy_pass http://127.0.0.1:8084;
}
}
```

Restart Nginx web server:

* For Linux: `nginx -t && nginx -s reload`
* For Windows, you need to terminate all Nginx processes and then start **nginx.exe** from `C:\nginx`.

Make sure Nginx has started and doesn't give any error related to SSL.

### Update platform configuration files

Next we need to tell Navixy to use HTTPS protocol instead of HTTP in all the configuration files. Open the following files:

* `/var/www/panel-v2/PConfig.js` – edit the parameter "apiRoot", changing "http" to "https". Edit the parameter "terminalHost" - change "ws" to "wss" and delete port 8383.
* `/var/www/pro-ui/Config.js` – edit the parameter "apiRoot", changing "http" to "https"
* `/var/www/pro-ui/static/app_config.js` – edit the parameter "apiUrl", changing "http" to "https" (if the value is present)
* `/home/java/api-server/conf/config.properties` – edit the parameter api.externalBaseUrl, changing "http" to "https" (if the value is present)

Restart Navixy services (see [Restarting instance](../../maintenance/restarting-instance.md)) for all the changes to take effect. If the page is still loading over HTTP, you can try clearing your browser's cache.
