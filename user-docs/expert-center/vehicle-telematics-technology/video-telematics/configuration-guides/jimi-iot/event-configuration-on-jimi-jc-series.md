---
description: Configure custom event alerts on Jimi JC series dashcams in Navixy using state field value rules with a reference table of supported event codes.
---

# Event configuration on Jimi JC series

Jimi JC devices can send various event codes to the Navixy platform. While the platform has its own notification system with already predefined alerts, you can set up additional custom alerts based on specific event codes from your devices using [state field alert type](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/events-and-notifications/inputs-and-outputs/state-field-value). This feature is particularly useful when you need alerts for events where video recording isn't necessary. For example, count how many particular events happen using the [report on all events](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/reports/specific-report-details/report-on-all-events).

## How to set up event code alerts

1. Navigate to the alert configuration section.
2. Select **State field value** as the alert type.
3. In the settings:

- Set "event_code" as the state field;
- Enter the desired event code as the expected value;
- Configure additional notification preferences as needed.

## Example use case: Seat belt unplug alert

Let's walk through setting up an alert for when a seat belt is unplugged:

1. Create a new alert
2. Select **State field value** as the alert type\
   ![Example use case: Seat belt alert — Setting alert type](../../../../.gitbook/assets/browser_KjcUn9UmiH.png)\
   3\. In the settings tab:\
   ![Live example: Setting event code 167 for seat belt unplug detection](../../../../.gitbook/assets/image-20241024-092818.png)

- State Field: `event_code`
- Expected Value: `167` (seat belt unplugged code)

When the device sends a packet with event code 167, the platform triggers the alert and notifies designated personnel.

Additionally, check the short video on event code 143 to find out how to create such alerts:

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F6wixEtiNjTTQGdw00ODs%2Fuploads%2FH3OsZfippp6o5MXzkxm3%2FState%20field%20value%20event%20creation%20for%20Jimi%20JC.mp4?alt=media&token=295c0786-1c6e-47ce-9a2c-3ac49cb5b934" %}

## Event codes table

This table provides a list of event codes supported by Jimi JC devices.

For your convenience, Navixy presents both:

- Decimal codes (used in platform configuration).
- Hexadecimal (HEX) codes (as referenced in manufacturer documentation).

The definitions provided closely align with the original Jimi JC series documentation, ensuring consistency with manufacturer specifications. Use these codes when configuring state field value alerts in the platform.

| HEX Code | Code | Event                           |
| -------- | ---- | ------------------------------- |
| 0x01     | 1    | SOS                             |
| 0x0E     | 14   | External battery low            |
| 0x47     | 71   | Fatigue driving                 |
| 0x50     | 80   | Door closed                     |
| 0x51     | 81   | Door opened                     |
| 0x53     | 83   | Fuel stolen                     |
| 0x69     | 105  | File uploading                  |
| 0x70     | 112  | SD card mount                   |
| 0x73     | 115  | Fuel data timeout               |
| 0x76     | 118  | Temperature data timeout        |
| 0x80     | 128  | Vibration                       |
| 0x86     | 134  | SD card cannot be recognized    |
| 0x87     | 135  | Over speed                      |
| 0x88     | 136  | Power removal                   |
| 0x8A     | 138  | Oil or electronics connected    |
| 0x8B     | 139  | Oil or electronics disconnected |
| 0x8C     | 140  | DMS – Eye closed                |
| 0x8F     | 143  | DMS: Distraction                |
| 0x90     | 144  | Harsh acceleration              |
| 0x91     | 145  | Harsh deceleration              |
| 0x92     | 146  | Harsh turning                   |
| 0x93     | 147  | Crash                           |
| 0x94     | 148  | DMS: Missing face               |
| 0x97     | 151  | DMS: Phone calling              |
| 0x9A     | 154  | DMS: Smoking                    |
| 0xA0     | 160  | DMS: Yawn                       |
| 0xA1     | 161  | DMS: Monitoring shelter         |
| 0xA2     | 162  | DMS – Calibration fail          |
| 0xA3     | 163  | DMS: Look down                  |
| 0xA4     | 164  | SD card capacity is not enough  |
| 0xA5     | 165  | RFID reading                    |
| 0xA6     | 166  | Seat belt plugged               |
| 0xA7     | 167  | Seat belt unplugged             |
| 0xCC     | 204  | Forward collision warning       |
| 0xCD     | 205  | Lane departure warning          |
| 0xCE     | 206  | Headway monitoring warning      |
