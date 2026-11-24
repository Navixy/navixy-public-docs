---
description: >-
  Find the assets with the most speeding, idling, and other driving violations
  over a period, and open the trips where the violations happened.
tags:
  - reference
  - beta
---

# Driver performance

**Driver performance** counts driving violations for each asset over a period: speeding, idling, high engine RPM, trips that last too long, and exits from zones. For each asset, the report shows how many violations of each type its trips and stops had, and how the total changed since the previous period. Use it to find the assets with the most violations, and then the trips where the violations happened.

This page describes what the report shows and how it counts violations. To run the report, see [Manage reports](manage-reports.md#run-a-report).

{% hint style="info" %}
**Access**

**Driver performance** is in your Reports menu when your role gives access to it. See [Access to Reports](README.md#access-to-reports).
{% endhint %}

## What the report shows

<figure><img src="../.gitbook/assets/reports-driver-performance.png" alt="The Driver performance report on the Summary tab, next to the Reports menu. At the top of the report are the assets button, the period button for last week, and the Run button. Under them are the time zone of the report and the line Data up to. The Violation types buttons are Speeding, Idling, High RPM, Trip duration, and Zone exit. Three tiles show the totals: Violations, Distance, and Driving time. The Ask AI button is at the top of the page. The Violations tile shows No rule with an asterisk, and the line under the tiles says that the previous period starts before the data. The Summary by asset table has a row for each asset, with its trips, distance, and driving time, and a column for each violation type, Total, and Δ total. Every violation cell shows No rule with an asterisk, and every Δ total cell shows a dash."><figcaption><p>Driver performance on the Summary tab: the totals in the tiles and a row for each asset. This workspace has no rule for any violation type, so every violation cell shows No rule.</p></figcaption></figure>

Here's what you can do in the report:

* Choose the assets and the period at the top of the report and click **Run**. The report opens for **All assets** and **Last week** and runs immediately.
* Read the totals for the period in the 3 tiles: **Violations**, **Distance**, and **Driving time**. Each tile compares its total with the previous period.
* Choose which violation types the report counts.
* See on a chart when the violations happened.
* Compare assets on the **Summary** tab, list the trips on the **Trips** tab, and list each violation on the **Violations** tab.
* Click a trip to open its panel, with the track on a map, the speed, and the violations of the trip.
* Hide columns and download a table as a file. See [Show and hide columns](manage-reports.md#show-and-hide-columns) and [Download a table](manage-reports.md#download-a-table).

The report uses the same trips and stops as [Trips and stops](trips-and-stops.md). **Distance** and **Driving time** are calculated in the same way. See [How the trip values are calculated](trips-and-stops.md#how-the-trip-values-are-calculated).

## Violation types

The report counts 5 violation types. Navixy checks each type on trips, on stops, or on both:

| Type | When Navixy counts a violation | Checked on |
| --- | --- | --- |
| **Speeding** | The speed is above the limit. | Trips and stops |
| **Idling** | The ignition is on while the asset stands still, for longer than the limit. | Stops |
| **High RPM** | The engine speed, in revolutions per minute (RPM), is above the limit. | Trips and stops |
| **Trip duration** | The trip lasts longer than the limit. The violation starts at the moment when the trip reaches the limit. | Trips |
| **Zone exit** | The asset leaves a [geo object](../bdr/geo-objects/), such as a depot or a delivery zone. | Trips and stops |

For example, a high engine RPM while the vehicle stands still is a violation of the stop.

Each type needs data from the GPS device:

* **Speeding** and **Zone exit** use the positions and the speed that the GPS device sends.
* **Idling** needs the ignition state.
* **High RPM** needs the engine RPM, which the GPS device reads from the vehicle's CAN bus or OBD port.

When the GPS device doesn't send the ignition state, **Idling** shows **Not supported**. When the GPS device doesn't send the engine RPM, **High RPM** shows 0.

## When a violation type shows a number

A violation type shows a number of violations only when there is a rule for that type. For **Speeding**, **Idling**, **High RPM**, and **Trip duration**, the rule is a limit: the value above which Navixy counts a violation, such as a speed of 90 km/h. **Zone exit** has no limit, but it also needs a rule. A rule belongs to your workspace or to one asset. An asset's own rule applies to that asset in place of the workspace rule. When there is no rule for a type, the report shows **No rule** for it.

Navixy calculates violations every 4 hours. The violations of a trip or a stop appear up to about 5 hours after the trip or stop ends. Until then, the trip or stop isn't calculated yet. Navixy calculates violations only for the trips and stops that it stored after it started to calculate violations. Older trips and stops show **Not calculated**.

A trip or a stop keeps the limit that applied when Navixy first calculated it. When the limit changes, the new limit applies only to the trips and stops that Navixy calculates later.

A cell without a number shows one of these values:

| Value | What it means |
| --- | --- |
| **Not calculated** | Navixy hasn't calculated any of the trips and stops in the cell yet. |
| **No rule** | There was no rule for this violation type when Navixy calculated the trips and stops. |
| **Not supported** | The GPS device doesn't send the ignition state that **Idling** needs. |
| **No data for period** | The asset has no trip or stop that starts in the period. |

An asterisk (\*) next to a value means that some trips and stops in the cell aren't calculated yet. Point to the asterisk to see how many. A number with an asterisk counts only the trips and stops that Navixy has calculated so far. The legend under the **Summary by asset** table and under the **Trips** table explains these values.

## Tiles

The three tiles show the totals of the report:

| Tile | What it shows |
| --- | --- |
| **Violations** | The number of violations of the violation types that you chose. |
| **Distance** | The total distance of the trips. |
| **Driving time** | The total driving time of the trips. |

Each tile compares its total with the previous period, for example "+6 (+18%)" or **No change**. A rise in violations is red, and a fall is green.

The previous period is the period of the same kind just before yours: the day before a day, the week before a week, the month before a month. For a range of days in the calendar, it's the same number of days just before the range. When your period includes today, or the report data ends before your period does, the report compares the same part of both periods. For example, this week up to Tuesday 16:05 is compared with last week up to Tuesday 16:05.

The line under the tiles names the previous period, or says why there is nothing to compare with. For example, the previous period can start before the earliest report data of your workspace.

## Violation type filter

The buttons next to **Violation types** choose which violation types the report counts. Click a type to leave it out, and click it again to count it again. At least one type stays chosen. The choice applies to everything on the page at once: the **Violations** tile, the chart, and all 3 tabs. The page address keeps the choice.

## Chart

The chart shows when the violations happened, with one color for each violation type. For a period of more than one day, it's **Violations by day**. For a period of one day, it's **Violations by two hours**. Each bar counts the violations of the trips and stops that start in it.

The chart appears only when the **Violations** tile shows a number.

## Tabs and tables

The report has 3 tabs: **Summary**, **Trips**, and **Violations**. Each tab has its own table. The **Trips** and **Violations** tables list the latest first.

### Summary

The **Summary by asset** table has a row for each asset of the report, with the most violations first. To sort it in another way, click a column heading. After the **Asset** column, it has these columns:

| Column | What it shows |
| --- | --- |
| **Trips**, **Distance**, **Driving time** | The number of trips, their total distance, and their total driving time. |
| A column for each violation type | The number of violations of that type. |
| **Total** | The number of violations of all the types that you chose. |
| **Δ total** | The change in **Total** since the previous period. |

To see the trips of one asset, click the asset. The **Trips** tab opens and shows only that asset's trips, and the **Violations** tab shows only its violations. The asset's name is above the table. To see every asset again, click the close icon next to the asset's name.

A trip or a stop counts in full in the period that it starts in. See [Which trips a period includes](how-report-data-works.md#which-trips-a-period-includes).

### Trips

The **Trips** table lists each trip of the period. It has the **Start**, **End**, **Asset**, **Duration**, and **Distance** of the trip, a column for each violation type that is checked on trips, and **Total**. **Idling** isn't in this table, because Navixy checks idling only on stops.

Click a trip to open its panel. See [Trip panel](#trip-panel).

### Violations

The **Violations** table lists each violation, with its **Type** and **Asset**. It also has these columns:

| Column | What it shows |
| --- | --- |
| **Time** | When the violation started. |
| **Peak** | The highest value during the violation: the speed, the idling time, the engine RPM, or the trip duration. For a zone exit, the name of the geo object that the asset left. |
| **Limit** | The limit that applied to the trip or stop. A zone exit has no limit. |
| **Duration** | How long the violation lasted. A violation shorter than a minute shows in seconds. A zone exit has no duration. |
| **Trip or stop** | The trip or the stop of the violation, by its start time. |

Click a violation of a trip to open the panel of that trip. A violation of a stop opens nothing.

The table lists only the violations of the trips and stops that Navixy has calculated. When some trips and stops aren't calculated yet, a note above the table says how many.

## Trip panel

The trip panel opens at the side of the page. To open it, click a trip on the **Trips** tab or a violation of a trip on the **Violations** tab. The panel has the title **Trip**, with the asset and the GPS device under it. It shows the following:

* The figures of the trip, as in [Trips and stops](trips-and-stops.md#trip-or-stop-panel): **Start**, **End**, **Duration**, **Driving time**, **Distance**, **Avg speed**, and **Max speed**.
* A map with the track of the trip, its start and end pins, and the violations that have a position, numbered. A **Trip duration** violation has no position, so it appears only in the list.
* **Speed**: A line of the speed during the trip. When **Speeding** has a limit, a dashed line shows the limit.
* **Violations**: The violations of the trip, numbered as on the map. Each violation shows its type, its time, its peak value against the limit, and how long it lasted. A zone exit shows the geo object instead.
* The coordinates: **Start point** and **End point**.

Click a violation in the list to mark it on the map and on the speed line.

The track has gaps where the GPS device sent no position for more than 5 minutes. A trip older than 3 months has no track.

When the trip has no violation, the panel says why under **Violations**. For example, no violation was found, the trip isn't calculated yet, or there was no rule.

## See also

* [How report data is prepared](how-report-data-works.md): Learn when a trip appears in a report, how far back report data goes, and which time zone a report uses.
* [Manage reports](manage-reports.md): Run the report, share it by its link, and download its tables.
* [Troubleshooting reports](troubleshooting.md): Find out why a violation type shows **No rule** or why a report is empty.
