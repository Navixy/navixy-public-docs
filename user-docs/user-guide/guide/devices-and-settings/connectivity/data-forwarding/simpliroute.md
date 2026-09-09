---
description: >-
  Forward vehicle positions from Navixy to SimpliRoute over HTTP, linking live
  location data to the delivery orders managed in SimpliRoute.
---

# SimpliRoute

SimpliRoute is a delivery route and order management service, and this protocol forwards vehicle positions from Navixy into it. SimpliRoute matches each position to the orders assigned to that vehicle, so dispatchers can follow deliveries against the planned route. It's used mainly in Latin America.

In Navixy, select **Simple Route** in the **Protocol** dropdown. That is how SimpliRoute is named in the interface.

## What Navixy sends

Navixy sends one HTTP POST request per position, with a JSON body containing these fields:

| Field | Contents |
| ----- | -------- |
| `idVehiculo` | The VIN of the vehicle linked to the device |
| `patent` | The registration number of the vehicle, shortened as described below |
| `empresaProveedoraGps` | The tax number from the account profile, identifying the GPS provider |
| `acc` | Whether the ignition is on |
| `latitude` and `longitude` | Position in degrees |
| `speed` | Speed |
| `datetime` | When the device recorded the position |

SimpliRoute responds with `GPS ok.` when it accepts a position.

{% hint style="warning" %}
Navixy shortens the registration number before sending it: it removes spaces and hyphens, then keeps the first six characters. A plate recorded as `ABC-1234` is sent as `ABC123`. Make sure the vehicle is registered in SimpliRoute under the shortened form, or SimpliRoute won't match the position to a vehicle.
{% endhint %}

### When Navixy sends

Navixy sends every message that has a valid position and is less than 15 days old. Messages without a position are skipped. So are messages older than 15 days, which a device can send after storing data offline for a long period.

External ID isn't used by this protocol. Navixy identifies the vehicle by its VIN and registration number instead.

## Before you start

This protocol reads vehicle and account data rather than taking it from the retranslator configuration. All three of the following must be in place, or the retranslator sends nothing at all:

* A **tax number** on the account profile. SimpliRoute uses it to identify the GPS provider.
* A **vehicle** linked to the device, created in [Fleet management](../../../fleet-management/vehicles.md).
* A **VIN** and a **registration number** on that vehicle.

Confirm all three before you create the retranslator. When any one is missing, forwarding fails silently: there's no error in the **Data forwarding** block, and no data reaches SimpliRoute.

You also need the endpoint address and port from SimpliRoute.

## Set up forwarding

{% stepper %}
{% step %}

### Check the vehicle and account data

Confirm the tax number on the account profile, and confirm that the device is linked to a vehicle that has both a VIN and a registration number.
{% endstep %}

{% step %}

### Open the Data forwarding block

Go to **Devices and settings**, select the device, then find the **Data forwarding** block.
{% endstep %}

{% step %}

### Open the protocol list

Click **Protocols**, then click **+** to add a configuration.
{% endstep %}

{% step %}

### Enter the Simple Route settings

Fill in the fields as follows:

* **Name**: a label that identifies this retranslator
* **Protocol**: **Simple Route**
* **Address**: the endpoint address from SimpliRoute
* **Port**: the port from SimpliRoute, commonly `443`

This protocol doesn't use a login or password. Leave both fields empty.
{% endstep %}

{% step %}

### Enable and save

Switch on **Enabled**, then click **Save**. A retranslator that isn't enabled sends nothing.
{% endstep %}

{% step %}

### Link the retranslator to a device

In the device's **Data forwarding** block, switch on the toggle for the retranslator you created, then click **Save**. Leave External ID empty, because this protocol doesn't use it.
{% endstep %}
{% endstepper %}

## Manage forwarding

To change or stop forwarding to SimpliRoute:

* Switch off the toggle in the **Data forwarding** block to stop forwarding for one device, and keep the configuration for the others.
* Click **Protocols**, select the row, and edit it to change the name, address, or port. The change applies to every device linked to this retranslator.
* Delete the configuration from the **Protocols** list to stop forwarding for all devices. Confirm in the dialog.

## Troubleshooting

When data doesn't appear in SimpliRoute, check the following in order:

1. The retranslator is enabled, and its toggle is switched on for the device.
2. The account profile has a tax number.
3. The device is linked to a vehicle, and that vehicle has both a VIN and a registration number. A missing value here stops forwarding without any visible error.
4. The vehicle is registered in SimpliRoute under the shortened registration number, with spaces and hyphens removed and only the first six characters kept.
5. The address and port are correct.
6. The device reports valid coordinates.
