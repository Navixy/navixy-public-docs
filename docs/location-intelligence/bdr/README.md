---
description: >-
  Business Data Repository is where you manage your fleet in Location
  Intelligence: the vehicles, people, and cargo you track, the places that
  matter to them, and the hours they work.
tags:
  - overview
  - beta
noIndex: true
---

# Business Data Repository

A GPS device reports where it is and what its sensors read, but it doesn't know that it's fitted to a refrigerated van, who drives that van, or which depot the van returns to. Business Data Repository is where you store these facts, so the data your devices send is about your vehicles, people, and places rather than just hardware.

Business Data Repository is the Navixy product for your business data. It has three parts: **Assets** for what you track, **Geo objects** for the places that matter to it, and **Schedules** for its working hours. The menu has no entry called Business Data Repository. You open each part separately by clicking **Assets**, **Geo objects**, or **Schedules** on the sidebar.

## What you can do here

**Track your vehicles and people, not your devices.** Describe each vehicle, person, container, or something else as an asset and assign a GPS device to it. The asset takes the device's position, so the map in Tracking shows the van, not the device installed in it. When a device moves to another vehicle, you reassign it to that vehicle.

**Keep your own data on every object.** Add the fields your business works with, such as a license plate, a contact phone, or an inspection date. You add a field to the type once, and every object of that type will receive it.

**Mark the places and hours that matter.** Draw depots, customer sites, and delivery zones on the map. Describe shifts, opening hours, and service windows as schedules. Then assign them where they belong: a home depot to a van, working hours to a driver, opening hours to a depot.

## Who it is for

Business Data Repository is for the people who set up and run a fleet in Navixy: fleet managers, dispatchers, and the teams that prepare workspaces for their customers. It works closely with [Device Management](../device-management/) and [Tracking](../tracking/). You activate GPS devices in Device Management, assign them to assets here, and see the assets on the map in Tracking. An asset appears on the map only once it has a primary device.

## Core concepts

Assets are at the center. You assign devices, geo objects, and schedules to assets through the fields of their asset type. A geo object can have a schedule too, such as a depot's opening hours.

- **Asset:** Something your business tracks, such as a vehicle, a trailer, a person, or a container. It gets its position and readings from the GPS device assigned to it. A device can be assigned to one asset at a time.
- **Asset type:** The template an asset is based on, such as Vehicle or Generator. It decides which fields the asset has. Every workspace starts with three preset asset types, **Vehicle**, **Workforce**, and **Cargo**, and you can build your own.
- **Custom field:** A property of your own on an asset or a geo object, such as a VIN or an opening time. You add a field to a type, and every record of that type has it.
- **Asset group:** A set of assets you handle together, such as the vans of one depot. An asset has exactly one type and can be in any number of groups.
- **Geo object:** A place or an area drawn on the map, such as a depot, a customer's site, or a delivery zone. Like an asset, it is based on a type, which decides its fields. You create the geo object types you need.
- **Schedule:** When something happens and how often it repeats, such as a depot's opening hours, a vehicle's service window, or a team's shift. A schedule is made of time slots, and you can assign one schedule to any number of assets and geo objects.

## Access to Business Data Repository

Your role decides which records of Business Data Repository you can see and what you can do with them. A role sets this separately for each kind of record: assets, asset groups, geo objects, schedules, devices, inventories, and the types with their custom fields and tags. For each kind, a role gives access to all the records of the workspace, to some of them, or to none. It also allows some or all of these actions:

| Action | What it allows |
| --- | --- |
| View | See the records in lists and open them |
| Create | Create records |
| Edit | Change records |
| Delete | Delete records |

To edit or delete a record, your role must also let you view it.

When your role lets you see only some records, Business Data Repository leaves out the others everywhere:

* **Assets**, **Geo objects**, and **Schedules** list and count only the records that you can see.
* An asset group shows only the assets in it that you can see.
* An asset shows its GPS device only if you can also see the device.

The buttons that create, change, and delete records are shown to everyone. If your role doesn't allow the action, an error message appears when you click the button. To learn more, see [Access and roles](../access-and-roles.md).

## Where to start

Start with [Create asset](assets/create-asset.md). It takes you from choosing an asset type to a vehicle with a GPS device, which you'll then see on the map in [Tracking](../tracking/).

## Section contents

The documentation describes the three major parts of Business Data Repository: assets, geo objects, and schedules.

- [Assets](assets/): Learn how assets, asset types, custom fields, and asset groups work, including preset asset types and every custom field type.
  - [Create asset](assets/create-asset.md): Create your first asset from start to finish based on a preset or custom type.
  - [Manage assets](assets/manage-assets.md): View basic instructions for asset-related tasks, such as assigning a device, grouping assets, adding a custom field, tagging an asset, or deleting an asset.
- [Geo objects](geo-objects/): Learn how geo objects, their shapes, and their custom fields work and how a geo object is assigned to an asset.
  - [Create geo object](geo-objects/create-geo-object.md): Create your first geo object and assign it to an asset.
  - [Manage geo objects](geo-objects/manage-geo-objects.md): View basic instructions for tasks involving geo objects, such as entering a shape as GeoJSON, changing a shape, adding a custom field, tagging a geo object, or assigning it to an asset.
- [Schedules](schedules/): Learn how schedules, time slots, and repeat rules work, including every repeat option.
  - [Set up schedule](schedules/set-up-schedule.md): Create your first schedule and assign it to an asset.
  - [Manage schedules](schedules/manage-schedules.md): View basic instructions for schedule-related tasks, such as assigning a schedule to an asset or building a custom repeat rule.
