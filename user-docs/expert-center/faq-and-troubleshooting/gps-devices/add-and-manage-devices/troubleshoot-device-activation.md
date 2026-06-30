---
description: Diagnose and fix GPS device activation failures in Navixy. Covers device, SIM card, and platform configuration causes for connection problems.
---

# Troubleshoot device activation

There can be various issues during the device activation process. To expedite troubleshooting, it is effective to identify all possible reasons and address them one by one.

## How does a device come online?

A device goes online once it establishes a connection with the server where the corresponding device ID is registered. The platform then receives and decodes the first packet containing valid coordinates and current time.

There are three main components involved in the activation and operation of almost all devices on the platform:

* GPS Device - refers to the device itself, which needs to transmit data in a specific format according to the model's protocol.
* SIM Card - the device requires a SIM card for connectivity. Without it, the device will not be able to establish a connection.
* Platform - it sends automatic commands to your devices if there is an SMS gateway installed.

We have listed these components in a specific order for a reason. If the device fails to connect to the platform for the first time, it is important to check the device and SIM card first, as approximately 99.9% of all issues are resolved by addressing these two items. Only in 0.1% of cases, the problem may lie with the platform itself.

Now, let's explore all possible reasons and actions for the initial activation on the platform. We recommend checking all of these aspects to start tracking devices and take full advantage of the platform's features as soon as possible.

## GPS device

Here, you will find a comprehensive list of all the possible device-side issues that we are aware of. The majority of these problems can be resolved either by addressing them directly on the device or with the assistance of the manufacturer. If you encounter any activation commands that need to be executed on the platform side, our technical support department can provide guidance and ensure that the commands have been successfully sent to the devices.

<details>

<summary>If a device is new or has not been used for an extended period of time</summary>

If device is new or has not been used for an extended period of time, a GPS cold start may be necessary. A GPS cold start occurs when the device needs to establish its location from scratch. This typically happens when the device has been turned off or has been out of range from satellite signals for a significant amount of time. During a cold start, the device has to download data from each satellite to accurately calculate its precise location. The duration of a cold start can vary depending on the strength and availability of satellite signals. It usually takes a few minutes, but on certain device models, it may take up to 15 minutes.

In this case, be patient and allow the device some time to complete the cold start process.

</details>

<details>

<summary>A device is switched off</summary>

Yes, there are instances where the device is switched off and the user has thoroughly checked all possible aspects. It's straightforward. If the device responds to SMS commands or if the LED indicators are functioning, then it is powered on and ready for operation.

</details>

<details>

<summary>A device is in a sleep mode</summary>

Many device models have a sleep mode feature. This mode is typically used to conserve the device's internal battery power or reduce data usage on SIM cards. There are various types of sleep modes available, ranging from disabling the GPS module but maintaining communication capabilities without sending accurate location coordinates, to a complete sleep mode where the device becomes non-responsive to SMS, internet commands, and other forms of interaction. In this mode, the device only wakes up at specific times or when certain conditions are met.

To start troubleshooting, the first step is to determine if the sleep mode is enabled on your device. Usually, this mode can be deactivated by shaking the device.

</details>

<details>

<summary>Tracking mode</summary>

Certain device models offer additional configuration options to operate in different modes. In such modes, the device may or may not send data based on various factors:

* If the device detects that it is roaming, it may switch to a different mode of sending data. However, this typically occurs less frequently.
* In parking mode, the device reduces data usage and activates eco mode. As a result, when the device is not in motion, it either does not send data or sends it very infrequently. It may only send heartbeat or TCP keep-alive packets.

However, for the initial connection, the platform needs to receive a correct packet containing GPS coordinates.

So, to ensure the device sends this information, a simple solution is to drive or walk around the neighborhood with the device. This will prompt the device to transmit the necessary data.

</details>

<details>

<summary>A device is installed in the wrong direction</summary>

The device being installed in the wrong direction can be viewed as a unique scenario within the previous context. Each device manufacturer provides specific guidelines for proper installation. If the device is installed incorrectly, it can result in various issues. One significant problem that arises during activation is that the accelerometer fails to detect the device's movement. For instance, if the device is mounted vertically instead of horizontally as intended, the device will be unable to recognize motion initiation and consequently won't transmit data to the platform.

To resolve this, it is necessary to reinstall the device in the correct orientation based on the manufacturer's recommendations.

