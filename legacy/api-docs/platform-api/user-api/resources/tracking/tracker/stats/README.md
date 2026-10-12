---
title: Stats
description: Engine hours and distance travelled, counted per day over a period.
---

# Stats

Stats answer how much a tracker did rather than where it went. Both operations take a period and return one figure per day, which is what a utilisation report or a maintenance schedule is built from.

For the trips behind these figures, see [Track](../../track/README.md).

## Operations in this section

<!-- endpoint-reference:start -->

#### Engine hours

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/stats/engine_hours/read`](stats_engine_hours.md#post-tracker-stats-engine_hours-read) | POST | Read engine hours |

#### Mileage

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/stats/mileage/read`](stats_mileage.md#post-tracker-stats-mileage-read) | POST | Read mileage per day |

<!-- endpoint-reference:end -->
