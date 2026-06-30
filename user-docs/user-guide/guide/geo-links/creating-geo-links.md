---
description: Create a geo link to share tracking data with external users. Configure the link's lifespan, visible objects, and map display options in the setup dialog.
---

# Creating geo links

To create a geo link, click **+** at the top of the page.

![New geo link button](../../.gitbook/assets/image-20250725-133824.png)

The creation dialogue window contains the following options:

![Creating a geo link dialog](../../.gitbook/assets/image-20250725-120004.png)

{% stepper %}
{% step %}

## Description (optional)

An information field that stores additional custom information about the link. The maximum length is 100 characters.
{% endstep %}

{% step %}

## Select objects and data

The list of objects available through the link. Each object has the following list of options to be specified upon adding the object:

**Object:** The object to be tracked.

**Label:** The specific label that is displayed in the geo link interface, overwriting the currently set label in the cabinet.

**Attributes:** The attributes of the GPS device to be displayed in the geo link interface. The attributes include:

- Speed
- Address
- Movement status
- Driver name
- Phone number
- Vehicle name
- License plate
- Connection status
- Sensor readings

{% hint style="info" %}
Sensor readings depend on the device’s model. To learn more about sensors and their types, see [Vehicle sensors](../devices-and-settings/vehicle-sensors/README.md).
{% endhint %}

Use the copy button to apply the same attribute list to all other objects in the geo link. This function may save a significant amount of time when configuring attributes for multiple objects.

![Copying attributes](../../.gitbook/assets/image-20250725-123905.png)
{% endstep %}

{% step %}

## Map settings

**Map provider:** Select the map that you want to show your users through the generated geo link. The list of maps is specified by the service provider.

**Trace history view**: The tracking period. This is what a trace might look like if it's set to 5 minutes:

![](https://www.navixy.com/wp-content/uploads/2024/04/5.png)

**Additional options:**

- **Autoscale:** The map auto-adjusts its zoom and position to accommodate multiple device movements.
- **Show icons**: Enables or disables icons for the object.

**Tracking options:** You can either show or hide the object location based on the geofence. For instance, if **Track outside geofence** is selected, objects are shown on the map only when they are outside of the selected geofences. This function may be useful for scenarios such as shipping or delivery, where the end user shouldn't see the asset being loaded with goods before departure. Respectively, **Track inside geofences** shows objects only when they are inside the selected geofences.

![](https://www.navixy.com/wp-content/uploads/2024/04/7.png)
{% endstep %}

{% step %}

## Show on map

Choose which stationary entities you want to show on the geo link’s map.

**POIs:** The list of Places available for showing on the map. To learn more about this feature, see [Places (POIs)](../tracking/map-tools/places-pois.md).

**Geofences:** The list of geofences available for showing on the map. To learn more about this feature, see [Geofences](../tracking/map-tools/geofences.md).

- **Limited validity duration**: Specify the link's lifetime. This can be quickly selected from predefined periods or set to a custom period of time. If the lifetime duration is set to start from a future moment, the link will remain inactive until that time arrives. Leave the option unselected for a permanent geo link configuration.
- **Preview**: Check what the configured geo link looks like from the geo link web interface from the end user's perspective. The preview feature enables quick swapping between the user and geo link interfaces for a more accurate representation of the geo link configuration.
  {% endstep %}

{% step %}

## Save geo link

Click **Create** to open a pop-up dialogue with the generated link. The link can be copied and provided to end users or shared via the social network buttons:

![](https://www.navixy.com/wp-content/uploads/2024/04/9-1.png)
{% endstep %}
{% endstepper %}
