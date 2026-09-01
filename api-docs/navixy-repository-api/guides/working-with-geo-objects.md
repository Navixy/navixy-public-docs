---
description: Create geofences, POIs, and routes in different shapes using GeoJSON geometry.
---

# Working with geo objects

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

Geo objects in Navixy Repository API represent geographic features like geofences, points of interest, and routes. They store location data in GeoJSON format, making them useful for defining delivery zones, marking important locations, creating routes, and managing areas where your fleet operates.

This guide walks you through creating, updating, and managing geo objects with different geometry types.

## Prerequisites

To work with geo objects, you need your workspace's ID. It comes with your access credentials and is carried in your access token. See [Authentication](../authentication.md) for how tokens work and where the workspace ID comes from.

### Check the available geo object types

Before creating a geo object, list the available types to see what options exist with this query:

```graphql
query ListGeoObjectTypes {
  bdr {
    geoObjectTypes(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
    ) {
      nodes {
        id
        code
        title
      }
    }
  }
}
```

The response returns predefined types and any custom types your workspace has created:

```json
{
  "data": {
    "bdr": {
      "geoObjectTypes": {
        "nodes": [
          {
            "id": "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11",
            "code": "poi",
            "title": "Point of Interest"
          },
          {
            "id": "b1ffcd00-0d1c-5f29-cc7e-7cc0ce491b22",
            "code": "geofence",
            "title": "Geofence"
          }
        ]
      }
    }
  }
}
```

If the response returns an empty array, doesn't have the type you need, or you want a type with a different code or title, create it:

```graphql
mutation CreateGeoObjectType {
  bdr {
    geoObjectTypeCreate(input: {
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
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
          "id": "c2eede11-1e2d-6e30-dd8f-8dd1df602c33",
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
Send the geometry object itself, not the `Feature` or `FeatureCollection` wrappers some GeoJSON tooling produces. In particular, `GEOJSON` custom fields that restrict geometry types reject wrapped values.
{% endhint %}

More examples of different geometry types can be found in the [Common GeoJSON patterns](working-with-geo-objects.md#common-geojson-patterns) section.

### Supported geometry types

<table><thead><tr><th width="143.4888916015625">Geometry type</th><th width="385.0889892578125">Use case</th><th>Example</th></tr></thead><tbody><tr><td>Point</td><td>A single coordinate marker. For GPS containment checks, use a Polygon centered on the point instead.</td><td>Warehouse pin on a map</td></tr><tr><td>Polygon</td><td>Area boundary. Supports <code>containsPoints</code> checks (the only types that do are <code>Polygon</code> and <code>MultiPolygon</code>).</td><td>Geofence</td></tr><tr><td>LineString</td><td>A straight or curved line passing through specific points. Useful for visual map overlays, but has no area and cannot be used for containment checks.</td><td>Road segment, boundary line</td></tr><tr><td>MultiPoint</td><td>Multiple separate coordinate markers</td><td>Distribution network</td></tr><tr><td>MultiLineString</td><td>Multiple line sequences</td><td>Multiple road segments</td></tr><tr><td>MultiPolygon</td><td>Multiple areas treated as a single geo object. Supports <code>containsPoints</code> checks</td><td>Separate zones that don't touch, city districts</td></tr><tr><td>GeometryCollection</td><td>A mixed collection of geometry objects of different types</td><td>A route line together with its checkpoint markers</td></tr></tbody></table>

**Routes and detecting deviation:** A GeoJSON `LineString` represents a line with no width. In fleet management, route monitoring works by detecting whether a vehicle leaves a defined corridor: a `Polygon` drawn as a band around the intended path. A line can't do this job because it has no area. Use `LineString` only for drawing on a map, and `Polygon` whenever you need to check what's inside.

**Points and GPS presence detection:** A `Point` geometry has no area, so `containsPoints` cannot be applied to it. If you need to detect whether a GPS device is near a specific location, create a small `Polygon` centered on that location instead of using a `Point`.

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

### Custom fields

Geo objects support custom fields. You might want to add fields for:

- Access restrictions (delivery time windows, vehicle type requirements)
- Operational metadata (zone manager contact, capacity limits)
- Business attributes (pricing tier, priority level)

See [Implementing custom fields](implementing-custom-fields.md) for details on defining and using custom fields.

## Example scenario: Delivery service zones

A delivery company needs to define service areas and mark important locations. They start by creating an arrival zone around their main warehouse: a small polygon that lets them detect when vehicles arrive or depart. Then they create a larger delivery zone, verify which addresses fall within it, and adjust boundaries as the business grows.

{% stepper %}
{% step %}
### Create the arrival zone

Rather than a `Point`, draw the warehouse as a small `Polygon` around the building. This lets you use `containsPoints` later to detect vehicle arrivals and departures.

{% hint style="info" %}
`version` is optional. If you leave it out, an update always applies, even when someone else changed the record after you last read it. Include it to catch such conflicts, as the examples in this scenario do. See [Optimistic locking](../optimistic-locking.md) for details.
{% endhint %}

```graphql
mutation CreateWarehouseZone {
  bdr {
    geoObjectCreate(input: {
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      typeId: "019ce6d5-ff98-8000-801f-43474f542312"
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
          "id": "019a6b2f-793e-807b-8001-555345529b44",
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
    geoObject(id: "019a6b2f-793e-807b-8001-555345529b44") {
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
        "id": "019a6b2f-793e-807b-8001-555345529b44",
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
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      typeId: "b1ffcd00-0d1c-5f29-cc7e-7cc0ce491b22"
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

- The outer array holds one or more rings. A ring is a closed loop of coordinate pairs. This example has one ring, the outer boundary.
- Each ring is an array of `[longitude, latitude]` pairs.
- The first and last pairs are identical, which closes the loop.

The response returns:

```json
{
  "data": {
    "bdr": {
      "geoObjectCreate": {
        "geoObject": {
          "id": "019a6b30-8a4f-807b-8001-666456630c55",
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
### Check test point containment

Check if specific delivery addresses fall within your zone using the `containsPoints` field:

```graphql
query CheckDeliveryAddresses {
  bdr {
    geoObject(id: "019a6b30-8a4f-807b-8001-666456630c55") {
      title
      geojsonData
      containsPoints(points: [
        { lat: 52.520008, lng: 13.404954 }
        { lat: 52.500000, lng: 13.400000 }
        { lat: 52.600000, lng: 13.300000 }
      ]) {
        index
        point {
          lat
          lng
        }
        isContained
      }
    }
  }
}
```

The response shows which points are inside the zone:

```json
{
  "data": {
    "bdr": {
      "geoObject": {
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
        },
        "containsPoints": [
          {
            "index": 0,
            "point": { "lat": 52.520008, "lng": 13.404954 },
            "isContained": true
          },
          {
            "index": 1,
            "point": { "lat": 52.500000, "lng": 13.400000 },
            "isContained": true
          },
          {
            "index": 2,
            "point": { "lat": 52.600000, "lng": 13.300000 },
            "isContained": false
          }
        ]
      }
    }
  }
}
```

The first two addresses fall within the zone, while the third is outside. This lets you check which delivery requests you can accept.

{% hint style="warning" %}
The `containsPoints` field is only available for `Polygon` and `MultiPolygon` geometry types. It tests whether each point falls inside the polygon boundary.
{% endhint %}
{% endstep %}

{% step %}
### Update the zone boundary

As your delivery business expands, you need to cover a larger area. Update the zone geometry to extend the boundaries:

```graphql
mutation ExpandDeliveryZone {
  bdr {
    geoObjectUpdate(input: {
      id: "019a6b30-8a4f-807b-8001-666456630c55"
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
          "id": "019a6b30-8a4f-807b-8001-666456630c55",
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

{% step %}
### Delete the geo object

When you restructure your delivery zones and no longer need this geo object, you can delete it:

```graphql
mutation DeleteDeliveryZone {
  bdr {
    geoObjectDelete(input: {
      id: "019a6b30-8a4f-807b-8001-666456630c55"
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
        "deletedId": "019a6b30-8a4f-807b-8001-666456630c55"
      }
    }
  }
}
```
{% endstep %}
{% endstepper %}

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

Use `LineString` when you need to draw a line on a map for display purposes, for example to show the planned path of a delivery or the boundary between two zones. A `LineString` has no area and cannot be used for containment checks.

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
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
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
            "id": "019a6b2f-793e-807b-8001-555345529b44",
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
          },
          {
            "id": "019a6b30-8a4f-807b-8001-666456630c55",
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
          }
        ],
        "pageInfo": {
          "hasNextPage": false,
          "endCursor": "YXJyYXljb25uZWN0aW9uOjE="
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
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: {
        typeIds: ["b1ffcd00-0d1c-5f29-cc7e-7cc0ce491b22"]
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
            "id": "019a6b30-8a4f-807b-8001-666456630c55",
            "title": "Central Berlin Zone"
          }
        ]
      }
    }
  }
}
```

For more on filtering and pagination, see [Filtering and sorting](../filtering-and-sorting.md) and [Pagination](../pagination.md).

## Handling version conflicts

If you include `version` in your mutation and the entity has been modified since you last fetched it, the API returns a conflict error:

```json
{
  "errors": [
    {
      "message": "Entity has been modified by another request",
      "path": ["bdr", "geoObjectUpdate"],
      "extensions": {
        "type": "https://api.navixy.com/errors/conflict",
        "title": "Optimistic Lock Conflict",
        "status": 409,
        "detail": "GeoObject 019a6b30-... was modified. Expected version 2, current version 3.",
        "code": "CONFLICT",
        "entityType": "GeoObject",
        "entityId": "019a6b30-8a4f-807b-8001-666456630c55",
        "expectedVersion": 2,
        "currentVersion": 3,
        "traceId": "0af7651916cd43dd8448eb211c80319c"
      }
    }
  ]
}
```

To resolve this:

1. Query the geo object to get the current version and geometry
2. Merge your changes with the current state
3. Retry the mutation with the correct version

For more details on version conflicts, see [Optimistic locking](../optimistic-locking.md).

## See also

* [Geo objects](../geo-objects.md): Complete reference for all geo object operations and types
* [Filtering and sorting](../filtering-and-sorting.md): Narrow list queries and control result order
* [Optimistic locking](../optimistic-locking.md): Prevent concurrent updates from overwriting each other with `version`
