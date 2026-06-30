---
description: Enable online payments via Stripe (webhooks and API keys) or connect a custom gateway using a payment URL
---

# Payment gateway

With Navixy, you can provide your customers with online payment capabilities using Stripe or other popular systems.

## Accepting payments with Stripe

You can easily integrate Stripe to automatically receive online payments from your customers.

{% hint style="info" %}
Stripe is a global payment processor that supports more than 100 currencies and is available in over 30 countries. By integrating Stripe with Navixy, you can accept online payments using a range of methods, including bank cards (Visa, Mastercard, American Express), Google Pay, Apple Pay, Alipay, and many others.
{% endhint %}

To activate the Stripe integration, contact your account manager for assistance. Once activated, you need to configure the following technical settings in your Stripe account:

1. If you don't have a Stripe account yet, create one at [http://stripe.com](http://stripe.com).
2. Configure the methods you want to accept (e.g., bank cards, digital wallets) inside your Stripe dashboard.
3. Generate your API keys and add them to your Navixy account to enable communication between the platforms.

The instructions below guide you through the specific configuration required to integrate Navixy and Stripe.

### How to set up Stripe webhook

1. Log in to the [Stripe dashboard](https://dashboard.stripe.com/).
2. Navigate to [Developers → Webhooks](https://dashboard.stripe.com/account/webhooks).
3. Click **Add endpoint**. Fill out the fields in the dialog box. The **Endpoint URL** depends on where your service is deployed (EU or US server).\
   EU platform: `https://saas.navixy.com/api-v2/external/payments/<PlatformID>/stripe`\
   US platform: `https://saas.us.navixy.com/api-v2/external/payments/<PlatformID>/stripe`\
   Replace `<PlatformID>` with your specific platform ID. This is typically your panel number or the ID you use to log in to the Admin panel.

#### **Filtering events**

In the **Select events to listen to** (or **Select types to send**) section, select only the following 4 items:

1. `charge.refunded`
2. `payment_intent.amount_capturable_updated`
3. `payment_intent.succeeded`
4. `payment_intent.payment_failed`

#### **Stripe API keys**

You must provide your Stripe API keys to the Navixy support team to complete the integration.

**Standard API keys**

Navigate to **Developers → API keys**. Retrieve the following two parameters:

- `Publishable key`
- `Secret key`

**Webhook signing secret**

Navigate to **Developers → Webhooks**. Click on the webhook endpoint you just created. Under the Signing secret section, click **Reveal** to obtain the key:

- `Signing secret`

Once you have completed these steps, you are ready to accept online payments.

## Accepting payments with other methods

You can use practically any payment method by creating a custom script to receive online payments. Your script should be accessible via a URL and process payments by calling the [Navixy Billing API](https://www.navixy.com/docs/navixy-api/user-api/backend-api/resources/billing).

### User interface for custom payment methods

To set up a custom payment gateway:

1. Go to [Service preferences](service-preferences/) in the Navixy Admin Panel settings.
2. Enter the payment system URL.

Your payment system needs to process the parameter `/?id={id}`.

Once you have specified the **Payment system URL** in the **Domain & URLs** section of **Service preferences**, an **Add money** option is enabled in the [Profile](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/profile) section of the web platform:

<figure><img src="../.gitbook/assets/image (74).png" alt="Add money link" width="375"><figcaption><p>Add money option</p></figcaption></figure>
