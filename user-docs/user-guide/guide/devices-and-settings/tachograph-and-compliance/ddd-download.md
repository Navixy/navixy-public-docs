---
description: Request DDD files from a vehicle's tachograph over the air without a physical connection. Covers on-demand and scheduled download options by device type.
---

# DDD files download

Lets you remotely request tachograph data from a vehicle as DDD files, without physically connecting to the tachograph. What you can request depends on the device family, from a single **Download** button on simple devices to detailed, multi-type, date-ranged requests on richer ones.

{% hint style="info" %}
The download controls are **on-demand commands**, not saved settings. Clicking a download button sends a request to the device. It does not store a configuration.
{% endhint %}

<!-- SCREENSHOT: DDD files download block (Teltonika variant) showing data-type options, card 1/2, and date range. Annotate: data type, card selection, activities date range, send button. -->

## Initial setup

{% stepper %}
{% step %}
### Download (extract) data from the company or driver card

Connect a card reader to your computer and use it to download (extract) data from the company or driver card.
{% endstep %}

{% step %}
### Enter the company card number

In the **DDD files download** block, enter the company card number (an internal document of the organization) where prompted, then save. This associates downloaded files with your company. The TachoAuthClient application may be required for downloading. Contact Navixy technical support to obtain it.
{% endstep %}
{% endstepper %}

## Download options by device family

What appears in the block depends on the device:

* **Simple devices**: A single **Download** button retrieves the available data.
* **Teltonika**: Choose a **data type**: **card 1 or card 2**, **driver activities** (with a **date range**, up to about 90 days), **detailed speed**, **events and faults**, **technical data**, or **overview**. You can also store a **company card number**.
* **Galileo / BCE**: A download command (BCE also exposes slot 1 / slot 2 and vehicle-unit activities).
* **Remote-download variants**: Store a **company-card number** and set **auto-download intervals** for the vehicle unit and the driver card, so data is collected automatically on a schedule.

## Downloading

{% stepper %}
{% step %}
### Request the download

Click the **Download** button (or send the relevant data-type command) for the data you need. The download can take several minutes, typically 5 to 10.
{% endstep %}

{% step %}
### Keep the ignition on

Ensure the vehicle's ignition is on during the download. If the ignition is off, the download fails and you must restart it.
{% endstep %}
{% endstepper %}

Once a download completes, the file can be emailed automatically to the recipients you configure, see [DDD emails](ddd-emails.md).

{% hint style="warning" %}
Navixy only supports the **download functionality** of DDD files. Navixy doesn't process them in any way and doesn't read any data from them. DDD download and retention are legally regulated, in the EU, driver-card data is typically downloaded about monthly and vehicle-unit data about quarterly, with roughly one year of retention.
{% endhint %}

## See also

* [DDD emails](ddd-emails.md): auto-email downloaded DDD files to recipients.
