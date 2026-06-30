---
title: Not all devices are shown in the objects list
description: Check service plan limits on the user account; the most restrictive active plan controls how many trackers appear in the objects list
---

# Not all devices are shown in the objects list

## Question

Not all devices are shown in the objects list. Why?

## Answer

First, check if any filters are applied:

* Clear all filters in the tracker list.
* Remove any text from the search field.

If the issue persists, it’s likely due to a service plan limitation on the number of visible trackers. Here’s how this works:

If the user has at least one tracker assigned to a plan that limits the number of allowed trackers, and the actual number of trackers exceeds that limit, the platform will only display the number of trackers allowed by the most restrictive plan.

**Example:** A user has 100 trackers:

* 99 trackers use a plan with a 100-tracker limit.
* 1 tracker uses a plan with a 5-tracker limit.

In this case, the user will only see **5 trackers**, because the plan with the 5-tracker limit affects visibility.

**How to fix it:** Adjust the restrictive plan (in this example, remove or update the plan that limits the user to 5 trackers).

## Links

* [Object list](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/tracking/objects-list)
* [Maximum device count for a user](https://app.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/s/5dyjvbUVXEbs60EnxnDz/using-navixy/maximum-device-count-for-a-user)
