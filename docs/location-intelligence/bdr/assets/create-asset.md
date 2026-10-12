---
description: >-
  How to create an asset: start from a preset
  asset type or build your own, then create an asset of that type and assign
  it a GPS device so it appears on the map.
tags:
  - guide
  - beta
---

# Create asset

Follow this guide to create an asset: a vehicle with a GPS device.

Every asset is based on an asset type. First you choose one of the preset types or build your own, then you create the asset based on it. This guide creates one vehicle based on the Navixy-provided Vehicle asset type and assigns it one device.

Before you start, you need a GPS device in your workspace. To add one, see [Device Management](../../device-management/README.md). You also need a role that lets you create assets, and to build your own asset type, a role that lets you create types. See [Access to Business Data Repository](../README.md#access-to-business-data-repository).

To create an asset, follow these steps:

{% stepper %}
{% step %}

## Choose or build an asset type

Use a preset type if it fits what you're tracking. Build your own if none of them do.

### Use a preset asset type

Every workspace starts with three preset asset types created by Navixy. This guide uses **Vehicle**, for cars, vans, trucks, trailers, buses, and equipment. The other two are **Workforce** for the people you track and **Cargo** for containers, pallets, parcels, and boxes.

The Vehicle type has a **Driver** field, where you assign a Workforce asset: the person driving the vehicle.

Vehicle, Workforce, and Cargo appear as tabs on the **Assets** page. Each works like any asset type you build yourself, so you can rename it and add [custom fields](README.md#custom-fields).

If one of them fits what you track, there is nothing to set up. Go on to the next step.

### Build your own asset type

If none of the preset types fit what you track, such as generators you rent out or boats in a marina, build your own type.

An asset type decides which properties (called [custom fields](README.md#custom-fields)) such assets will have. Every type starts with one built-in field, **Name**, which has to be filled in. The fields you add come after it. Every field you add is optional unless you mark it as **Required**.

To create the type, follow these steps:

1. Click **Assets** on the sidebar.
2. Click **Create asset type**. A panel called **Create asset type** opens.
3. Enter a **Name** for the type, for example, Generator. This name will be used for the type's tab on the **Assets** page.
4. Under **Custom fields**, click **Add field**.
5. Select **Device**. An asset can have a GPS device only if its type has a **Device** field.
6. In the field's name box, labelled **Device**, enter a name, for example, GPS device.
7. Click **Add field** again.
8. Select **Text**.
9. In the field's name box, labelled **Text**, enter a name, for example, Serial number.
10. Add any other field you want. All field types are described on the [Assets](README.md#custom-field-types) page.
11. Click **Save**.

Generator will appear as a tab on the **Assets** page.
{% endstep %}

{% step %}

## Create the asset

This step uses the Vehicle type. If you built your own type, click its tab instead and fill in the fields you gave it.

To create the vehicle and assign it a device, follow these steps:

1. Click **Assets** on the sidebar.
2. Click the **Vehicle** tab.
3. Click **Create asset**. A panel titled **Vehicle** opens.
4. Enter a **Name**, for example, the vehicle's fleet number.
5. In the **Vehicle kind** field, select the kind that fits, such as Van or Truck. Apart from the name, this is the only field you have to fill in.
6. Optional: enter the **License plate** and any other details you have. All the other fields are optional.
7. In the **Device** field, select your GPS device. If the device already belongs to another asset, Navixy asks whether to reassign it. Click **Confirm** to transfer the device to this vehicle.
8. Check that **Primary device** is turned on. The primary device is the one whose position becomes the vehicle's position.
9. Click **Save**.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You created a vehicle asset with a GPS device that reports its position. You can see it in the **Vehicle** tab of the **Assets** page and on the map in [Tracking](../../tracking/README.md). Every asset you create of this type will have the same fields.
{% endhint %}

## Next steps

- [Manage assets](manage-assets.md): Add a custom field later, group assets, and assign a device.
- [Assets](README.md): Learn how assets, asset types, and custom fields work, including the preset asset types and every custom field type.
