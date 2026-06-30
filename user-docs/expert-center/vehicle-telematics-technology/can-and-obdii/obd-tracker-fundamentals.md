---
description: Plug-and-play OBD devices connect to the vehicle diagnostic port to read engine data without extra wiring. Covers data types and fleet management use cases.
---

# OBD device fundamentals

In this guide, you’ll learn what an OBD device is, how these devices work, and what makes them suitable for fleets, small businesses, and others.

Tracking providers have a number of options to consider when choosing a GPS device. OBD devices (also referred to as OBD2 or OBDII) are an option that are affordable, convenient, and accessible for a variety of users. These plug-and-play devices are simple to install and provide fleets with rich vehicle data, helping them take advantage of intelligent telematics.

In this document, we’ll explain how these devices work, what functions they provide, and how they’re used. Navixy supports OBD devices from a multitude of manufacturers and with over 15 years of experience and hundreds of partners worldwide, we have significant knowledge of the telematics and fleet management industry.

![OBD port location](../../.gitbook/assets/obdii-connector-location-2.png)

OBD ports are typically found within the shaded area

## Where can OBD devices be used?

As stated, an OBD device is any GPS tracking device that plugs into the vehicle’s OBDII (On-Board Diagnostic) port. Practically every passenger vehicle sold since 1996 comes with this standardized port, meaning these devices are compatible with just about every car, truck, SUV, or van available. Along with providing GPS location, they can also give fleets access to valuable vehicle data, DTC codes, DPA (Driving Pattern Analysis), and VIN.

![Example of OBD device](../../.gitbook/assets/device-tracker.png)

Example of OBD device

## What is the OBD standard?

The OBD standard was originally established to improve emission control standards. Though the earliest version of the technology was introduced by Volkswagen in the 1960s, the OBDII standard started becoming mandatory in various countries in the mid 1990s, and has expanded to much of the world since.

Along with the type of diagnostic connector and its pinout, the OBDII standard also specifies the electrical signaling protocols available and the messaging format, as well as which vehicle parameters to monitor and how to encode this data.

The OBD port is also commonly available in electric vehicles, although some manufacturers consider replacing it in the future, partly because emission control is not applicable to EVs.

## Pinouts

The OBDII pinout includes 16 pins, each of which communicates with a specific protocol. Though some pins are standardized, others are optional, such as those designed for a specific manufacturer or vendor. The table below describes the corresponding protocol for each pin. Note: Any unlabeled pin is open for use by OEMs.

![OBDII pinouts](../../.gitbook/assets/obd11-graphic.png)

OBDII pinouts

### CAN High/Low

The CAN Bus high and low lines transmit vehicle data to the device. In idle mode, both lines carry 2.5V. However, since communication requires a voltage differential, the high line goes up to 3.75V and the low goes down to 1.25V to generate a 2.5V differential whenever data is being sent. Therefore, CAN bus is a reliable choice for networked communication on mobile devices.

### K-Line

Primarily, the K-Line is used for vehicle diagnostics. It was the original protocol used for this purpose by the OBDII standard until CAN bus became mandatory in 2008. Now, it’s mostly used with older vehicles and in markets with less advanced technology.

### J1850

Available in two varieties, J1850 is another standardized protocol that has since been mostly replaced by newer technology. The first variety, Pulse Width Modulated (PWM), is typically found in older Ford vehicles, while Variable Pulse Width (VPM) appears in General Motors models.

### J1939

While the OBD standard covers passenger and light vehicles, the SAE J1939 protocol establishes the standards for how devices communicate in heavy-duty vehicles. However, unlike OBDII, J1939 is standardized, so all manufacturers use a common language.

Below, you can see how the number of pins and their arrangement differs for this protocol.

![J1939 pinouts](../../.gitbook/assets/heavy-duty-vehicles-graph.png)

J1939 pinouts

### J1708

One protocol that appears in J1939 but not OBDII is J1708 (CAN 2 high/low). Although this is another older protocol that’s since been replaced by CAN bus readings, it’s still included in J1939 to support older heavy-duty trucks. This protocol is capable of reporting parameters such as fuel level, absolute fuel consumption, and engine hours.

## What’s the difference between OBD and hardwired devices?