</details>

<details>

<summary>A device is discharged</summary>

Sometimes, a device can become discharged, particularly with personal and cargo trackers. It may seem like the user has already attempted to power on the device using the button, but there is no response.

In such cases, the recommended course of action is to try charging the device and then attempt to turn it on again after a period of time.

For vehicle trackers specifically, it is essential to ensure that it is correctly connected to the car battery. Double-checking the wiring is crucial in this situation.

</details>

<details>

<summary>Incorrect device ID or phone number is specified on the platform</summary>

Some devices may have additional IDs aside from the IMEI. These IDs are sent to the platform in the initial login packet. If the ID in the login packet does not match the one specified on the platform, your device will not be registered, and its packets will be rejected.

* Double check the correctness of the specified ID.
* Make sure to verify that the specified IMEI matches the SIM card number you provided during the registration of the GPS device. It's understandable to make mistakes, especially when registering multiple devices simultaneously.
* Additionally, check the device settings for the ID sending option. Select the setting that sends the same ID you specified on the platform to ensure accurate registration.

</details>

<details>

<summary>A device cannot send valid GPS coordinates</summary>

For a device to establish a proper connection, it is essential for the platform to receive accurate coordinates in real-time. To ensure this, there are several factors that need to be verified:

* Confirm that the device is located in an area with reliable GPS coverage.
* Ensure that there isn't a substantial layer of metal obstructing the device's signal above its installation spot.
* Make sure that the device is installed outdoors, avoiding garages, buildings, tunnels, or areas under roofs.
* Check if the GPS antenna is properly connected on devices equipped with an external antenna.

If you haven't yet installed the device, it's recommended to take a short walk or a brief car ride with it to acquire initial coordinates. This will assist in establishing a successful and accurate connection.

</details>

<details>

<summary>Device's timezone is not UTC+0</summary>

While it may seem logical for the tracker to transmit data based on your current time zone, it actually doesn't. The platform follows a standardized format to receive data from devices and then translates the received time into your specified time zone in the user settings. If the device sends packets with timestamps different from UTC+0, it will not be able to activate properly on the platform.

To resolve this issue, ensure that the time zone of the device is set to UTC+0. This will ensure accurate synchronization between the device and the platform.

</details>

<details>

<summary>A custom password for SMS commands is set on a device</summary>

Some customers choose to set a custom password on SMS commands for their devices as an added security measure. While this is justified in certain scenarios, it can complicate the device's operation unnecessarily for regular vehicle monitoring. For intruders to change any settings, they would need to know the device model and the SIM card's phone number to send SMS commands.

If using a password is deemed necessary, it is recommended to only configure a device manually.

However, if using a password is not required, it is advised to remove the custom password and set a standard password instead. This will ensure that commands from the platform can successfully reach your device.

</details>

<details>

<summary>Mistakes in manually sent commands</summary>

Makes in manually sent commands are quite common and can cause issues. When manually activating a device, commands often contain multiple parameters. Even a single mistake in a comma or sign can render the entire command incorrect. Additionally, not every device model or SIM card can notify you of these errors. It is crucial to carefully check the commands you send to your device or the settings you have configured.

Among the important settings for establishing the initial connection of the device are the IP address, port, and APN settings.

