---
description: >-
  Navixy's pre-built dashboards answer your fleet's daily operational questions
  the moment you open the app, with telemetry from every device already
  aggregated and ready to act on.
---

# Dashboards

The **Dashboards** app provides ready-made fleet views that answer the questions operations teams ask every day. It tells you which vehicles are active, which ones need attention, how trip activity has changed over the week, and whether hardware is performing as expected. Each dashboard covers a specific time horizon and type of question, so you get the right view without switching between the Objects list, the map, and individual reports.

The app complements the [Objects list](../tracking/objects-list/) and [History view](../tracking/history-view/) rather than replacing them. Use the dashboards for fleet-wide situational awareness, and the existing views when you need to drill into a single object or pull a report for a custom time range.

{% hint style="info" %}
The Dashboards app is currently in beta. Each dashboard versions independently, because the calculations behind them differ, so the tabs you see may carry different version numbers. If something is missing, confusing, or could be more useful, the **Send Feedback** button in the top-right corner of any tab is the fastest way to share it.
{% endhint %}

## Available dashboards

The app organizes its views into tabs, each focused on a distinct operational question.

| Dashboard                                                           | Question it answers                                                    | Data window                      |
| ------------------------------------------------------------------- | ---------------------------------------------------------------------- | -------------------------------- |
| [Fleet Live Status Dashboard](fleet-live-status-dashboard.md)       | What is the fleet doing right now?                                     | Live, refreshed every 90 seconds |
| [Trips Dashboard](trips-dashboard.md)                               | What did the fleet do over the past week?                              | Last 7 days                      |
| [Technical Conditions Dashboard](technical-conditions-dashboard.md) | How well is the fleet's hardware performing, and what needs attention? | Full history (~7 days)           |
| [IoT Logic Dashboard](iot-logic-dashboard.md)                       | How is IoT Logic being used across the fleet?                          | Full history (~7 days)           |

The **Fleet Live Status Dashboard** is the tab that opens by default.

## What you can do with the dashboards

The dashboards are built around the operational questions that come up most often in fleet management, and they update automatically so the answers are always current.

| <p><strong>Check fleet status without opening multiple screens</strong><br><br>At the start of a shift or at any point during the day, you can see how many vehicles are online and reporting GPS, which ones have gone the longest without contact, and how the fleet splits across moving, parked, and idling. All of this is available in a single view.</p> | <p><strong>Spot inactivity and idling before they appear in formal reports</strong><br><br>Vehicles that haven't reported in hours and units running their engines while stationary both accumulate costs that aren't immediately visible in trip data. The dashboards surface these patterns as they develop, not after the fact.</p>                                             |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p><strong>Review weekly trip activity across the fleet</strong><br><br>Without running a custom report, you can see total distance, trip counts, average duration, and which vehicles are carrying the most or least load. This visibility makes it straightforward to identify under-used assets or workload imbalances across a rolling seven-day window.</p>            | <p><strong>Monitor hardware health across all devices</strong><br><br>GPS fix quality, voltage trends, odometer readings, and active fault codes from the vehicle's OBD interface all appear in a single view. Deteriorating hardware tends to show gradual signals before it causes missed trips or silent devices, and those signals are visible here well before a device goes dark.</p> |
| <p><strong>Confirm your IoT Logic automations are actually running</strong><br><br>The IoT Logic Dashboard shows which devices, flows, and nodes processed data in the last 7 days. A flow that quietly stopped matching produces no error anywhere else in the Navixy platform. This dashboard is where you catch it.</p> | <p><strong>Chain the dashboards together for one investigation</strong><br><br>Fleet Live Status flags objects as offline, Technical Conditions' Latest diagnostics tells you whether their hardware is failing or they're simply parked, and Trips confirms whether they did any work this week at all. Together they turn one flagged object into an answer, not a bigger question.</p> |

## Searching a dashboard

The **Search** field at the top of every tab (or the **Ctrl+K** / **⌘K** shortcut) filters the active tab's content: widget titles, panel help text, and the values already loaded in its tables. Matching content stays visible and highlighted while the rest of the tab dims.

Search results come only from the active tab and only from data already loaded on screen, so it can't return a value from a tab you haven't opened or a row a table hasn't loaded yet. It can still detect a match elsewhere: if your query matches a panel title or description on another tab, a banner offers a one-click switch to that tab.

## Customizing the layout

**Custom layout**, next to Search, switches the active tab into edit mode so you can tailor it to how you work. On a desktop-sized screen you can hide, drag, and resize widgets; on a narrow screen you can only hide them. Restore a hidden widget from the **Hidden** menu, or click **Reset layout** to return to the default arrangement.

Click **Done** to leave edit mode. Layout changes are saved to your browser for your account, so they don't follow you to a different device or appear for other users on the account.

In edit mode, each visible tab also shows a **×** you can click to hide that entire dashboard. At least one tab must stay visible. Hidden dashboards reappear from **Hidden dashboards** in the tab bar, or you can bring all of them back at once with **Show all dashboards**.

{% hint style="info" %}
If a dashboard receives a new version, any layout you saved for it is discarded rather than applied on top of the update, and a banner explains that the saved layout was from an older version.
{% endhint %}

## Exporting data

Every panel in the Dashboards app, both charts and tables, has a download icon in its top-right corner. Click it to export the panel's current data as a CSV file. The export reflects the panel's state at the moment you click, so it's useful for capturing a snapshot for a handover, a weekly summary, or further analysis in a spreadsheet.

To export an entire tab rather than one panel at a time, use **Export PDF** in the top-right corner. It captures the tab as currently displayed, matching its current sort order and light or dark theme. A table too long to fit is cut off with a note pointing you to that panel's own CSV export instead.

{% hint style="info" %}
The dashboards don't currently support global filters, custom time ranges, or shared links.
{% endhint %}

## Sending feedback

The **Send Feedback** button in the top-right corner of any dashboard tab opens a short form. You can select the specific widget your feedback relates to (or the dashboard as a whole), write a message up to 399 characters, and send it directly to the product team.

During the beta, the **Send Feedback** button is the most direct channel for influencing how the dashboards evolve. Feedback scoped to a specific panel ("the Top 10 longest unreported table would be more useful if it showed the last known location") is more actionable than general comments, but both are welcome.

## Custom dashboards with Dashboard Studio

The dashboards described on this page use a fixed set of panels and metrics built around the most common operational needs. If your operations require dashboards built around your own KPIs, business rules, or reporting logic, [Dashboard Studio](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/dashboard-studio) in [IoT Query](https://navixy.com/en/iot-query) lets you build and share custom dashboards from your telemetry data.
