---
description: Point your custom subdomain to Navixy via CNAME, update instance settings, and enable SSL for secure access
---

# Domain name

When you create a new Navixy trial account, you are given a default URL in the format of `https://*****.navixy.com`. However, you may want to customize this URL to use your own domain name.

## How to configure a custom domain

To set up a custom domain (e.g., `my.company.com`) to point to the user web interface, follow the steps below:

{% stepper %}
{% step %}

### Create a CNAME record

First, you must create a CNAME (Canonical Name) record on your DNS server that points to the Navixy server. This record acts as an alias, ensuring your custom subdomain redirects to the platform. In "www.navixy.com", "navixy.com" is the domain name, and "www" is the CNAME. If either option is entered into the address bar, both will take you to the same place. The "www" is another way to get there. However, in this case, you can also use a CNAME record to redirect your DNS to another domain.

{% hint style="info" %}
CNAME records can't be created for the root domain (e.g., `company.com`). You must use a subdomain, such as `my.company.com` or `www.company.com`.
{% endhint %}

**Configuration details**

Access your DNS configuration via your hosting provider or registrar and add a new CNAME record pointing to one of the following, depending on your platform location:

- EU Platform: `saas.navixy.com`
- US Platform: `saas.us.navixy.com`
- ME Platform: `saas.me.navixy.com`

If you are unsure which platform your account uses, check your current Admin panel URL. If it contains "us.navixy.com", use the US address. Otherwise, use the EU or ME address.

**Requirements**

- Primary DNS service must be enabled, and you need to have access to edit the domain zone. Usually, registrars or hosting providers give access via a web interface to edit zones.
- Secondary DNS service must also be enabled, but editing the zone on the secondary server is usually not required.
- You can use any domain level, but Navixy recommends using a third-level domain. The second-level domain is usually reserved for your company's website and can't be used for anything else.

**Examples of DNS record configuration**

Below are examples of how to configure a domain using common registrars.

[**GoDaddy**](http://godaddy.com)

1. Log in to your GoDaddy account.
2. Click **Launch** next to **Domains**.
3. Click the gear icon for the target domain and select **Domain Details**.
4. Select the **DNS Zone File** tab and click **Add Record**.
5. Select **CNAME**.
6. Enter the following information:
   - **Host**: Enter your subdomain prefix (e.g., enter `my` to create `my.company.com`).
   - **Points To**: Enter `saas.navixy.com` (or the US/ME equivalent).
7. Click **Save** and then **Save Changes**.

[**Enom**](http://enom.com)

1. Log in to your **Enom** account.
2. Navigate to **Domains** and select **My Domain**.
3. Click the domain you wish to use.
4. Click **Host Settings**.
5. Click **New Row** to add a record.
6. Enter the subdomain prefix (e.g., `my`) in the first field.
7. Enter `saas.navixy.com` (or the US/ME equivalent) as the host name.
8. Click **Save**.
   {% endstep %}

{% step %}

### Verify the CNAME record

Before changing settings in the Navixy interface, make sure your DNS record is configured correctly.

**Option A: Terminal command**

You can query DNS servers using the `nslookup` command from your terminal: `nslookup my.company.com`

Expected response:

```
my.company.com canonical name = saas.navixy.com.
Name: saas.navixy.com
Address: 3.121.166.173
```

**Option B: Online lookup tools**

You can use online tools such as [MxToolBox ](https://mxtoolbox.com/)to perform a CNAME lookup. If the result shows your custom domain pointing to `saas.navixy.com`, the setup is correct.

{% hint style="info" %}
If the lookup doesn't return the correct CNAME, you must wait for the DNS records to propagate. Navixy can't control this duration. Don't proceed to the next step until the record is verified.
{% endhint %}
{% endstep %}

{% step %}

### Update Navixy settings

Once the CNAME record is active:

1. Log in to the Navixy Admin Panel.
2. Navigate to **Settings** **→** **Service preferences → Domain and URLs**.
3. Enter your custom domain name (e.g., `my.company.com`).
4. Save your changes.
   {% endstep %}
   {% endstepper %}

## Using multiple domain names with the same instance

You can't use more than one domain name with a single Navixy ServerMate instance. Once you configure a custom domain, the previous default URL (e.g., `1234.navixy.com`) stops working.

{% hint style="warning" %}
While Navixy provides recommendations for DNS configuration, Navixy isn't responsible for the management of your third-party domain settings.
{% endhint %}

## SSL encryption

SSL encryption is a proven way to protect web traffic between your customers and your server by encrypting it, which prevents a man in the middle from intercepting the web traffic and accessing sensitive information about your customers. To use SSL encryption, you need to have a valid SSL certificate for your domain that is signed by a trusted Certificate Authority (CA).

Navixy highly recommends enabling SSL encryption for your domain due to the following reasons:

- It may be a mandatory requirement when handling sensitive data for government or large clients.
- It prevents browsers from displaying "Not Secure" warnings to your users.
- The [X-GPS Monitor](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/x-gps-mobile-apps/x-gps-monitor) app for iOS devices requires SSL encryption to function.

After enabling SSL encryption, the protocol in the address bar changes from http to https, and a lock icon appears next to it, indicating that the connection is secure.

### How to install SSL certificate

- Navixy-managed (recommended): Contact the Navixy technical support team. Navixy can issue a Let's Encrypt certificate for you and ensure it is automatically renewed. This service is free of charge.
- Self-managed: If you prefer to obtain your own certificate from a different authority, provide the certificate and private key to the Navixy technical support team. Navixy installs it on your server free of charge. You may also use a wildcard certificate if you possess one for your higher-level domain.
