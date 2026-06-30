---
description: Manage GPS device configuration in Navixy through settings blocks for tracking, sensors, connectivity, driver safety, tachograph, and hardware controls.
---

# Devices and settings

**Devices and settings** is where you configure each tracking device in your fleet. You pick a device from the list on the left, then adjust its behavior through a set of **settings blocks**: collapsible panels that each control one aspect of the device.

This section is for **fleet managers and administrators** who set up how devices report, what their sensors mean, when alerts fire, and how data flows to other systems. Use the cards below to jump to the group that matches your task.

{% include "../../.gitbook/includes/device-limit-hint.md" %}

## How settings blocks work

* **Pick a device, expand a block, edit, and save.** Each block has its own save indicator. Some blocks offer a **Reset to defaults** button.
* **Not every block appears for every device.** The blocks you see depend on your **device model's hardware** (for example, a phone block only on devices with a SIM), your **plan** (some blocks require a plan feature), and your **permissions** (without edit rights, blocks appear read-only). Throughout this section, each block is described as _"appears if your device or plan supports it."_
* **Copy settings to other devices.** Many blocks let you apply their settings to one or more other devices at once, usually limited to compatible (same-model) devices.

{% hint style="warning" %}
Copying a block's settings **overwrites** the target devices' current settings for that block. **Blocked devices** show only the Phone number block, and nothing else is editable until the device is unblocked.
{% endhint %}

## Explore the section

<table data-card-size="large" data-view="cards"><thead><tr><th>Group</th><th>What it covers</th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><h4>Managing devices and groups</h4></td><td>Search, filter, and organize the device list with tags and groups.</td><td><a href="managing-devices-and-groups.md">managing-devices-and-groups.md</a></td></tr><tr><td><h4>Object management</h4></td><td>Device identity and admin: label, group, tags, SIM, plan, statuses, and commands.</td><td><a href="object-management/">object-management</a></td></tr><tr><td><h4>Location and movement</h4></td><td>How the device reports position: tracking, parking, odometer, ignition, and towing.</td><td><a href="location-and-movement/">location-and-movement</a></td></tr><tr><td><h4>Vehicle sensors</h4></td><td>Turn raw inputs into fuel, temperature, ignition, and other sensor values.</td><td><a href="vehicle-sensors/">vehicle-sensors</a></td></tr><tr><td><h4>Connectivity</h4></td><td>Offline timeout and data forwarding to third-party servers.</td><td><a href="connectivity/">connectivity</a></td></tr><tr><td><h4>Driver safety</h4></td><td>Detect and report harsh acceleration, braking, and cornering.</td><td><a href="driver-safety/">driver-safety</a></td></tr><tr><td><h4>Tachograph and compliance</h4></td><td>Download and email digital tachograph DDD files.</td><td><a href="tachograph-and-compliance/">tachograph-and-compliance</a></td></tr><tr><td><h4>Device-specific controls</h4></td><td>Hardware-specific blocks that appear only on supported devices.</td><td><a href="device-specific-controls/">device-specific-controls</a></td></tr></tbody></table>
