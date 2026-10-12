---
title: Vehicle
description: The vehicle behind a tracker, carrying its VIN, consumption figures, and other details a device cannot report.
---

# Vehicle

A vehicle records what the Navixy platform knows about the thing being tracked rather than about the device tracking it: its VIN, its registration, its fuel consumption norms, and the rest.

A vehicle is linked to a [tracker](../../tracking/tracker/README.md), and that link is what lets the platform combine the two. A track's `norm_fuel_consumed`, for example, is only calculated when a vehicle with `normAvgFuelConsumption` is attached to the tracker that produced it.

## Vehicle object

{% openapi-schemas spec="navixy-platform" schemas="Vehicle" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

<details>

<summary>Subtypes</summary>

<table><thead><tr><th width="136.4000244140625">Type</th><th>Subtypes</th></tr></thead><tbody><tr><td>"car"</td><td>"sedan", "universal", "hatchback", "liftback", "limousine", "pickup", "minivan", "coupe", "coupe4d", "muscle", "convertible", "phaeton", "lando", "crossover", "roadster", "suv"</td></tr><tr><td>"truck"</td><td>"tipper", "board", "covered", "awning", "mixer", "tanker", "refrigerator", "transporter", "container", "tractor"</td></tr><tr><td>"bus"</td><td>"city", "shuttle", "platform", "school", "intercity", "sightseeing"</td></tr><tr><td>"special"</td><td>"mobile_crane", "racing", "buggy", "ambulance", "firefighter", "hearse", "shop", "harvester", "snowplow", "tractor", "grader", "excavator", "bulldozer", "armored", "amphibian", "boat"</td></tr></tbody></table>

</details>

## API actions

API base path: `/vehicle`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

Creates a vehicle from a vehicle object without an `id`.

***

_Required sub-user rights:_ `vehicle_update`.

{% openapi-operation spec="navixy-platform" path="/vehicle/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 247 - a device cannot be assigned, or a uniqueness constraint failed.

***

Deletes vehicles with the specified IDs. Only one of `vehicle_id` or `vehicle_ids` must be specified.

_Required sub-user rights:_ `vehicle_update`.

{% openapi-operation spec="navixy-platform" path="/vehicle/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

Lists the vehicles of the current user.

{% openapi-operation spec="navixy-platform" path="/vehicle/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

Reads one vehicle by ID.

{% openapi-operation spec="navixy-platform" path="/vehicle/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

Updates a vehicle. Pass a full vehicle object including its `id`. `icon_id` can only be changed through [avatar/assign](avatar.md#post-vehicle-avatar-assign).

_Required sub-user rights:_ `vehicle_update`.

{% openapi-operation spec="navixy-platform" path="/vehicle/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201, 247, 261.

***

Converts a batch of tab-delimited vehicles and returns the list of checked vehicles with errors. If `file_id` is set, `batch` is ignored.

_Required sub-user rights:_ `vehicle_update`.

{% openapi-operation spec="navixy-platform" path="/vehicle/batch_convert" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 234.

## More in this section

<!-- endpoint-reference:start -->

#### Vehicle avatar

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/vehicle/avatar/assign`](avatar.md#post-vehicle-avatar-assign) | POST | Assign vehicle icon |
| [`/vehicle/avatar/upload`](avatar.md#post-vehicle-avatar-upload) | POST | Upload vehicle avatar |

#### Vehicle import

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/vehicle/import/start`](import.md#post-vehicle-import-start) | POST | Start vehicle import |
| [`/vehicle/import/read`](import.md#post-vehicle-import-read) | POST | Read vehicle import |
| [`/vehicle/import/list`](import.md#post-vehicle-import-list) | POST | List vehicle imports |
| [`/vehicle/import/download_failed`](import.md#post-vehicle-import-download_failed) | POST | Download failed import rows |

#### Service task

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/vehicle/service_task/batch/create`](service-task/README.md#post-vehicle-service_task-batch-create) | POST | Create service tasks for vehicles |
| [`/vehicle/service_task/create`](service-task/README.md#post-vehicle-service_task-create) | POST | Create service task |
| [`/vehicle/service_task/delete`](service-task/README.md#post-vehicle-service_task-delete) | POST | Delete service task |
| [`/vehicle/service_task/download`](service-task/README.md#post-vehicle-service_task-download) | POST | Download service task report |
| [`/vehicle/service_task/list`](service-task/README.md#post-vehicle-service_task-list) | POST | List service tasks |
| [`/vehicle/service_task/read`](service-task/README.md#post-vehicle-service_task-read) | POST | Read service task |
| [`/vehicle/service_task/set_status`](service-task/README.md#post-vehicle-service_task-set_status) | POST | Set service task status |
| [`/vehicle/service_task/update`](service-task/README.md#post-vehicle-service_task-update) | POST | Update service task |

#### Service task file

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/vehicle/service_task/file/create`](service-task/file.md#post-vehicle-service_task-file-create) | POST | Create service task file |

#### Vehicle status listing

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/vehicle/status/listing/read`](listing.md#post-vehicle-status-listing-read) | POST | Read vehicle status listing |
| [`/vehicle/status/listing/update`](listing.md#post-vehicle-status-listing-update) | POST | Update vehicle status listing |

<!-- endpoint-reference:end -->
