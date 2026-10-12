---
description: >-
  See how much fuel the fleet holds, which tanks are close to empty, and which
  objects consumed the most over the CAN bus in the last 24 hours.
---

# Fuel dashboard

The **Fuel** tab of the Dashboards app answers "how much fuel does the fleet hold, and who needs refueling?" It brings tank levels, refueling candidates, and CAN bus consumption together into a single view. That way, you can plan refueling and spot unusual consumption without running a fuel report for each object.

{% hint style="info" %}
The Fuel tab is newer than the other four and changes more often. If something is missing, confusing, or could be more useful, use the **Send feedback** button at the top of the tab. Each dashboard versions independently, because the calculations behind them differ.
{% endhint %}

Want more customization and detail? [Dashboard Studio](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/dashboard-studio) lets you build fleet analytics tailored to your own KPIs using IoT Query data.

## Where the numbers come from

Three independent kinds of fuel data feed this dashboard, and an object can send one, two, or none of them.

| Data                    | What produces it                                       | Which panels use it                                                             |
| ----------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------- |
| Fuel level, percent | A sensor that reports how full the tank is, as a %      | **Fuel in %**, **Below 10%**, **Fleet health**, **Refuel needed**, **Fuel level**, **Lowest 10 (%)** |
| Fuel level, volume | A sensor that reports the remaining volume              | **Fuel in liters**, **Below 20 L**, **Lowest 10 by volume**                     |
| Consumption, CAN | The vehicle's CAN bus, reporting cumulative fuel used   | **With consumption**, **Consumed 24h**, **Top 10 consumers**, **Consumption**   |

Every panel discards readings that look like sensor noise: a fuel percentage below 0.01% or above 105%, and a volume below 0.01. An object reporting only such values is counted nowhere on the tab.

This split explains most surprises on the tab. An object with a percentage sensor and no volume sensor is counted in **Fuel in %** and never appears in **Lowest 10 by volume**. An object that reports consumption over CAN but has no level sensor appears only in the consumption panels. The **Fuel units** chart is the fastest way to see how your own fleet divides across these cases.

For how fuel sensors are configured in the first place, see [Measurement sensors](../devices-and-settings/vehicle-sensors/measurement-sensors/README.md).

## Data window and refresh

The KPI tiles read the latest value each object reported. **Fuel in %** and **Fuel in liters** count objects that reported in the last hour, and the consumption tiles cover the last 24 hours. Only the **Avg fuel trend (30d)** chart looks further back, over 30 days.

The dashboard refreshes automatically every **90 seconds** while the browser tab is in focus, and the **Update** button in the top-right corner forces an immediate refresh.

## Fleet coverage

The first row of tiles tells you how much of the fleet reports fuel data at all. Read it before anything else on the tab, because every other panel covers only the objects counted here.

| Tile                  | What it shows                                                        |
| --------------------- | --------------------------------------------------------------------- |
| **Total objects**     | Every object in your account.                                        |
| **With consumption**  | Objects that reported CAN fuel consumption in the last 24 hours.     |
| **Fuel in %**         | Objects that sent percentage fuel data in the last hour.             |
| **Fuel in liters**    | Objects that sent volume fuel data in the last hour.                 |
| **Fleet health**      | Share of the percentage-reporting objects whose latest level is above 25%. |

<details>

<summary>Why Fuel in % plus Fuel in liters can exceed Total objects</summary>

An object fitted with both kinds of sensor is counted in both tiles. The two numbers overlap rather than divide the fleet, so their sum can be larger than **Total objects**. The **Fuel units** chart resolves the overlap: its **Liters and percent** slice is exactly the set of objects counted twice.

The reverse gap is more common. When **Fuel in %** and **Fuel in liters** together fall well short of **Total objects**, most of the fleet either carries no fuel sensor or hasn't reported in the last hour.

</details>

## Refueling candidates

The second row of tiles answers the operational question: who needs fuel now?

