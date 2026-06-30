---
description: Resolve common GPS device activation issues in Navixy. Covers required device configuration, timezone settings, and steps when devices fail to appear online.
---

# GPS device activation troubleshooting

Activating GPS tracking devices on the Navixy platform is usually straightforward thanks to [automatic device activation](../quick-start/activate-gps-device.md). However, if you encounter issues, this guide will help you troubleshoot and resolve common device activation problems.

### **Device Time Zone settings differ from UTC+0h**

Ensure that GPS tracking devices are set to UTC+0h to avoid data misinterpretation by the Navixy server. Reconfigure any manually set or previously connected devices to UTC+0h before activation on Navixy.

<details>

<summary>Read more</summary>

**Issue:**\
When the Navixy server software receives data from a GPS tracking device, the data comes with a timestamp. The server processes this data based on the user’s time zone preferences, ensuring accurate track details and reporting across different time zones. However, the server expects all device data to be in UTC+0h. Manually configured devices or devices previously connected to another platform may have a different time zone, causing Navixy to misinterpret timestamps, potentially marking the data as outdated or faulty.

**Solution:**\
For accurate data processing and display, all GPS tracking devices must be configured to UTC+0h. If the device is not set to UTC+0h, the Navixy server might interpret the data incorrectly, affecting the reliability of track details and reporting.

**Recommendations for Troubleshooting:**

1. Ensure the device is set to UTC+0h before activating it on Navixy.
2. Avoid setting the local time zone on the device.

</details>

### Device is switched off or lacks GSM connection

Ensure the device is turned on and connected to the GSM network. You can do this by checking the device's power status and verifying that it is registered on the GSM network. If possible, send a test SMS to confirm that the device is receiving messages properly.

<details>

<summary>Read more</summary>

**Issue:**

When the Navixy server tries to communicate with a GPS tracking device, the device must be turned on and connected to the GSM network. If the device is switched off or lacks a GSM connection, activation commands cannot be delivered, causing the device to appear offline or unresponsive.

**Solution:**

To ensure proper communication between the Navixy server and the GPS tracking device, verify that the device is powered on and has a stable GSM connection. This enables the server to receive and process data correctly.

**Recommendations for Troubleshooting:**

* If you have physical access to the device, check its LED indicators to confirm it is turned on and connected to the GSM network.
* Send an SMS to the device with delivery confirmation to check GSM registration. If the SMS delivery fails, the device is not registered on the GSM network. You might have to send SMS to the device via the SIM portal to verify.

</details>

### SIM card balance is low or no Internet service

Ensure the SIM card has sufficient balance and internet service is activated on it for the device to connect to the platform. Check the SIM card's balance and verify that it has enough funds to support Internet data usage. Additionally, confirm that the SIM card's data plan includes adequate Internet traffic to maintain a stable connection with the platform.

<details>

<summary>Read more</summary>

**Issue:**\
During the device activation process, a tracking device tries to connect to the Navixy platform and transmit its location data over the Internet. If the SIM card in the device has insufficient balance or has run out of Internet traffic limits, the device cannot connect to the platform. This results in a failure to send location data and other essential information, rendering the tracking device non-functional.

**Solution:**\
To ensure uninterrupted connectivity and data transmission, verify that the SIM card used in the tracking device has adequate balance and GPRS traffic. Regularly monitor and top up the SIM card balance to prevent connectivity issues.

**Recommendations for Troubleshooting:**

* Check the SIM card balance to ensure it has enough funds to support Internet access. Verify that the SIM card plan includes sufficient Internet data to handle the device's communication needs.
* Ensure the APN settings are correctly configured on your device. Obtain the correct APN settings from your cellular network provider, which typically include the APN name, username, and password. These can usually be found on the provider’s website or by contacting their customer support.
* If connectivity issues persist, contact your SIM card provider to confirm there are no network-related problems affecting Internet traffic.

</details>

### Incorrectly entered IMEI or phone number

Verify the IMEI and phone number entered during activation to ensure accuracy. Double-check each digit of the IMEI and phone number against the device's documentation or label to make sure there are no errors. Correct any discrepancies to prevent activation issues and ensure successful communication with the platform.

<details>

<summary>Read more</summary>

**Issue:**

When activating a device on the Navixy platform, an incorrect IMEI or phone number can cause activation to fail. This error typically occurs due to a typo or misentry of the device’s details, leading to unsuccessful communication between the device and the server.

**Solution:**

To ensure successful activation, double-check the IMEI and phone number entered for the device. Confirm that all digits are correct and correspond to the device’s information.

**Recommendations for Troubleshooting:**

* Verify the IMEI and phone number by rechecking the device’s documentation or label.
* If activation fails, delete the device and repeat the activation by carefully re-entering the IMEI and phone number to correct any potential errors.

</details>

