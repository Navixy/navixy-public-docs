---
title: Place
description: Business locations that employees visit, called points of interest in the interface.
---

# Place

A place is a location the account cares about: a client site, a depot, a delivery address. Tasks are assigned against places, and reports name them instead of raw coordinates.

[Places (POI)](work-with-poi.md) covers creating and managing them, [Place import](import.md) creates many at once from a spreadsheet, and [Place avatar](avatar.md) sets the icon shown for one.

## Operations in this section

<!-- endpoint-reference:start -->

#### Place avatar

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/place/avatar/upload`](avatar.md#post-place-avatar-upload) | POST | Upload place avatar |
| [`/place/avatar/assign`](avatar.md#post-place-avatar-assign) | POST | Assign place icon |

#### Place import

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/place/import/start`](import.md#post-place-import-start) | POST | Start POI import |
| [`/place/import/read`](import.md#post-place-import-read) | POST | Read POI import |
| [`/place/import/list`](import.md#post-place-import-list) | POST | List POI imports |
| [`/place/import/download_failed`](import.md#post-place-import-download_failed) | POST | Download failed POI rows |
| [`/place/import/finish`](import.md#post-place-import-finish) | POST | Finish POI import |

#### Places (POI)

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/place/read`](work-with-poi.md#post-place-read) | POST | Read place |
| [`/place/list`](work-with-poi.md#post-place-list) | POST | List places |
| [`/place/create`](work-with-poi.md#post-place-create) | POST | Create place |
| [`/place/search_location`](work-with-poi.md#post-place-search_location) | POST | Search places by location |
| [`/place/update`](work-with-poi.md#post-place-update) | POST | Update place |
| [`/place/delete`](work-with-poi.md#post-place-delete) | POST | Delete place |
| [`/place/batch_convert`](work-with-poi.md#post-place-batch_convert) | POST | Convert place batch |
| [`/place/upload`](work-with-poi.md#post-place-upload) | POST | Upload places |

<!-- endpoint-reference:end -->