When selecting a GPS device, people can choose between portable and powered devices. While the former require an internal battery and have a limited lifespan, the latter get power from the vehicle’s battery. This group can also be either a hardwired or an OBD device.

The primary difference between an OBD GPS tracking device and a hardwired one comes down to installation. Hardwired devices generally require professional installation since they need to be connected to the vehicle’s battery through the fuse box for power. In comparison, an OBD device plugs directly into the OBDII port, meaning installation can be completed by anyone in seconds. This is also why OBD2 devices are often referred to as "plug-and-play" devices.

In addition to ease of installation, OBD tracking devices also transmit rich vehicle data since they can directly access the CAN bus. This allows usage-based insurance, rental car companies, and corporate fleets to receive such information such as engine hours and fuel consumption.

Finally, since professional installation isn’t required, OBD devices can often be more affordable than other devices in the long run.

## Why do people choose OBD devices over other devices?

There are several reasons to choose an OBD device over a different type of GPS device, including ease of installation and affordability. In addition to convenience, they also provide access to a wide array of vehicle data points.

As discussed above, OBD2 devices are extremely easy to install, making them suitable choices for customers who don’t have experience with GPS devices or a car’s wiring system. As a result, deploying an advanced telematics solution is possible for a wide range of end users. Furthermore, since no professional installation is required, OBD devices can be a more affordable option for small businesses and fleets.

Despite their ease of installation, OBD tracking devices still deliver useful telemetric data just like their hardwired counterparts, including vehicle location and speed, asset history, alerts, and more. Because of this, they’re often deployed by those who need a convenient, affordable telematics solution, such as company-owned fleets, contractor vehicles, taxis, and even personal cars.

## Common capabilities of OBD devices

Although certain functions vary from device to device, GPS tracking providers can expect to access a host of capabilities with OBD devices.

### GPS tracking

