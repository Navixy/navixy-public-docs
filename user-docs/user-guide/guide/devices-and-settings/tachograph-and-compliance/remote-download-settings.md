---
description: Schedule recurring tachograph data collection for vehicle unit and driver card, and store the company card number to authorize remote downloads.
---

# Remote download settings

Configures automatic, scheduled tachograph downloads on devices that support remote download. It stores the company card number and how often the vehicle unit and driver card data are collected.

## Settings

* **Company card number**: the identifier on the company's tachograph card, used by the device to verify (authenticate) that the remote download request is authorized. Enter the number exactly as it appears on the card.
* **Vehicle unit download interval** (days): how often the device's on-board tachograph unit data is pulled automatically. EU regulations require a vehicle unit download at least every 90 days. Many operators use 28 days as a standard interval.
* **Driver card download interval** (days): how often driver card data is downloaded automatically when a card is present in the tachograph. EU regulations require driver card data at least every 28 days.

## Availability

Appears on tachograph devices that support remote, scheduled downloads.

## See also

* [DDD files download](ddd-download.md): request DDD files on demand.
* [DDD files recipient email](ddd-emails.md): email downloaded files to recipients.