### Configuration is protected with password or master number

If the device has custom settings such as passwords or master phone numbers, it may prevent automatic configuration by Navixy, leading to activation failures. Remove any custom passwords or master numbers before attempting automatic activation.

<details>

<summary>Read more</summary>

**Issue:**\
During device activation, the Navixy platform sends configuration SMS commands to the device from the service phone number. If the device was previously configured to receive configuration commands from a dedicated master number or if a custom password was set, these commands might fail, leading to unsuccessful activation.

**Solution:**\
To allow automatic activation, remove any custom passwords or master numbers from the device. Alternatively, manually configure the device using the appropriate activation commands.

**Recommendations for Troubleshooting:**

* Remove any custom passwords or master numbers from the device before attempting automatic activation.
* If automatic activation fails, [manually configure](../quick-start/activate-gps-device.md#activate-gps-device-manually) the device using the provided activation commands.

</details>

### Unsupported device or device modification

Make sure your device is listed among those [supported by Navixy](https://navixy.com/devices/). If you are unsure of the manufacturer and/or model, please consult with your device supplier.  It is also recommended to update the device’s firmware to the latest version.

<details>

<summary>Read more</summary>

**Issue:**\
When activating a GPS tracking device on the Navixy platform, it is important that the device model is properly identified and indicated during activation. If the device is not correctly identified, the data sent from the device may not be parsed correctly or may be misinterpreted. Additionally, the firmware version for the same model may be outdated or a custom version, causing compatibility issues.

**Solution:**\
To resolve these issues, verify that your device is on the list of supported devices and ensure it has the latest firmware version. If your device is not on the list of supported models or has a custom firmware version, please reach out to your service provider for support.

**Recommendations for Troubleshooting:**

* Check our list of supported devices.
* Update the device firmware to the latest version.
* If the device is not supported or uses a custom firmware version, please contact the technical support team of your [service provider.](../quick-start/about-service-providers.md)

</details>

### Teltonika and Ruptela leading space issue with automatic activation

Ensure proper configuration for Teltonika and Ruptela devices to avoid issues with leading spaces in SMS commands. Configure the devices [manually](../quick-start/activate-gps-device.md#activate-gps-device-manually) or check with your service provider for the possibility of using another SMS gateway that is optimized for M2M communication.

<details>

<summary>Read more</summary>

**Issue:**\
During automatic activation of Teltonika and Ruptela devices, users may encounter problems due to leading spaces being removed by some SMS gateways. These devices expect a user and password preceding the command, like `<login> <password> command`. When login and password are not set (as recommended), this results in double leading spaces `command`. Some SMS gateways, not optimized for M2M communication, trim these spaces, causing the commands to be unrecognized by the devices.

**Solution:**\
To resolve this issue, either reach out to your service provider to substitute the SMS gateway or manually configure these devices via Teltonika or Ruptela configuration software using the Navixy server IP and port details.

**Recommendations for Troubleshooting:**

* [Manually configure the devices](../quick-start/activate-gps-device.md#activate-gps-device-manually) using the configuration software.
* Contact your service provider to use an SMS gateway optimized for M2M communication that preserves leading spaces.

</details>

### SIM card has no phone number

[Automatic device activation process](../quick-start/activate-gps-device.md#activate-gps-device-automatically) requires entering a SIM card's phone number, but SIM cards for M2M communication might not have one. In this case, [manually configure the device](../quick-start/activate-gps-device.md#activate-gps-device-manually) and enter the device IMEI (or any arbitrary set of digits) as the phone number in the activation dialog. Alternatively, contact your service provider to request integration with the MVNO platform for enabling communication using ICCID.

<details>

<summary>Read more</summary>

**Issue:**

When using M2M SIM cards from MVNO providers, they often don’t have associated phone numbers. Instead, these SIM cards are identified through other identifiers, most commonly ICCID. As a result, configuration commands can’t be sent via a common SMS gateway as with normal SIM cards. This leads to challenges in device activation and communication.

**Solution:**

To resolve this issue, you have two options: [manually configure the device](../quick-start/activate-gps-device.md#activate-gps-device-manually) and place the device IMEI (or any arbitrary set of digits) as the phone number in the activation dialog, or reach out to your [service provider](../quick-start/about-service-providers.md) to request integration with the MVNO platform, enabling bi-directional communication over SMS using ICCID instead of a phone number.

**Recommendations for Troubleshooting:**

* [Manually configure the device](../quick-start/activate-gps-device.md#activate-gps-device-manually) and enter the device IMEI as the phone number in the activation dialog.
* [Contact your server provider](../quick-start/about-service-providers.md) to request integration with the MVNO platform for enabling communication using ICCID.

</details>

## See also

* [MQTT Device activation guide](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/gps-devices/add-and-manage-devices/activate-your-mqtt-device-on-navixy)