Every common OBD2 device on the market transmits [GPS location](https://www.navixy.com/asset-gps-tracking/features/). Depending on the device settings and use case, this could simply mean updated GPS coordinates on demand or live tracking on the map.

To provide better accuracy, as well as faster and more reliable positioning, modern OBD devices support advanced technologies including multiple GNSS networks, Assisted GPS, LBS / Cell ID, and better sensitivity thanks to newer GPS modules or dual orthogonal GPS antennas inside the device.

### CAN bus information

One of the main benefits of an OBD device is its direct connection to CAN bus data. This not only provides access to fuel, engine, coolant, battery, and other parameters, but also vehicle identification number (VIN), malfunction indicator (MIL), and DTC codes for diagnostics. Basically, what a driver sees on the dashboard may be sent to the cloud to further improve [fleet management](https://www.navixy.com/fleet-management/features/) operations.

### Geofences

Many businesses use [geofences](https://www.navixy.com/blog/improve-fleet-oversight-with-navixy-geofences/) to monitor assets and prevent misuse, such as employees using vehicles for personal errands or off the clock. In addition, auto geofencing (unauthorized movement) can tell if a vehicle is being towed from its location when the ignition is off.

### Driver behavior monitoring

Many fleets are concerned with safety, which is why OBD devices are often used to improve driver behavior behind the wheel. For instance, many of these devices can detect harsh braking events, which a fleet manager can use to both monitor vehicle wear and determine which drivers have the most violations. Certain OBD devices are even capable of monitoring speed and detecting crashes for additional alerts.

Navixy’s [Eco Driving](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/fleet-management/eco-driving) system offers adjustable penalty settings for speeding and harsh driving to help companies score employees on safety. They can then address which drivers need coaching in which areas.

### Harsh driving

To help monitor driver behavior, certain OBD devices include gyroscopes and accelerometers to detect harsh driving events like hard braking and acceleration. In addition to alerting the manager or dispatcher through the platform, the device may also emit an audible alert to let the driver know of the infraction.

### Speed monitoring

Similar to harsh driving alerts, speed monitoring can be used to improve driver safety. Managers can set speed thresholds that trigger an alert if exceeded, so they can take corrective action. Since the OBD device tracks engine RPMs via the CAN bus, it can detect spikes and report to the manager.

### Crash detection

Some OBD devices also have the ability to tell when a collision occurs thanks to the inclusion of an accelerometer or G-sensor.

### Fuel monitoring

Excessive [fuel usage](https://www.navixy.com/blog/remote-fuel-level-monitoring/) is one of the most common ways in which fleets waste money. OBDII devices make tracking and monitoring fuel levels easy and convenient so businesses can identify fuel-wasting problems such as frequent engine idling, leaks, theft, and other issues.

### Fuel level

OBD devices can communicate with equipment that monitors vehicle [fuel levels](../fuel-management/installation-and-initial-configuration-of-fuel-control-devices/fuel-level-sensors/), including capacitive, resistive, and sensors. A drastic change in level could indicate potential theft, giving a company more time to take action.

### Fuel consumption

In addition to identifying theft and leaks, fuel monitoring can also provide insight into fleet efficiency by tracking fuel consumption. Companies can monitor inefficiencies by pairing this function with the excessive idling alert to determine which vehicles are the most wasteful.

### Events related to fuel

As mentioned, OBD devices can monitor for several fuel-related events. Along with theft attempts and excessive engine idling, it’s possible to detect harsh acceleration, another common waste of fuel.

### Driver ID

Fleets of all sizes often choose to secure their vehicles by requiring drivers to present identification prior to starting the engine. Without the correct button, key, or tag, the vehicle won’t turn on, ensuring that only authorized individuals are able to drive away. To support this function, certain OBD devices have BLE sensors to communicate with Bluetooth beacons.

### Bluetooth

[Bluetooth](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/bluetooth-sensors-block) support connects OBD devices with a multitude of auxiliary devices. In addition to driver ID, BLE beacons can include temperature sensors, door open/close status, and tire pressure monitors, to name a few. Combining these wireless devices with the convenience of an OBD2 device means having a robust telematics system without costly or complicated installation.

### Voice monitoring

Even basic OBD tracking devices often provide voice monitoring technology via SMS control. After sending a message to the device, a person can hear what’s happening in the car through the unit. This way, it’s possible to check in with a driver who might be in danger.

## Common OBD device applications

OBD devices are used in a considerable variety of ways in different industries, so we’ll focus on a few of the most popular applications.

### Live tracking/Asset location

For many users, being able to reliably track a car’s location is a must. With the help of an OBD device, it’s easy to know exactly where your vehicles are at any time. As a result, they can be used for business and personal purposes.

### Business use

Both small and large businesses use OBD devices to monitor and improve productivity. For example, they can see where employees or contractors are and assign tasks based on their location, or accurately track mileage for reimbursements if a personal car is used for work. OBD devices are also useful for observing fuel usage and identifying wasteful practices like engine idling.

### Personal use

OBD devices are easy for people to use in their own vehicles. Parents who want to ensure that teenagers are safe can use an OBD device to track harsh driving events like speeding and hard braking. In addition, they can be used to find someone’s location if they can’t be reached in case of an emergency.

### Usage-based billing

For more accurate invoicing, many companies are turning to [usage-based billing](https://www.navixy.com/blog/how-usage-based-pricing-diversifies-iot-revenue-streams-for-telematics-companies/). Doing so can help ensure that customers are only charged for the amount a certain asset was used. For instance, pay-as-you-drive businesses use OBD devices to determine exactly how many miles a customer drove, as well as how much fuel was used and if any harsh driving violations occurred.

### Lease/rental fleets

Since OBD devices are easily deployed in passenger vehicles, car leasing and rental companies often use them for tracking large fleets. This gives them complete oversight over which vehicles are in use, where they are, and who’s driving them.

### Fuel monitoring

To help businesses understand exact vehicle fuel usage, OBD2 devices typically offer fuel monitoring capabilities. With reports and alerts, fleet managers can gain insight into what might be causing excessive usage, in addition to monitoring for potential fuel theft or leaks.

### Maintenance

Keeping a fleet of cars, trucks, or vans in top condition requires staying on top of [maintenance](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/fleet-management/maintenance). Since OBD devices have access to CAN bus data, they can transmit key information like tire pressure, engine hours, RPMs, and more. This makes it possible to track when services like oil changes and tire rotations should be performed. By keeping vehicles in better condition, you can expect improved reliability and reduced repair costs.

## What are the different types of OBD devices?

Although OBD devices have the same general design and function, different devices may vary when it comes to whether or not they can actually read OBDII data, are for cars or heavy machinery, and if they require an extension cable.

### Read OBD data vs not

Some users may not be aware that some OBD tracking devices can’t read vehicle info via the CAN bus. This means that you won’t be able to view fuel levels, driving events, engine data, and more. However, if you don’t need this information to satisfy your needs, then this type of device could still be a suitable fit.

### Cars vs heavy vehicles

Whereas passenger vehicles like cars, trucks, and vans all use the OBDII standard, larger vehicles and machinery have a different protocol for accessing vehicle data—J1939. As a result, you’ll need a special type of adapter and cable for the OBDII device. Fortunately, since these machines still have readable CAN bus data, you’ll be able to access all the information you would with a regular car or truck.

### OBD accessories

In addition to the aforementioned adapters for J1939, OBD devices can also come with a number of other accessories to support their function. For instance, if the OBDII port in your vehicle is in a hard-to-reach area, you can use an OBD extension cable to access it and securely install the device. Alternatively, if you need to run tests in a controlled setting, the female OBD power cable lets you plug in and perform diagnostics wherever there’s a 10-30V DC power supply.

## Typical specifications of OBD devices

Depending on the application, you’ll want to know the physical characteristics of the OBD device you’re considering, such as its size, weight, and what it’s made of. Depending on the application, you could also account for its IP rating and battery life.

### Size

Though there’s no standard size for an OBD device, they’re typically about 50 x 50 x 25mm or 2 x 2 x 1 inches.

It’s crucial for these devices to be small and discrete. This not only prevents them from being removed intentionally but also so users don’t bump and dislodge them by mistake. Moreover, some OBDII ports are in tight spaces, meaning devices must be small enough to fit wherever they might be located.

### Weight

Similar to OBD device sizes, these devices typically weigh within the same range—approximately 40 to 70 grams, or 1.4 to 2.5 ounces.

### IP rating

If an OBD device needs to be used in an industry with challenging environmental conditions, such as mining or logging, then it needs the right IP rating. This rating describes the device’s ability to withstand both water and physical contaminants, like dust. The first digit represents resistance to solids on a scale of 0 to 6, while the second rates water resistance from 0 to 9, with 0 meaning no protection.

Most OBD devices have an IP41 rating, which is sufficient for most personal and business uses. For greater resistance against moisture and dirt, look for an OBD device with an IP67 rating. This could be necessary at a quarry or mine where dust is constantly present.

### Battery

Most OBD devices come with a power supply and backup battery, but generally, the device will draw power from the vehicle whenever the ignition is on. As a result, they can often be used for long periods of time and battery capacity isn’t an issue. However, the device will continue to draw some power even when the ignition is off, so keep this in mind for vehicles that might sit idle for weeks or months at a time.

## Drawbacks of using an OBD device

Despite the many advantages of using an OBD device, there are still a few drawbacks to consider. Be sure to keep these potential problems in mind when choosing a GPS tracking device.

### Easy to steal/uninstall

Unfortunately, one of the top benefits of an OBD device, easy installation, is also a disadvantage. Since the device is often out in the open, it’s easy for someone to simply remove it if they don’t want to be tracked. Moreover, a person may even accidentally disturb the device if it’s in an area where it can be easily bumped.

To prevent intentional or unintentional device removal, the OBD device can be sealed away or stowed out of sight with the help of an extension cable. Furthermore, many OBD2 devices have removal alerts, so if someone does unplug it, an alert will be sent to the platform.

### Drains car battery

Since OBDII tracking devices draw power from the vehicle, they also take it from the battery. Naturally, many are concerned about the device draining the battery but typically this isn’t the case in the short-term since most devices go into low-power sleep mode when the ignition is off. However, if vehicle battery life is a concern, you can check the device’s specifications to learn about its power consumption.

### Data hacking

In recent years, several companies have been found to have OBDII devices that are vulnerable to external attacks. Under certain conditions, a hacker could potentially access a car’s computer and tell it to brake or accelerate independent of your command. Primarily, this is the result of poor system administration, including unencrypted networks.

## What is certification and what does it entail?

GPS device certification documents describe which carriers a device can operate with. They also show that a device is compliant with various health and safety review organizations. Different countries and regions have different laws regarding compliance, so it’s important that the OBD device you choose is certified where you live. You can check with the manufacturer for a device’s complete certification.

## Navixy-supported OBD devices

Learn more about our supported OBD devices by visiting our dedicated OBD device page.
