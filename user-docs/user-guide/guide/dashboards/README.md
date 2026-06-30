---
description: >-
  Navixy's pre-built dashboards answer your fleet's daily operational questions
  the moment you open the app, with telemetry from every device already
  aggregated and ready to act on.
---

# Dashboards

The **Dashboards** app provides ready-made fleet views that answer the questions operations teams ask every day: which vehicles are active, which ones need attention, how trip activity has changed over the week, and whether hardware is performing as expected. Each dashboard covers a specific time horizon and type of question, so you get the right view without switching between the Objects list, the map, and individual reports.

The app complements the [Objects list](../tracking/objects-list/) and [History view](../tracking/history-view/) rather than replacing them. Use the dashboards for fleet-wide situational awareness, and the existing views when you need to drill into a single object or pull a report for a custom time range.

{% hint style="info" %}
The Dashboards app is currently in beta. Each dashboard versions independently, so the tabs you see may carry different version numbers. This is due to the nuances of the calculations used in different dashboards. If something is missing, confusing, or could be more useful, the Send Feedback button in the top-right corner of any tab is the fastest way to tell us.
{% endhint %}

## Available dashboards

The app organizes its views into tabs, each focused on a distinct operational question.

| Dashboard                                                           | Question it answers                                                    | Data window                      |
| ------------------------------------------------------------------- | ---------------------------------------------------------------------- | -------------------------------- |
| [Fleet Live Status Dashboard](fleet-live-status-dashboard.md)       | What is the fleet doing right now?                                     | Live, refreshed every 60 seconds |
| [Trips Dashboard](trips-dashboard.md)                               | What did the fleet do over the past week?                              | Last 7 days                      |
| [Technical Conditions Dashboard](technical-conditions-dashboard.md) | How well is the fleet's hardware performing, and what needs attention? | Full hourly statistics history   |

The **Fleet Live Status Dashboard** tab opens by default when you open the app. Switch between tabs using the tab bar at the top of the page.

## What you can do with the dashboards

The dashboards are built around the operational questions that come up most often in fleet management, and they update automatically so the answers are always current.

| <p><strong>Check fleet status without opening multiple screens</strong><br><br>At the start of a shift or at any point during the day, you can see how many vehicles are online and reporting GPS, which ones have gone the longest without contact, and how the fleet splits between moving, parked, and idling. All of this is available in a single view.</p> | <p><strong>Spot inactivity and idling before they appear in formal reports</strong><br><br>Vehicles that haven't reported in hours and units running their engines while stationary both accumulate costs that aren't immediately visible in trip data. The dashboards surface these patterns as they develop, not after the fact.</p>                                             |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p><strong>Review weekly trip activity across the fleet</strong><br><br>Without running a custom report, you can see total distance, trip counts, average duration, and which vehicles are carrying the most or least load. This makes it straightforward to identify under-used assets or workload imbalances across a rolling seven-day window.</p>            | <p><strong>Monitor hardware health across all devices</strong><br><br>GPS fix quality, voltage trends, odometer readings, and active fault codes from the vehicle's OBD interface all appear in a single view. Deteriorating hardware tends to show gradual signals before it causes missed trips or silent devices, and the dashboards surface those signals as they develop.</p> |

## Exporting data

Every panel in the Dashboards app, both charts and tables, has a download icon in its top-right corner. Click it to export the panel's current data as a CSV file. The export reflects the panel's state at the moment you click, which makes it useful for capturing a snapshot for a handover, a weekly summary, or further analysis in a spreadsheet.

{% hint style="info" %}
The dashboards do not currently support global filters, custom time ranges, or shared links.
{% endhint %}

## Sending feedback

The **Send Feedback** button in the top-right corner of any dashboard tab opens a short form. You can select the specific widget your feedback relates to (or the dashboard as a whole), write a message up to 399 characters, and send it directly to the product team.

During the beta, this is the most direct channel for influencing how the dashboards evolve. Feedback scoped to a specific panel ("the Top 5 long-unseen table would be more useful if it showed the last known location") is more actionable than general comments, but both are welcome.

## Custom dashboards with Dashboard Studio

The dashboards described on this page use a fixed layout built around the most common operational needs. If your operations require dashboards built around your own KPIs, business rules, or reporting logic, [Dashboard Studio](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/dashboard-studio) in [IoT Query](https://navixy.com/en/iot-query) lets you build and share custom dashboards from your telemetry data.
