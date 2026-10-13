---
description: Create geofences, POIs, and routes in different shapes using GeoJSON geometry.
tags:
  - guide
  - beta
---

# Creating geofences, POIs, and routes

Geo objects in Business Data Repository represent geographic features like geofences, points of interest, and routes. They store location data in GeoJSON format, making them useful for defining delivery zones, marking important locations, creating routes, and managing areas where your fleet operates.

This guide walks you through creating, updating, and managing geo objects with different geometry types.

## Prerequisites

You need an access token for the workspace you work in. The API takes the workspace from the token, so no operation on this page asks for a workspace ID. See [Authentication](../../authentication.md) for how to get a token and how the API picks the workspace.

### Check the available geo object types

Before creating a geo object, list the available types to see what options exist with this query:

```graphql
query ListGeoObjectTypes {
  bdr {
    geoObjectTypes {
      nodes {
        id
        code
        title
      }
    }
  }
}
```

The response lists the types your workspace has created. A new workspace has none, so the list is empty until you create a type. A workspace with two types sees this:

```json
{
  "data": {
    "bdr": {
      "geoObjectTypes": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-43474f540001",
            "code": "poi",
            "title": "Point of Interest"
          },
          {
            "id": "019a6b2f-793e-807b-8001-43474f540002",
            "code": "geofence",
            "title": "Geofence"
          }
        ]
      }
    }
  }
}
```

If the response is empty or doesn't have the type you need, create it:

```graphql
mutation CreateGeoObjectType {
  bdr {
    geoObjectTypeCreate(input: {
      code: "delivery_zone"
      title: "Delivery Zone"
    }) {
      geoObjectType {
        id
        code
        title
      }
    }
  }
}
```

The response confirms the new type:

```json
{
  "data": {
    "bdr": {
      "geoObjectTypeCreate": {
        "geoObjectType": {
          "id": "019a6b2f-793e-807b-8001-43474f540003",
          "code": "delivery_zone",
          "title": "Delivery Zone"
        }
      }
    }
  }
}
```

Save the type ID. You'll need it when creating geo objects.

{% hint style="info" %}
Geo object types are labels for grouping and filtering. They don't limit the shape: a geo object with type "Place" can use any geometry, including a `Polygon`.
{% endhint %}

## How GeoJSON geometry works

