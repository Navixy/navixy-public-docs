---
title: Recurso Confiable event codes
description: >-
  The main Recurso Confiable protocol passes Navixy's platform event ID through
  unchanged, but a second protocol option replaces it with its own three-value
  code
---

# Recurso Confiable event codes

## Question

What do the numeric event codes Recurso Confiable receives mean, and how do they map to Navixy events?

## Answer

Navixy ships two Recurso Confiable protocol options, and the answer depends on which one the retranslator uses. Both send the code in the `code` field, but only the main protocol passes Navixy's event ID through unchanged. Check the protocol name shown in **Data forwarding** → **Protocols** before you interpret a code.

### Main Recurso Confiable protocol

This protocol sends Navixy's platform event ID as is. There's no separate translation table, so a code you see on the Recurso Confiable side always matches the same numeric event ID documented in [Platform's event code IDs and their definitions](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/access-iot-data/save-iot-data-to-csv-file/columns-in-csv-file#platforms-event-code-ids-and-their-definitions).

For a regular movement or location packet with no alarm, the code is `2` ("Track. No specific event, just a track point").

### Second protocol option

The second Recurso Confiable protocol option, used for a specific enterprise retail integration, replaces the platform event ID with its own code. It distinguishes only three cases:

| **Navixy event**                                                                | **Code sent** |
| ------------------------------------------------------------------------------- | ------------- |
| SOS button pressed (event ID 83)                                                | `911`         |
| OBD Unplug from the car’s connector (44), Device detached from the object (100) | `912`         |
| Any other event, including plain track points                                   | `0`           |

Don't use the platform event ID table to interpret codes from this protocol. A code of `0` doesn't identify which event occurred, and a plain track point sends `0` rather than `2`.

If you're not sure which of the two options a retranslator uses, contact [support](mailto:support@navixy.com) with the retranslator name.

## Links

[Recurso Confiable](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/connectivity/data-forwarding/recurso-confiable)

[Platform's event code IDs and their definitions](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/access-iot-data/save-iot-data-to-csv-file/columns-in-csv-file#platforms-event-code-ids-and-their-definitions)