| Tile               | What it shows                                                |
| ------------------ | ------------------------------------------------------------- |
| **Consumed 24h**   | Total CAN fuel the fleet used in the last 24 hours.          |
| **Refuel needed**  | Objects whose latest level is below 25%.                     |
| **Below 10%**      | Objects whose latest level is below 10%.                     |
| **Below 20 L**     | Objects whose latest remaining volume is below 20 liters.    |

**Refuel needed** and **Below 10%** both read percentage sensors, at two different thresholds. **Below 10%** is the subset that can't wait. **Fleet health**, in [Fleet coverage](#fleet-coverage), reads the same 25% threshold the other way round. It gives the share of objects that are fine rather than the count that isn't.

{% hint style="info" %}
The 25%, 10%, and 20-liter thresholds are fixed and can't be changed. **Below 20 L** keeps that title on every account, because panel titles aren't converted, and the number it shows is a count of objects rather than a volume. The volumes in the charts and tables below do follow your account's measurement system. See [Units of measurement](README.md#units-of-measurement).
{% endhint %}

## Fleet distribution

Two charts sit below the tiles and split the fleet rather than count it.

**Fuel units** is a donut chart of the latest report type per object: **Liters only**, **Percent only**, **Liters and percent**, or **No fuel data**. Slices with no objects are hidden, so a fleet with no volume sensors shows only two slices.

**Fuel level** is a bar chart of how many objects sit in each percentage range, based on each object's latest reading. The ranges are 1 to 10%, 10 to 25%, 25 to 50%, 50 to 75%, and above 75%. It shows the shape of the fleet's fuel state, where the tiles give only counts above and below a threshold.

## Fuel trend

**Avg fuel trend (30d)** plots the daily fleet average fuel level over the last 30 days, as a percentage and as a volume. It is the only panel on the tab with history behind it.

<details>

<summary>How to read a falling trend line</summary>

A gradual decline over several days across the whole fleet usually means refueling has fallen behind consumption, not that any single tank is emptying. Compare it against **Consumed 24h**: a steady consumption figure alongside a falling average points to a refueling gap, while a rising consumption figure points to heavier operation.

A sudden step in the line more often reflects a change in which objects report than a change in fuel. An object that stops reporting drops out of the average, and if it was fuller or emptier than the rest, the average moves. Check **Fuel in %** for the same day to see whether the reporting population changed.

</details>

## Lowest tanks

Two ranking charts name the individual objects closest to empty.

**Lowest 10 (%)** ranks the ten emptiest tanks by latest percentage. **Lowest 10 by volume** ranks the ten lowest remaining volumes. The two lists can name different objects, because a small tank at 40% can hold less fuel than a large tank at 20%.

## Fleet fuel details

The **Details** table is the detail layer beneath the tiles and charts above. It lists the latest percentage and volume per object, newest reading first. Every column is sortable by clicking its header.

The table lists only objects with at least one valid reading, and shows at most 500 rows.

When no object in your account has reported fuel level data, the table shows "No fuel data." That message means the fleet carries no fuel level sensors, or none of them have reported yet.

## Consumption

Two panels at the bottom of the tab cover CAN bus consumption rather than tank level.

**Top 10 consumers** ranks the objects with the highest CAN fuel use in the last 24 hours. **Consumption** is the table beneath it, listing 30-day and 24-hour use per object, so you can tell a single heavy day from a sustained pattern. It shows at most 500 rows.

Both read the vehicle's cumulative fuel counter and report the difference across the period, rather than a figure the vehicle sends directly.

When no object reports consumption over CAN, the table shows "No consumption data." Consumption reporting depends on the vehicle's CAN bus and on the device reading it, so a fleet can have full fuel level coverage and no consumption data at all.

## Exporting panel data

Every panel here, both charts and tables, has the same CSV, Excel, and PDF export options described in [Exporting data](README.md#exporting-data).
