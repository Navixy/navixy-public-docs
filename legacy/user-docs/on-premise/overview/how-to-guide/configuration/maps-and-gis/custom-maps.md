---
description: Add custom map layers to Navixy from tile servers or SVG files. Use for restricted area cartography or custom routes not available in standard map providers.
---

# Custom maps

You can add your own custom map layers to Navixy platform, which can be particularly useful for tracking vehicles in restricted areas that are blurred or not displayed on publicly available maps, or for adding custom routes that do not exist on other maps (e.g. marine routes).

There are two types of layers that can be added to Navixy:

* Layer from a tile server
* Layer from an SVG file

## Adding a tile server as custom map layer

Once the tile server is up and running with the cartographic data is uploaded, you can easily add new map layers into the Navixy interface. You will need to enable the appropriate plugin for external cartography and provide it with the URL to your tile server. You can also restrict access to the new map layer to certain user accounts.

Once the plugin is activated, the new layer will appear in the list of available layers in the user web interface and/or mobile apps. Users will be able to select each layer separately (substitution mode) or combine multiple layers together (overlapping mode).

The application has two parameters:

* %name – Name of the application
* %link\_to\_the\_tiles _–_ External link to the tiles

{% hint style="info" %}
If you are using an HTTPS connection, it is important that the link to the app is also HTTPS. Otherwise, you may encounter a mixed content error.
{% endhint %}

Once you have the necessary data, you simply need to add a line to your MySQL database. Map layers can be added to the entire service (a.k.a. Dealer PaaS account) or only to specific users.

### Enabling the layer for the entire service (for all user accounts)

To add a new map layer to the entire Navixy service, use the following query and provide the parameters marked in bold:

{% code overflow="wrap" %}
```
INSERT INTO google.plugins2dealers (dealer_id, plugin_id, parameters) VALUES (1, 50, '{"layers":[{"name":"%name","tiles":["%link_to_the_tiles"]}]}');
```
{% endcode %}

### Enabling the layer for selected user accounts only

When adding a map layer to a specific user, a new parameter (%user\_id) must be included in the request. This parameter should be substituted with the ID of the user to whom the map layer should be added. If the layer needs to be added to multiple users, a separate request must be made for each user.

```
INSERT INTO google.plugins2users (user_id, plugin_id, parameters) VALUES (%user_id, 50, '{"layers":[{"name":"%name","tiles":["%link_to_the_tiles"]}]}');
```

## Adding a SVG file as a custom map layer

The Navixy platform allows you to add SVG files as a map layer, which can be particularly useful for displaying outlines of hard-to-access areas, such as mines or construction sites.

When adding a custom map layer to the Navixy platform, the following three parameters must be configured for the application:

* %name: the name of the map layer
* %link\_to\_the\_layer: an external link to the layer
* %lat1, %lng1, %lat2, %lng2: the coordinates of any opposing corners of the layer

{% hint style="danger" %}
If you are using an HTTPS connection for your Navixy platform, it's important to ensure that any links to external apps or resources are also HTTPS. Otherwise, you may encounter a mixed content error.
{% endhint %}

### Enabling the SVG layer for the entire service (for all user accounts)

{% code overflow="wrap" %}
```
INSERT INTO google.plugins2dealers (dealer_id, plugin_id, parameters) VALUES (1, 83, '{"layers":[{ "name": "%name","url":"%link_to_the_layer","bounds":[{"lat":%lat1,"lng":%lng1},{"lat":%lat2,"lng":%lng2}]}]}');
```
{% endcode %}

### Enabling the SVG layer for selected user accounts only

To add a map layer to a specific user in Navixy, you must include a new parameter (%user\_id) in the request. This parameter should be replaced with the ID of the user for whom the map layer is intended. If the map layer needs to be added to multiple users, a separate request must be made for each user.

{% code overflow="wrap" %}
```
INSERT INTO google.plugins2users (user_id, plugin_id, parameters) VALUES (%user_id, 83, '{"layers":[{ "name": "%name","url":"%link_to_the_layer","bounds":[{"lat":%lat1,"lng":%lng1},{"lat":%lat2,"lng":%lng2}]}]}');
```
{% endcode %}