Geo objects store their geographic shape in the `geojsonData` field, which uses GeoJSON format as defined in [RFC 7946](https://www.rfc-editor.org/rfc/rfc7946). The format describes each shape as a list of coordinates.

A GeoJSON value in the API is the geometry object itself: a JSON object with a `type` and `coordinates`:

```json
{
  "type": "Polygon",
  "coordinates": [
    [
      [13.35, 52.48],
      [13.45, 52.48],
      [13.45, 52.56],
      [13.35, 52.56],
      [13.35, 52.48]
    ]
  ]
}
```

Inside a GraphQL mutation, you write the same structure with one difference: the keys have no quotes.

```graphql
{
  type: "Polygon",
  coordinates: [
    [
      [13.35, 52.48],
      [13.45, 52.48],
      [13.45, 52.56],
      [13.35, 52.56],
      [13.35, 52.48]
    ]
  ]
}
```

{% hint style="warning" %}
Send the geometry object itself, not the `Feature` or `FeatureCollection` wrappers some GeoJSON tooling produces. The API rejects `Feature` and `FeatureCollection` wrappers in every GeoJSON input, including `geojsonData`.
{% endhint %}

More examples of different geometry types can be found in the [Common GeoJSON patterns](creating-geofences-pois-and-routes.md#common-geojson-patterns) section.

### Supported geometry types

<table><thead><tr><th width="143.4888916015625">Geometry type</th><th width="385.0889892578125">Use case</th><th>Example</th></tr></thead><tbody><tr><td>Point</td><td>A single coordinate marker. To check which vehicles are at the place, draw a Polygon around the point instead.</td><td>Warehouse pin on a map</td></tr><tr><td>Polygon</td><td>Area boundary. One of the two types that have an area, so <code>containsPoint</code> can test a point against it.</td><td>Geofence</td></tr><tr><td>LineString</td><td>A straight or curved line passing through specific points. Useful for visual map overlays, but has no area, so <code>containsPoint</code> returns <code>false</code> for it.</td><td>Road segment, boundary line</td></tr><tr><td>MultiPoint</td><td>Multiple separate coordinate markers</td><td>Distribution network</td></tr><tr><td>MultiLineString</td><td>Multiple line sequences</td><td>Multiple road segments</td></tr><tr><td>MultiPolygon</td><td>Multiple areas treated as a single geo object. The other type that has an area.</td><td>Separate zones that don't touch, city districts</td></tr><tr><td>GeometryCollection</td><td>A mixed collection of geometry objects of different types</td><td>A route line together with its checkpoint markers</td></tr></tbody></table>

**Routes and detecting deviation:** A GeoJSON `LineString` represents a line with no width. In fleet management, route monitoring works by detecting whether a vehicle leaves a defined corridor: a `Polygon` drawn as a band around the intended path. A line can't do this job because it has no area. Use `LineString` only for drawing on a map, and `Polygon` whenever you need to check what's inside.

**Points and GPS presence detection:** A `Point` geometry has no area, so no vehicle can be inside it. To find out whether a GPS device is near a specific location, create a small `Polygon` around that location instead of a `Point`. Then test the device's position against the polygon with `containsPoint`, as [Checking which geo objects cover a point](creating-geofences-pois-and-routes.md#checking-which-geo-objects-cover-a-point) shows.

### Coordinate format

GeoJSON uses `[longitude, latitude]` order, which is opposite to how coordinates are often written in text.

```
Text format:     52.520008, 13.404954 (latitude, longitude)
GeoJSON format:  [13.404954, 52.520008] (longitude, latitude)
```

For example, Berlin's Brandenburg Gate is located at latitude 52.516275 and longitude 13.377704. In GeoJSON, this becomes `[13.377704, 52.516275]`.

{% hint style="warning" %}
Always double-check coordinate order when converting from other formats. Swapped coordinates will place your location in the wrong part of the world.
{% endhint %}

For details on geometry structure, winding order for polygons, and coordinate reference systems, see [RFC 7946 Section 3.1](https://www.rfc-editor.org/rfc/rfc7946#section-3.1).

### Valid areas

A `Polygon` or a `MultiPolygon` must describe a well-formed area. When it doesn't, `geoObjectCreate` and `geoObjectUpdate` fail with a [validation error](../../error-handling.md#validation-error-400), usually on `input.geojsonData`, and save nothing. An area is well formed when it follows these rules:

* A `Polygon` is a non-empty array of rings, and a `MultiPolygon` is a non-empty array of such polygons.
* A ring has at least four positions, and its first and last positions have the same longitude and latitude.
* A position has at least two elements, and every element is a number: a longitude from -180 to 180, then a latitude from -90 to 90.

The API doesn't check the winding order of a ring. A third number in a position, the altitude, doesn't change the area. Other geometry types are saved without these checks. An update without `geojsonData` keeps the stored geometry and doesn't check it. A stored polygon that breaks these rules has no area, so `containsPoint` returns `false` for it.

### Custom fields

Geo objects support custom fields. You might want to add fields for:

* Access restrictions (delivery time windows, vehicle type requirements)
* Operational metadata (zone manager contact, capacity limits)
* Business attributes (pricing tier, priority level)

See [Defining and using custom fields](defining-and-using-custom-fields.md) for details on defining and using custom fields.

## Example scenario: Delivery service zones

A delivery company needs to define service areas and mark important locations. They start by creating an arrival zone around their main warehouse: a small polygon that lets them detect when vehicles arrive or depart. Then they create a larger delivery zone and adjust its boundaries as the business grows.

{% stepper %}
{% step %}
### Create the arrival zone

Rather than a `Point`, draw the warehouse as a small `Polygon` around the building. A polygon has an area, so `containsPoint` can tell whether a vehicle's position is inside it.

{% hint style="info" %}
`version` is optional. If you leave it out, an update always applies, even when someone else changed the record after you last read it. Include it to catch such conflicts, as the examples in this scenario do. See [Optimistic locking](../../optimistic-locking.md) for details.
{% endhint %}

```graphql
mutation CreateWarehouseZone {
  bdr {
    geoObjectCreate(input: {
      typeId: "019a6b2f-793e-807b-8001-43474f540001"
      title: "Main Warehouse - Mitte"
      geojsonData: {
        type: "Polygon"
        coordinates: [
          [
            [13.404754, 52.519808],
            [13.405154, 52.519808],
            [13.405154, 52.520208],
            [13.404754, 52.520208],
            [13.404754, 52.519808]
          ]
        ]
      }
    }) {
      geoObject {
        id
        version
        title
        geojsonData
      }
    }
  }
}
```

The polygon is a small rectangle roughly 30 × 45 meters centered on coordinates `[13.404954, 52.520008]` in Berlin's Mitte district. Adjust the coordinates to match your actual site boundary.

The response confirms creation:

```json
{
  "data": {
    "bdr": {
      "geoObjectCreate": {
        "geoObject": {
          "id": "019a6b2f-793e-807b-8001-47454f420001",
          "version": 1,
          "title": "Main Warehouse - Mitte",
          "geojsonData": {
            "type": "Polygon",
            "coordinates": [
              [
                [13.404754, 52.519808],
                [13.405154, 52.519808],
                [13.405154, 52.520208],
                [13.404754, 52.520208],
                [13.404754, 52.519808]
              ]
            ]
          }
        }
      }
    }
  }
}
```

Save the `id`. You'll need it for updates.
{% endstep %}

{% step %}
### Verify the geo object

Query the geo object to confirm it was created correctly:

```graphql
query GetWarehouseZone {
  bdr {
    geoObject(id: "019a6b2f-793e-807b-8001-47454f420001") {
      id
      version
      title
      type {
        code
        title
      }
      geojsonData
    }
  }
}
```

The response returns the full geo object:

```json
{
  "data": {
    "bdr": {
      "geoObject": {
        "id": "019a6b2f-793e-807b-8001-47454f420001",
        "version": 1,
        "title": "Main Warehouse - Mitte",
        "type": {
          "code": "poi",
          "title": "Point of Interest"
        },
        "geojsonData": {
          "type": "Polygon",
          "coordinates": [
            [
              [13.404754, 52.519808],
              [13.405154, 52.519808],
              [13.405154, 52.520208],
              [13.404754, 52.520208],
              [13.404754, 52.519808]
            ]
          ]
        }
      }
    }
  }
}
```

The `geojsonData` field contains the full GeoJSON structure you provided, which you can use to verify the configuration or display on a map in your application.
{% endstep %}

{% step %}
### Create a polygon-shaped delivery zone

Create a rectangular delivery zone covering central Berlin:

```graphql
mutation CreateDeliveryZone {
  bdr {
    geoObjectCreate(input: {
      typeId: "019a6b2f-793e-807b-8001-43474f540002"
      title: "Central Berlin Zone"
      geojsonData: {
        type: "Polygon"
        coordinates: [
          [
            [13.35, 52.48],
            [13.45, 52.48],
            [13.45, 52.56],
            [13.35, 52.56],
            [13.35, 52.48]
          ]
        ]
      }
    }) {
      geoObject {
        id
        version
        title
        geojsonData
      }
    }
  }
}
```

Note the coordinate structure:

* The outer array holds one or more rings. A ring is a closed loop of coordinate pairs. This example has one ring, the outer boundary.
* Each ring is an array of `[longitude, latitude]` pairs.
* The first and last pairs are identical, which closes the loop.

The response returns:

```json
{
  "data": {
    "bdr": {
      "geoObjectCreate": {
        "geoObject": {
          "id": "019a6b2f-793e-807b-8001-47454f420002",
          "version": 1,
          "title": "Central Berlin Zone",
          "geojsonData": {
            "type": "Polygon",
            "coordinates": [
              [
                [13.35, 52.48],
                [13.45, 52.48],
                [13.45, 52.56],
                [13.35, 52.56],
                [13.35, 52.48]
              ]
            ]
          }
        }
      }
    }
  }
}
```
{% endstep %}

{% step %}
### Update the zone boundary

As your delivery business expands, you need to cover a larger area. Update the zone geometry to extend the boundaries:

```graphql
mutation ExpandDeliveryZone {
  bdr {
    geoObjectUpdate(input: {
      id: "019a6b2f-793e-807b-8001-47454f420002"
      version: 1
      geojsonData: {
        type: "Polygon"
        coordinates: [
          [
            [13.30, 52.45],
            [13.50, 52.45],
            [13.50, 52.60],
            [13.30, 52.60],
            [13.30, 52.45]
          ]
        ]
      }
    }) {
      geoObject {
        id
        version
        geojsonData
      }
    }
  }
}
```

The response shows the incremented version:

```json
{
  "data": {
    "bdr": {
      "geoObjectUpdate": {
        "geoObject": {
          "id": "019a6b2f-793e-807b-8001-47454f420002",
          "version": 2,
          "geojsonData": {
            "type": "Polygon",
            "coordinates": [ "..." ]
          }
        }
      }
    }
  }
}
```
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You now have two geo objects, an arrival zone around the warehouse and a polygon delivery zone, and a zone boundary updated under its version.
{% endhint %}

## Common GeoJSON patterns

The examples below show the complete geometry objects you send in mutations and receive in responses.

### Single location marker (warehouse, customer address)

```graphql
{
  type: "Point",
  coordinates: [13.404954, 52.520008]
}
```

### Rectangular area (simple geofence)

```graphql
{
  type: "Polygon",
  coordinates: [
    [
      [13.35, 52.48],
      [13.45, 52.48],
      [13.45, 52.56],
      [13.35, 52.56],
      [13.35, 52.48]
    ]
  ]
}
```

### Polygon with exclusion zone (donut-shaped)

```graphql
{
  type: "Polygon",
  coordinates: [
    [
      [13.30, 52.45],
      [13.50, 52.45],
      [13.50, 52.60],
      [13.30, 52.60],
      [13.30, 52.45]
    ],
    [
      [13.38, 52.50],
      [13.40, 52.50],
      [13.40, 52.52],
      [13.38, 52.52],
      [13.38, 52.50]
    ]
  ]
}
```

The first ring defines the outer boundary. The second ring creates a hole: an area inside the outer boundary where the polygon doesn't apply. This is useful for delivery zones that exclude certain neighborhoods or restricted areas.

### Route corridor (for deviation detection)

In fleet management, routes are drawn as `Polygon` corridors rather than `LineString` geometries. A corridor is a band around the intended path: if a vehicle leaves this zone, it has deviated from the route. The example below shows a narrow corridor along a Berlin street segment:

```graphql
{
  type: "Polygon",
  coordinates: [
    [
      [13.376, 52.515],
      [13.406, 52.519],
      [13.406, 52.521],
      [13.376, 52.517],
      [13.376, 52.515]
    ]
  ]
}
```

Use a wider corridor for highways where minor deviations are acceptable, and a narrower one for city routes requiring precise tracking.

### Line overlay (visual display only)

Use `LineString` when you need to draw a line on a map for display purposes, for example to show the planned path of a delivery or the boundary between two zones. A `LineString` has no area, so `containsPoint` returns `false` for it.

```graphql
{
  type: "LineString",
  coordinates: [
    [13.377704, 52.516275],
    [13.404954, 52.520008],
    [13.388175, 52.519444]
  ]
}
```

### Multiple warehouse locations

```graphql
{
  type: "MultiPoint",
  coordinates: [
    [13.404954, 52.520008],
    [13.410000, 52.515000],
    [13.395000, 52.525000]
  ]
}
```

## Listing geo objects

To retrieve all geo objects for a workspace, use this query:

```graphql
query ListGeoObjects {
  bdr {
    geoObjects(
      first: 20
    ) {
      nodes {
        id
        title
        type {
          code
          title
        }
        geojsonData
      }
      pageInfo {
        hasNextPage
        endCursor
      }
    }
  }
}
```

The response returns a paginated list:

```json
{
  "data": {
    "bdr": {
      "geoObjects": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-47454f420002",
            "title": "Central Berlin Zone",
            "type": {
              "code": "geofence",
              "title": "Geofence"
            },
            "geojsonData": {
              "type": "Polygon",
              "coordinates": [
                [
                  [13.35, 52.48],
                  [13.45, 52.48],
                  [13.45, 52.56],
                  [13.35, 52.56],
                  [13.35, 52.48]
                ]
              ]
            }
          },
          {
            "id": "019a6b2f-793e-807b-8001-47454f420001",
            "title": "Main Warehouse - Mitte",
            "type": {
              "code": "poi",
              "title": "Point of Interest"
            },
            "geojsonData": {
              "type": "Polygon",
              "coordinates": [
                [
                  [13.404754, 52.519808],
                  [13.405154, 52.519808],
                  [13.405154, 52.520208],
                  [13.404754, 52.520208],
                  [13.404754, 52.519808]
                ]
              ]
            }
          }
        ],
        "pageInfo": {
          "hasNextPage": false,
          "endCursor": "VElUTEU6QVNDOk1haW4gV2FyZWhvdXNlIC0gTWl0dGV8MDE5YTZiMmYtNzkzZS04MDdiLTgwMDEtNDc0NTRmNDIwMDAx"
        }
      }
    }
  }
}
```

You can filter by type or search by title:

```graphql
query ListDeliveryZones {
  bdr {
    geoObjects(
      filter: {
        typeIds: ["019a6b2f-793e-807b-8001-43474f540002"]
        titleContains: "berlin"
      }
      first: 20
    ) {
      nodes {
        id
        title
      }
    }
  }
}
```

The response returns only matching geo objects:

```json
{
  "data": {
    "bdr": {
      "geoObjects": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-47454f420002",
            "title": "Central Berlin Zone"
          }
        ]
      }
    }
  }
}
```

For more on filtering and pagination, see [Filtering and sorting](../../filtering-and-sorting/) and [Pagination](../../pagination.md).

## Checking which geo objects cover a point

A geo object covers a point when the point is inside its area or on the boundary of the area. Only a well-formed `Polygon` or `MultiPolygon` has an area. A point inside a hole of a polygon isn't covered, and a point on the boundary of the hole is.

To find the geo objects that cover a point, add `containsPoint` to the filter. This query lists the zones that cover a delivery address in Berlin:

```graphql
query ZonesAtAddress {
  bdr {
    geoObjects(filter: { containsPoint: { lat: 52.5201, lng: 13.4050 } }, first: 20) {
      nodes {
        id
        title
      }
    }
  }
}
```

The address is inside both zones of the example scenario, so the response lists both, sorted by title:

```json
{
  "data": {
    "bdr": {
      "geoObjects": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-47454f420002",
            "title": "Central Berlin Zone"
          },
          {
            "id": "019a6b2f-793e-807b-8001-47454f420001",
            "title": "Main Warehouse - Mitte"
          }
        ]
      }
    }
  }
}
```

The `containsPoint` condition combines with `typeIds`, `titleContains`, and the custom field conditions, and all of them must match. Sorting, paging, and `total` work as with any other filter.

To test one geo object against one or more points, select its `containsPoint` field with a point. The field returns `true` or `false`. A geo object without an area returns `false` for every point. To test several points in one request, give each point its own alias:

```graphql
query ZoneCoversPoints {
  bdr {
    geoObject(id: "019a6b2f-793e-807b-8001-47454f420001") {
      gate: containsPoint(point: { lat: 52.5201, lng: 13.4050 })
      parking: containsPoint(point: { lat: 52.5230, lng: 13.4100 })
    }
  }
}
```

The response has one value for each alias:

```json
{
  "data": {
    "bdr": {
      "geoObject": {
        "gate": true,
        "parking": false
      }
    }
  }
}
```

Edges of an area are straight lines in longitude and latitude. Longitude 180 and -180 are different points, so draw an area that crosses the 180th meridian as two polygons. To check which of your assets are inside a geofence now, combine this test with the positions from Tracking, as [Checking which assets are inside a geofence](../../tracking/guides/building-a-live-fleet-map.md#checking-which-assets-are-inside-a-geofence) shows.

## Handling version conflicts

If you include `version` in your mutation and the entity has been modified since you last fetched it, the API returns a conflict error:

```json
{
  "errors": [
    {
      "message": "Version conflict: expected 2, found 3",
      "path": ["bdr", "geoObjectUpdate"],
      "extensions": {
        "type": "https://api.navixy.com/errors/conflict",
        "title": "Concurrent Modification Conflict",
        "status": 409,
        "detail": "Version conflict: expected 2, found 3",
        "instance": "/graphql",
        "code": "CONFLICT",
        "field": "input.version",
        "expectedVersion": 2,
        "currentVersion": 3,
        "traceId": "0af7651916cd43dd8448eb211c80319c",
        "timestamp": "2025-01-15T14:30:00Z"
      }
    }
  ]
}
```

To resolve this:

1. Query the geo object to get the current version and geometry
2. Merge your changes with the current state
3. Retry the mutation with the correct version

For more details on version conflicts, see [Optimistic locking](../../optimistic-locking.md).

## Deleting a geo object

When you restructure your delivery zones and no longer need this geo object, you can delete it. While an asset or another geo object names it in a custom field, the delete is refused with [`IN_USE`](../../error-handling.md#in-use-409), and `extensions.blockers` names those records with the path `customFields.<code>`. Change the value on them first, or unset it if the field isn't required.

```graphql
mutation DeleteDeliveryZone {
  bdr {
    geoObjectDelete(input: {
      id: "019a6b2f-793e-807b-8001-47454f420002"
      version: 2
    }) {
      deletedId
    }
  }
}
```

The response confirms deletion:

```json
{
  "data": {
    "bdr": {
      "geoObjectDelete": {
        "deletedId": "019a6b2f-793e-807b-8001-47454f420002"
      }
    }
  }
}
```

## See also

* [Geo objects](../api-reference/geo-objects.md): Complete reference for all geo object operations and types
* [Filtering and sorting](../../filtering-and-sorting/): Narrow list queries and control result order
* [Optimistic locking](../../optimistic-locking.md): Prevent concurrent updates from overwriting each other with `version`
* [Building a live fleet map](../../tracking/guides/building-a-live-fleet-map.md): Check which assets are inside a geofence right now, using the geometry you drew here
* [Reading a vehicle trip history](../../tracking/guides/reading-a-vehicle-trip-history.md): Find where a vehicle drove and where it stopped over a period
