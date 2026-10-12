---
description: Request DDD files from a vehicle's tachograph over the air without a physical connection. Covers on-demand download options by device type.
---

# DDD files download

Lets you remotely request tachograph data from a vehicle as DDD files, without physically connecting to the tachograph. What you can request depends on the device family, from a single download command to detailed requests covering multiple data types and date ranges.

{% hint style="info" %}
The download controls are **on-demand commands**, not saved settings. Clicking a download button sends a request to the device. It does not store a configuration.
{% endhint %}

<!-- SCREENSHOT: DDD files download block (Teltonika variant) showing data-type options, card 1/2, and date range. Annotate: data type, card selection, activities date range, send button. -->

## Initial setup

{% stepper %}
{% step %}
### Connect the company card

Insert your company tachograph card into a card reader connected to the computer that runs the Tacho Auth Client application. Contact Navixy technical support to obtain the application.
{% endstep %}

{% step %}
### Enter the Company card SN

In the **DDD files download** block that contains the **Company card SN** field (separate from the download buttons), enter the card's 16-character serial number, then **Save**. The device uses it to authenticate with the tachograph, so downloads won't work until it's set.
{% endstep %}
{% endstepper %}

## Download options by device family

What appears depends on the device family.

### Teltonika

{% hint style="info" %}
Teltonika devices show **two blocks, both labeled "DDD files download"**: one stores the **Company card SN**, the other sends the download commands below.
{% endhint %}

* **Company card SN**: enter your company card's 16-character serial number and **Save**. The device uses it to authenticate with the tachograph, and downloads won't work until it's set.
* **Card 1 / Card 2**: driver card data from tachograph slot 1 and slot 2 (driver and co-driver).
* **Activities**: driver activity records (driving, rest, work) from the vehicle unit for a selected period. Uses a date range, up to 90 days.
* **Detailed speed**: speed data recorded by the vehicle unit.
* **Events and faults**: the vehicle unit's log of events and faults.
* **Technical data**: vehicle unit technical and calibration data.
* **Overview**: vehicle unit identification and overview data (always included with a download).
* **Full vehicle data**: a complete vehicle unit download for a selected period (overview, activities, events and faults, detailed speed, and technical data). Uses a date range, up to 90 days.

### Galileo

A single **Download** command.

### BCE

A download command for **slot 1**, **slot 2**, or **vehicle-unit activities**.

## Downloading

{% stepper %}
{% step %}
### Request the download

Click the **Download** button (or send the relevant data-type command) for the data you need. The download can take up to 15 minutes.
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