* The APN setting is used to connect your SIM card to the internet. If it is entered incorrectly, the SIM card will be unable to connect to the network and start messaging.
* The IP address helps the platform determine which server the registered device should be located on. Upon device activation, the required address will be displayed in the user interface. However, if you activate the device manually, you can determine the address yourself. It's simple to identify: if the panel number starts with 10XXXXXX, the US server is used; otherwise, it is the EU server.
  * For the US server, the tracking address is either 13.52.37.2 or [tracker.us.navixy.com](http://tracker.us.navixy.com).
  * On the other hand, for the EU server, the tracking address is 52.57.1.136 or [tracker.navixy.com](http://tracker.navixy.com).
* The port selection determines the protocol used for decoding the packets of devices. You can find the appropriate port on [our website](https://www.navixy.com/devices/) in the supported devices section. Enter the name of your device in the search bar and open a model card to find the necessary port information.

</details>

<details>

<summary>A device may attempt to transfer data from its buffer</summary>

The platform is designed to accept data from the device buffer for a period of up to six months prior to registration. During this process, the device will display a blue "GPS not updated" status until it has unloaded all points from its memory.

If the device buffer holds data spanning more than six months, it will persist in attempting to send this data to the platform, but the platform will filter out these older data points. The device won't be activated on the platform until the historical data is within the six-month range.

* If the buffered data is unnecessary, you have the option to delete it. Doing so allows the device to immediately begin transmitting new and current data to the platform.
* Alternatively, you can also expedite the transmission of data from the buffer, allowing the device to come online as quickly as possible.

</details>

<details>

<summary>Firmware or device modification is not supported</summary>

Every device model and modification may have its own unique protocol or features not covered by the already integrated protocol. This can also apply to different firmware versions of a device. If the manufacturer alters certain specifics of how data is sent in messages, particularly those messages with a checksum, the platform may be unable to properly read or interpret it.

In such scenarios, you'll need assistance from the technical support team at [support@navixy.com](mailto:support@navixy.com). To streamline communication, it would be best to include both the manufacturer and the Navixy support team as recipients in your email. This way, you can avoid acting as a go-between, having to relay information back and forth between the teams.

</details>

## SIM card

Here we have collected all known cases with problems on the SIM card side. To solve them you need to contact your mobile operator.

<details>

<summary>A device is located in an area with weak GSM signal</summary>

If your device is situated in an area with a weak GSM signal, it may not be able to receive SMS commands for registration and, as a result, won't apply the new server settings required for sending data. Such a device typically doesn't recognize that it should be transmitting data to our platform. Generally, relocating the device to an area with a stronger signal is sufficient to resolve this issue.

Most GSM providers make it easy to determine the strength of the GSM signal. This information can often be found in your GSM provider’s personal account or upon request.

</details>

<details>

<summary>A device is located in an area with a weak mobile internet signal</summary>

If your device is situated in an area with a weak internet signal, it may not be able to transmit location data to the platform or establish any connection at all.

It's crucial to distinguish between different standards for internet and GSM data transmission. It's typically more challenging to acquire information on the strength of an internet signal compared to GSM. Sometimes, GSM strength is presented as proof of connectivity, but it's important to remember that a 100% GSM signal does not necessarily guarantee internet access at the device's location.

There's a possibility that the nearby base station only transmits GSM or that your service provider lacks an agreement to transmit internet traffic through other operators at the base station. It's also possible that the nearest base station doesn't support the necessary communication standard for data transmission. For instance, the base station might only transmit a 3G signal, while your device is limited to 2G.

There are two methods to verify this:

* Contact your GSM provider. Be sure to specify that you need information about the internet coverage in your specific location.
* Install a SIM card in your phone and check the network availability yourself.

</details>

<details>

<summary>SIM card does not have SMS feature available</summary>

This issue could arise if your SIM card isn't set up to receive SMS commands. This feature is crucial for the automatic activation of your device, as it relies on the SMS channel for the platform to send activation commands and connect your device to our server and port.

You should verify with your GSM provider whether your SIM card is enabled to receive SMS commands.

If you don't intend to use SMS commands, you can use the device configurator or the manufacturer's platform, such as FOTA Web, to set up your device.

</details>

<details>

<summary>SIM card does not have the mobile Internet data service activated</summary>

This situation is fairly common. It's possible that the SIM card you initially purchased isn't capable of transmitting data via the internet and is only designed for calls or SMS.

To resolve this issue, reach out to your GSM provider and request the activation of mobile internet data transfer services.

</details>

<details>

<summary>SIM card has run out of internet traffic</summary>

You may have used up all your internet data or paid megabytes before connecting your device to our platform. When this happens, your device won't be able to transmit data to the platform.

Another possible scenario is that you don't have sufficient funds in your account to automatically increase your data limit. Your plan's allotted data may have been exhausted, but without additional funds or an auto-renewal service, the data limit can't be increased.

To resolve this, get in touch with your GSM provider and inquire about the remaining internet data on your SIM card and the balance in your account. Also, verify if the auto-renewal service for your data limit is activated on your SIM card.

We recommend enabling this service, if available from your mobile operator, and setting up notifications about deductions to your email. This service will prevent such issues, and the notifications will help identify any devices with high data consumption due to misconfiguration.

</details>

<details>

<summary>SIM card is not activated</summary>

Certain SIM cards necessitate a specific activation process before they can be utilized. It's essential to consult with your mobile service provider for the appropriate steps to activate your SIM card.

</details>

<details>

<summary>SIM card is damaged or not installed correctly</summary>

If this issue wasn't a possibility, it wouldn't be included in this guide. Regrettably, we've come across such problems on several occasions.

Ensure that you've correctly installed the SIM card and didn't damage it during the process.

</details>

<details>

<summary>Sending data to our server IP is blocked by the ISP</summary>

This is a relatively uncommon scenario, but it can occur. For instance, we had an experience with a partner in a specific country where data couldn't be sent to our servers because our server's IP address needed to be whitelisted. This was due to their unique procedure for transmitting data to servers located in other countries.

We recommend that you confirm this aspect with your mobile service provider.

</details>

<details>

<summary>The communication standard on a device does not match the communication standard on a SIM card</summary>

This is one potential error you might encounter when attempting to connect your device to the platform. Every model and version of a device supports specific communication standards for data transmission. Some devices can support 2G and 3G, some only 2G, while others may only support 3G or LTE exclusively. If your SIM card isn't compatible with these communication standards, or if the nearby base station can't accommodate them, your device simply won't be able to communicate.

You can verify which communication standards are supported by different models [on our website](https://www.navixy.com/devices/), as we always strive to provide this information. Alternatively, you can check the supported standards on the device manufacturer's website or by contacting their support team.

</details>

<details>

<summary>Roaming</summary>

If your SIM card isn't M2M and doesn't have roaming activated, it won't function in foreign countries. Consequently, your device will be unable to receive commands or establish an initial connection to the platform.

If your devices regularly travel to other countries and you're using this type of SIM card for object tracking, enabling the roaming feature would be beneficial.

</details>

## Platform

Here, you'll find potential issues that could arise on the platform's end. If you've already checked all other possibilities and everything is set up correctly, the problem might be originating from the installed SMS gateway used for sending commands or in other settings of the platform.

<details>

<summary>A panel does not have an SMS gateway</summary>

If your panel's demo period has expired, you'll need to set up your own SMS gateway in order to use the automatic device activation feature. Without an SMS gateway, registration commands won't be sent from the platform.

You can refer [to our instructions](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/settings/messaging-gateways#sms-gateway) to understand the requirements for installing an SMS gateway on your panel.

</details>

<details>

<summary>SMS gateway does not support sending spaces at the beginning of a message</summary>

If your gateway does not support sending spaces at the beginning of messages, it will not be possible to activate Teltonika and Ruptela devices with its help.

This is due to the peculiarities of the commands of these devices, which require spaces at the beginning of each SMS command.

</details>

<details>

<summary>The "Do not send default settings" option is enabled in the panel settings</summary>

Take a look at your administrator panel settings. It's possible that you've previously activated a feature that prevents the platform from sending SMS commands to configure devices.

To resume sending commands, you'll need to deactivate this option in the Admin panel → Service preferences.

</details>

<details>

<summary>Insufficient funds in the user account where you activate the device</summary>

If the user account where you're activating the device has insufficient funds, this could be causing an issue.

Every user plan offers the option to either allow or forbid the sending of SMS messages, as well as setting the price for each message.

* If SMS sending is disabled on the user's plan, you can easily fix this by enabling it and setting a price. This price will be deducted from the user's account each time an SMS command is sent.
* If the user's balance is too low, advise them to top up their account on the platform.

</details>

<details>

<summary>Insufficient funds in your SMS gateway account</summary>

This issue occasionally arises when your SMS gateway account runs out of funds.

To resolve this, visit your SMS gateway's dashboard and add funds to your account.

</details>

<details>

<summary>Your SMS gateway cannot deliver message to a device</summary>

There could be several reasons for this issue:

* The device may be turned off, out of battery, or in sleep mode.
* The service for receiving and processing SMS messages might be disabled on the SIM card.
* The phone number could be incorrect, or your SMS gateway may prohibit sending messages to such numbers.
* There could be an error in the SMS gateway settings.
* Another type of error from the SMS gateway. Typically, gateways send us an error code which we then save in our logs.

</details>

If, after reviewing and addressing all potential issues mentioned above, your device still can't connect, please reach out to our technical support team at [support@navixy.com](mailto:support@navixy.com).
