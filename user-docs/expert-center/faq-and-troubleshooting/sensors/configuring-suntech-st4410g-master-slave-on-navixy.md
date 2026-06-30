---
description: Set up Suntech ST4410G master-slave redundancy in Navixy. Covers slave mapping, distance monitoring parameters, and alert integration for asset security.
---

# Configuring Suntech ST4410G master-slave in Navixy

The Suntech ST4410G is a compact, battery-powered GPS tracker built for tough conditions. Running on CAT M1 with 2G fallback and equipped with a long-lasting lithium battery, it's made for high-risk environments where flexibility and stealth matter. Navixy users can easily benefit from this device's capabilities, the ST4410G is fully integrated into the Navixy platform, ensuring stable data flow and comprehensive management capabilities for users.

The most outstanding feature of the ST4410G is its master-slave redundancy, combined with Navixy's robust alert system, provides an unparalleled layer of security and operational continuity.

In this document, you'll learn the main concept of the master-slave feature and how to set up alerts for it within the Navixy platform.

## Configuring master-slave system for reports and alerts: Key parameters in Syntrack and Navixy

To ensure the effectiveness of the master-slave configuration, it's important to set up several key parameters. These settings help control the timing of device interactions and alert generation.

The configuration is managed through the Syntrack tool, making it simple to tailor the system to your business needs. Furthermore, Navixy integration ensures accurate visualization and performance on the platform, providing useful alerts, helping the customer to act if any event occurs.

### Timeout interval

The timeout interval determines how long the master device will wait before considering a slave device as inactive or missing. This setting is crucial for preventing false alarms. For instance, if a slave device temporarily falls out of range but is expected to return shortly, the timeout interval will prevent the system from sending an alert prematurely.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfk8fVlzJ8KomhVMibxxDpfeMEYTvTp4SjlAMkFqKObHv9MMhffyRwJ6gjyGO3Z0m6k9NaJH9bcBPrLpPY4DZ94mVSlpmUT9zirSkqL1MHqwYfRVLS6TKv3r1qPSglmA8KVZwor?key=tadSe0dC1rnchCyfbf2IlA)

### PMR (Presence Message Request) time interval

The PMR time interval dictates how frequently the master device sends requests to the slave devices for presence updates. These requests include GPS information, and the slave devices respond with their status. Proper configuration of the PMR interval ensures that the master device stays synchronized with the slave devices, keeping their status continuously updated.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXetTC-gNFDNmZRuRzpgtsHppGOeoSewzwCFD1D7EoCpgLoyfuACm5yin7kbTgfeyAPDwGPZ3IzEmR9lyu6O7GbDXiyDi-cmUSpc2TL93BWc3PkBwRzfkraoOaljtytfXl3p6aDW?key=tadSe0dC1rnchCyfbf2IlA)

### Mapping slave devices to the master on Syntrack

Once the time parameters are set, the next step is to map the slave devices to the master. This process is carried out using the List of Present Devices function in the Syntrack tool. The user simply enters the 9-digit serial number of each slave device (entered from right to left), which creates a list of all the devices the master is monitoring.

Once the mapping is complete, the master device can track the real-time presence of each slave device, enabling continuous monitoring of all assets.

### Optimizing range and detection

For optimal performance, it's crucial to maintain the master-slave devices within an ideal communication range of 80 to 150 meters. This range ensures reliable communication between devices and minimizes the chance of false alerts. Staying within this range guarantees that the system will accurately detect the presence of slave devices and respond swiftly to any changes.

However, several factors may affect performance, such as when a slave device is placed inside a box or container that is later manipulated. In these cases, the system must quickly detect and alert the user about the absence or tampering of the slave device.

Now when the device is configured properly, it's time to provide additional value to it with the help of Navixy. The platform provides several software alerts that can be of great help in boosting the performance of the device.

### Additional alerts in Navixy: Distance monitoring between master devices

An additional feature offered through Navixy is the ability to create alerts based on the distance between two or more master devices. If multiple master devices are tracking the same set of assets, businesses can set up alerts to notify them if the devices move beyond a predefined distance from each other.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXebVwcAe_iy8QpvtvY2dryZM6MZ89jpbxua45YnnQMUB5zlbCqJ2KNi0-F_NibHDh8RsW9ciFDxe2XEudtiP7eKOsNgz5MwPmiH8Wmp6FgGup_8V0f14dTwVAWLROe47OL32ukDQA?key=tadSe0dC1rnchCyfbf2IlA)

This feature is especially useful for monitoring goods and vehicles, as it helps detect when assets are separated or when tampering occurs. By setting distance-based alerts, businesses gain an extra layer of security, ensuring that any unauthorized movement or separation of assets is immediately flagged.

## Setting up presence and tampering alerts in Navixy

No need to say that when you're managing valuable goods in transit, knowing the moment something goes wrong can make all the difference. That's where the master-slave configuration we've just talked about, paired with Navixy's alert system, really comes into play, allowing you to get instant notifications if anything goes out of the ordinary.

Navixy makes this easier. It lets you set up smart alerts tied to the master's monitoring activity. If a slave unit is detached or something seems off, the system quickly notifies you. No guesswork, just timely updates that help protect your assets.

### How to create presence alerts that matter

A key strength of the ST4410G GPS device is its ability to detect when one of its connected slave devices goes missing, or comes back online. That capability, combined with Navixy's alert features, gives businesses a powerful way to stay ahead of potential issues.

Setting up these alerts is straightforward:

1. Open the **Alerts** module in Navixy.
2. Create a rule for the specific event you care about, like a device going missing.
3. Select the master device and choose the **State field value** option.
4. Add the condition (e.g., "present_device_absent" or "absent_device_recovered") and enter the 9-digit serial number of the slave unit.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXesAk9lNzokfS4caOKZ3ptr4M-ctro2G_8Ebn7X9L438-zK9wKlWeg23xJvGTnuUiYUrBlm0Vc4yQrGghx2WtZ_5FMEvqBYF0FdLUWuX1AX94uKUAPMn1g25EgQtk-XtvW35kmp_A?key=tadSe0dC1rnchCyfbf2IlA)

With that in place, you'll be notified as soon as anything unexpected happens, whether it's a disconnection, tampering, or movement that shouldn't be happening.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdaD0R3YWew_0fASmCOXFp9PcTAdinJsLzIvA96LrLqCakCE0Xlb-PbP1mGEosdjiFo4mXM9kgDq4ZglOl6jT1uSg5lnUDK_Clidji_VaNeiuSmHkEULcaTZzzpmGNt54LqIbs0Lw?key=tadSe0dC1rnchCyfbf2IlA)

Managing alerts in a growing fleet shouldn't mean more manual work. Whether you're switching out devices often or dealing with complex conditions in the field, Navixy gives you two powerful ways to stay ahead:

- [Navixy API tools](https://www.navixy.com/docs/developers) help you automate at scale.
- [IoT Logic](https://www.navixy.com/iot-logic/) offers flexibility for complex scenarios.

## Final thoughts## 

Navixy enhances this capability by enabling distance-based alerts between multiple master devices, adding another critical layer of security for tracking grouped assets. The platform's intuitive alert system allows for straightforward configuration of presence and tampering notifications, leveraging specific state field values like "present_device_absent" or "absent_device_recovered." This not only a simple process of receiving immediate notifications for unexpected events but also highlights Navixy's commitment to proactive security. The ST4410G's advanced master-slave functionality, seamlessly integrated and configurable within Navixy's versatile environment, including support for API tools and IoT Logic for complex scenarios.
