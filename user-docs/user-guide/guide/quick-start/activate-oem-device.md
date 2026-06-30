---
description: >-
  Connect a factory-installed vehicle telematics unit to Navixy without
  additional GPS hardware. Follow the OEM activation steps to begin tracking.
---

# Activate OEM device

Modern vehicles from manufacturers such as Ford come equipped with factory-installed telematics modules that continuously collect location and vehicle health data. Navixy can connect to these built-in systems directly, so you get full fleet visibility without touching the vehicles.

## Why use OEM telematics

Compared to installing aftermarket GPS devices, connecting via an OEM integration offers three practical advantages:

* **No hardware installation.** The telematics module is already in the vehicle from the factory. There is nothing to fit, wire, or configure physically.
* **Richer vehicle data.** OEM modules report data that aftermarket devices typically can't access, including fuel level, engine diagnostics, odometer readings, and other vehicle health signals available through the manufacturer's system.
* **Lower total cost.** Eliminating hardware procurement and installation reduces both upfront spend and ongoing maintenance effort.

## How it works

OEM-connected vehicles send data from the manufacturer's connected-vehicle system to Navixy through a feed set up by your service provider. This means the activation process has two stages: your service provider prepares the data feed, then you add the vehicle in Navixy using the standard activation wizard.

{% hint style="info" %}
Ford vehicles with the FordPass Connect modem are currently supported. Support for other OEM manufacturers is available on request. Contact your service provider to find out whether your vehicle make can be integrated.
{% endhint %}

## Before you begin

You need the following credentials to hand when you contact your service provider. These are issued by the vehicle manufacturer or their API program administrator:

| Credential                 | Description                                                |
| -------------------------- | ---------------------------------------------------------- |
| Token endpoint             | URL used to obtain an access token for the OEM API         |
| Client ID                  | Identifier assigned to your application by the OEM         |
| Client secret              | Secret key paired with the client ID                       |
| gRPC Feed Service endpoint | Address of the gRPC service that streams vehicle data      |
| Feed flow name             | Name of the specific data feed configured for your account |

If you don't have these credentials, contact your OEM program administrator before proceeding.

## Activation steps

{% stepper %}
{% step %}
### Contact your service provider

Send your service provider the five credentials listed above and ask them to configure the OEM data feed for your account. This step happens entirely on their side, so you don't need to interact with the vehicle or Navixy yet.

Wait for your service provider to confirm that the feed is active before continuing.
{% endstep %}

{% step %}
### Add the vehicle in Navixy

Once your service provider confirms the feed is ready, add the vehicle using the standard activation wizard:

1. Go to **Device activation** in the left sidebar.
2. Search for and select your vehicle model (e.g., Ford with FordPass Connect).
3. Enter a descriptive **Label** for the vehicle (e.g., "Ford Transit, Plate XYZ").
4. Assign the vehicle to a **Group** if applicable.
5. Enter the **Device ID** as instructed in the activation dialog for your specific model.
6. Click **Activate**.

For full details on each wizard step, see [Activate GPS device automatically](activate-gps-device.md#activate-gps-device-automatically).
{% endstep %}

{% step %}
### Verify the connection

Within about 15 minutes of activation, the vehicle should appear online in Navixy with its current location and available vehicle data.

{% hint style="success" %}
The vehicle is active when its status shows as online in the Tracking section and vehicle data such as location and fuel level begins populating.
{% endhint %}

If the vehicle doesn't come online after 15 minutes, contact your service provider to verify the feed configuration.
{% endstep %}
{% endstepper %}
