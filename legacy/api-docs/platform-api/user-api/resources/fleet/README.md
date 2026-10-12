---
title: Fleet management
description: The vehicles behind the trackers, where they are maintained, and how their trips are categorised.
---

# Fleet management

Fleet management covers what a tracking device cannot report about itself. A vehicle record carries the VIN, the plate, and the consumption figures that turn distance into cost, and a garage records the premises the fleet is maintained from.

[Vehicle](vehicle/README.md) is where most of that lives. [Driver journal](driver_journal/README.md) categorises trips as business or private, which is what a mileage claim is built from.

## Operations in this section

<!-- endpoint-reference:start -->

#### Garage (depot)

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/garage/list`](garage.md#post-garage-list) | POST | List depots |
| [`/garage/create`](garage.md#post-garage-create) | POST | Create depot |
| [`/garage/update`](garage.md#post-garage-update) | POST | Update depot |
| [`/garage/delete`](garage.md#post-garage-delete) | POST | Delete depot |

#### Driver journal entry

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/driver/journal/entry/list`](driver_journal/entry.md#post-driver-journal-entry-list) | POST | List driver journal entries |
| [`/driver/journal/entry/create`](driver_journal/entry.md#post-driver-journal-entry-create) | POST | Create driver journal entries |
| [`/driver/journal/entry/update`](driver_journal/entry.md#post-driver-journal-entry-update) | POST | Update driver journal entry |
| [`/driver/journal/entry/delete`](driver_journal/entry.md#post-driver-journal-entry-delete) | POST | Delete driver journal entries |
| [`/driver/journal/entry/download`](driver_journal/entry.md#post-driver-journal-entry-download) | POST | Download driver journal |

#### Trip proposals

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/driver/journal/proposal/list`](driver_journal/proposal.md#post-driver-journal-proposal-list) | POST | List trip proposals |

#### Vehicle

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/vehicle/create`](vehicle/README.md#post-vehicle-create) | POST | Create vehicle |
| [`/vehicle/delete`](vehicle/README.md#post-vehicle-delete) | POST | Delete vehicles |
| [`/vehicle/list`](vehicle/README.md#post-vehicle-list) | POST | List vehicles |
| [`/vehicle/read`](vehicle/README.md#post-vehicle-read) | POST | Read vehicle |
| [`/vehicle/update`](vehicle/README.md#post-vehicle-update) | POST | Update vehicle |
| [`/vehicle/batch_convert`](vehicle/README.md#post-vehicle-batch_convert) | POST | Convert vehicle batch |

#### Vehicle avatar

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/vehicle/avatar/assign`](vehicle/avatar.md#post-vehicle-avatar-assign) | POST | Assign vehicle icon |
| [`/vehicle/avatar/upload`](vehicle/avatar.md#post-vehicle-avatar-upload) | POST | Upload vehicle avatar |

#### Vehicle import

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/vehicle/import/start`](vehicle/import.md#post-vehicle-import-start) | POST | Start vehicle import |
| [`/vehicle/import/read`](vehicle/import.md#post-vehicle-import-read) | POST | Read vehicle import |
| [`/vehicle/import/list`](vehicle/import.md#post-vehicle-import-list) | POST | List vehicle imports |
| [`/vehicle/import/download_failed`](vehicle/import.md#post-vehicle-import-download_failed) | POST | Download failed import rows |

#### Service task

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/vehicle/service_task/batch/create`](vehicle/service-task/README.md#post-vehicle-service_task-batch-create) | POST | Create service tasks for vehicles |
| [`/vehicle/service_task/create`](vehicle/service-task/README.md#post-vehicle-service_task-create) | POST | Create service task |
| [`/vehicle/service_task/delete`](vehicle/service-task/README.md#post-vehicle-service_task-delete) | POST | Delete service task |
| [`/vehicle/service_task/download`](vehicle/service-task/README.md#post-vehicle-service_task-download) | POST | Download service task report |
| [`/vehicle/service_task/list`](vehicle/service-task/README.md#post-vehicle-service_task-list) | POST | List service tasks |
| [`/vehicle/service_task/read`](vehicle/service-task/README.md#post-vehicle-service_task-read) | POST | Read service task |
| [`/vehicle/service_task/set_status`](vehicle/service-task/README.md#post-vehicle-service_task-set_status) | POST | Set service task status |
| [`/vehicle/service_task/update`](vehicle/service-task/README.md#post-vehicle-service_task-update) | POST | Update service task |

#### Service task file

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/vehicle/service_task/file/create`](vehicle/service-task/file.md#post-vehicle-service_task-file-create) | POST | Create service task file |

#### Vehicle status listing

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/vehicle/status/listing/read`](vehicle/listing.md#post-vehicle-status-listing-read) | POST | Read vehicle status listing |
| [`/vehicle/status/listing/update`](vehicle/listing.md#post-vehicle-status-listing-update) | POST | Update vehicle status listing |

<!-- endpoint-reference:end -->
