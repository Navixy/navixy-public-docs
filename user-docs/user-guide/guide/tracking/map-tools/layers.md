---
description: Add custom KML map layers to display city districts, responsibility areas, specialized routes, or terrain data not available in the standard map views.
---

# Layers

Map layers allow you to add custom data overlays to your map, enhancing the visualization of specific areas not included in the standard map view. This feature is particularly useful for displaying unique geographic details, such as city districts, responsibility areas, custom routes, and other specialized maps. By using map layers, you can better understand the locations and situations of your fleet.

![Layers tool](../../../.gitbook/assets/image-20240807-205554.png)

## Practical application

Here are some of the ways you can use the map layers:

* Marking specific places and highlighting them graphically.
* Loading custom train, ship, or agricultural routes.
* Displaying specialized maps, such as terrain, ocean depth, or forest density, which are not available in the standard map list and are created by specialized companies.

## Creating KML layers

There are several instruments available for creating KML layers, ranging from professional GIS software to accessible online mapping services. **Google My Maps** and **Google Earth** are two user-friendly options for creating KML layers, while more advanced tools include **ArcGIS** and **QGIS**.

<details>

<summary>Creating KML layers with Google Earth</summary>

1. Open **Google Earth**.
2. Search for the location you want to map.
3. Use the top toolbar to select:
   1. **Placemark** for a point
   2. **Polygon** for an area
   3. **Path** for a route
4. Name your polygon, path, or placemark.
5. Set the colors and adjust transparency.
6. Outline the area by clicking the vertex points.
7. Click OK to finalize your work.
8. In the **Places** panel, right-click your place.
9. Select **Save Place As** and choose where to save the file.
10. Save the file as a KML.

</details>

## Adding map layers to the Navixy platform

To add a custom map layer, follow these steps:

1. Click the **Layers** tool in the upper-right corner of the map.
2. Click to add a new KML layer.
3. Enter the layer’s name, choose your KML file, and save it.

Once you have successfully added a map layer, it can be displayed by clicking the checkbox to the left of the layer’s name.
