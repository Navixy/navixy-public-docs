---
description: >-
  Navixy's pre-built dashboards answer your fleet's daily operational questions
  the moment you open the app, with telemetry from every device already
  aggregated and ready to act on.
---

# Dashboards

The **Dashboards** app provides ready-made fleet views that answer the questions operations teams ask every day. It tells you which vehicles are active, which ones need attention, and whether hardware is performing as expected. It also covers how trip activity has changed over the week and how much fuel the fleet holds. Each dashboard covers a specific time horizon and type of question, so you get the right view without switching between the Objects list, the map, and individual reports.

The app complements the [Objects list](../tracking/objects-list/) and [History view](../tracking/history-view/) rather than replacing them. Use the dashboards for fleet-wide situational awareness, and the existing views when you need to drill into a single object or pull a report for a custom time range.

{% hint style="info" %}
The Dashboards app is currently in beta. Each dashboard versions independently, because the calculations behind them differ, so the tabs you see may carry different version numbers. If something is missing, confusing, or could be more useful, the **Send feedback** button in the top-right corner of any tab is the fastest way to share it.
{% endhint %}

## Available dashboards

The app organizes its views into tabs, each focused on a distinct operational question.

| Tab                                                            | Question it answers                                                    | Data window                            |
| -------------------------------------------------------------- | ---------------------------------------------------------------------- | -------------------------------------- |
| [**Live status**](fleet-live-status-dashboard.md)              | What is the fleet doing right now?                                     | Live, refreshed every 90 seconds       |
| [**Trips**](trips-dashboard.md)                                | What did the fleet do over the past week?                              | Last 7 days                            |
| [**Conditions**](technical-conditions-dashboard.md)            | How well is the fleet's hardware performing, and what needs attention? | Full history (~7 days)                 |
| [**IoT Logic**](iot-logic-dashboard.md)                        | How is IoT Logic being used across the fleet?                          | Full history (~7 days)                 |
| [**Fuel**](fuel-dashboard.md)                                  | How much fuel does the fleet hold, and who needs refueling?            | Latest readings, with a 30-day trend   |

The **Live status** tab opens by default.

{% hint style="info" %}
Three tabs carry a period in their label: they read **Trips, 14 days**, **Conditions, 14 days**, and **IoT Logic, 14 days**. The panels behind them cover the periods listed in the **Data window** column above, not 14 days. Trust the panel descriptions over the tab label.
{% endhint %}

## What a sub-user sees

A sub-user sees only the objects, geofences, and points of interest assigned to their account. Every panel, chart, and table on every tab is filtered to those items, so two people looking at the same tab on the same account can see different totals. For how the same rule applies to vehicles elsewhere in Navixy, see [Sub-user vehicle visibility rules](https://app.gitbook.com/s/DsdXBoGC1KF2ZkUJgWs5/using-navixy/subuser-vehicle-visibility-rules).

Access is item-based today. The dashboards filter by the items assigned to the sub-user, not by the sub-user's individual permissions. A sub-user with access to a device therefore sees that device on every tab.

The app loads these assignments before it draws anything. Two messages tell you that the load didn't finish:

* **Dashboard unavailable**, with the text "Permissions could not be loaded." No panel opens. Click **Retry**. If the message keeps coming back, the account may have no permission to view objects at all, and an administrator needs to grant it.
* A banner reading **Geofence list could not be loaded**, **POI list could not be loaded**, or **Geofence and POI lists could not be loaded**. The tab still opens, but the **Geofences** and **Points of Interest** panels on the **Live status** tab can show as empty even when you do have data there. Click **Retry** in the banner.

## What you can do with the dashboards

The dashboards are built around the operational questions that come up most often in fleet management, and they update automatically so the answers are always current.

| <p><strong>Check fleet status without opening multiple screens</strong><br><br>At the start of a shift or at any point during the day, you can see how many vehicles are online and reporting GPS, which ones have gone the longest without contact, and how the fleet splits across moving, parked, and idling. All of these answers are available in a single view.</p> | <p><strong>Spot inactivity and idling before they appear in formal reports</strong><br><br>Vehicles that haven't reported in hours and units running their engines while stationary both accumulate costs that aren't immediately visible in trip data. The dashboards surface these patterns as they develop, not after the fact.</p>                                             |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p><strong>Review weekly trip activity across the fleet</strong><br><br>Without running a custom report, you can see total distance, trip counts, average duration, and which vehicles are carrying the most or least load. This visibility makes it straightforward to identify under-used assets or workload imbalances across a rolling seven-day window.</p>            | <p><strong>Monitor hardware health across all devices</strong><br><br>GPS fix quality, voltage trends, odometer readings, and active fault codes from the vehicle's OBD interface all appear in a single view. Deteriorating hardware tends to show gradual signals before it causes missed trips or silent devices, and those signals are visible here well before a device goes dark.</p> |
| <p><strong>Confirm your IoT Logic automations are actually running</strong><br><br>The <strong>IoT Logic</strong> tab shows which devices, flows, and nodes processed data in the last 7 days. A flow that quietly stopped matching produces no error anywhere else in the Navixy platform. This dashboard is where you catch it.</p> | <p><strong>See which tanks are running low</strong><br><br>The <strong>Fuel</strong> tab ranks the emptiest tanks in the fleet by percentage and by volume, and lists the objects that consumed the most fuel over the CAN bus in the last 24 hours. Both answers are available without opening a fuel report.</p> |
| <p><strong>Chain the dashboards together for one investigation</strong><br><br><strong>Live status</strong> flags objects as offline, the <strong>Conditions</strong> tab's Latest diagnostics tells you whether their hardware is failing or they're simply parked, and <strong>Trips</strong> confirms whether they did any work this week at all. Together they turn one flagged object into an answer, not a bigger question.</p> | <p><strong>See what your team sees</strong><br><br>A sub-user's dashboards are filtered to the objects, geofences, and points of interest assigned to them, so the totals on their screen differ from yours. Check what they can reach before treating a gap in their view as missing data.</p> |

## Searching a dashboard

The search field at the top of every tab filters the active tab's content: widget titles, panel help text, and the values already loaded in its tables. **Ctrl+K** or **⌘K** puts the cursor in it. Matching content stays visible and highlighted while the rest of the tab dims.

A counter next to the field shows how many matches the tab holds, and the arrow buttons beside it move to the previous and the next match in turn.

Search results come only from the active tab, and only from data already loaded on screen. It can't return a value from a tab you haven't opened, or a row a table hasn't loaded yet. It can still detect a match elsewhere: if your query matches a panel title or description on another tab, a banner offers a one-click switch to that tab.

## Customizing the layout

**Custom layout**, next to the search field, switches the active tab into edit mode so you can tailor it to how you work. On a desktop-sized screen you can hide, drag, and resize widgets. On a narrow screen you can only hide them. Restore a hidden widget from the **Hidden** menu, or click **Reset layout** to return to the default arrangement.

Click **Done** to leave edit mode. Layout changes are saved to your browser for your account, so they don't follow you to a different device or appear for other users on the account.

In edit mode, each visible tab also shows a **×** you can click to hide that entire dashboard. At least one tab must stay visible. Hidden dashboards reappear from **Hidden dashboards** in the tab bar, or you can bring all of them back at once with **Show all dashboards**.

Hiding, moving, and resizing is the whole of what edit mode does. You can't add a panel, change a metric, or create a tab of your own. For that, see [Moving to Dashboard Studio](#moving-to-dashboard-studio).

{% hint style="info" %}
When a dashboard receives a new version, the app discards any layout you saved for it rather than applying it on top of the update. A banner explains that the saved layout was from an older version.
{% endhint %}

## Working with a single panel

Three controls sit in the top-right corner of a panel, next to its export icon. The third, **Customize in Dashboard Studio**, is covered in [Moving to Dashboard Studio](#moving-to-dashboard-studio).

**How this metric is calculated** opens the panel's own description: what the number counts, which threshold it applies, and over which period. Read it before you act on a figure that surprises you. Several panels look similar but count different things, and the description is where that difference is stated.

**Choose visible columns** appears on table panels. It opens a list of the table's columns, each with a checkbox, so you can hide the ones you don't need and keep the table readable on a narrow screen. **Show all columns** brings back every column at once. At least one column must stay visible, so the last remaining checkbox can't be cleared. The choice applies to the current view, including what the panel exports.

## Units of measurement

The dashboards convert distances, speeds, altitudes, and fuel volumes into the measurement system set on your account. The unit appears next to the value: beside the number on a KPI tile, in the column header of a table, and in a chart's tooltip. An account set to Imperial reads **Max speed (mph)** in a table where a metric account reads **Max speed (km/h)**.

Five measurement systems are available. Set yours in [Profile settings](../account/profile.md).

| Measurement system     | Distance | Speed | Altitude | Fuel volume    |
| ---------------------- | -------- | ----- | -------- | -------------- |
| Metric                 | km       | km/h  | m        | L              |
| Metric with US gallons | km       | km/h  | m        | gal (US)       |
| Imperial               | mi       | mph   | ft       | gal (imperial) |
| US                     | mi       | mph   | ft       | gal (US)       |
| Nautical               | nmi      | kn    | m        | L              |

Metric is the fallback when an account has no measurement system set.

{% hint style="info" %}
The Dashboards app converts distance, speed, altitude, and volume, and nothing else. A fuel level reported as a percentage reads the same on every account, and a temperature column keeps the unit it arrives in.
{% endhint %}

Panel titles and panel descriptions aren't converted. They state thresholds in metric units, because that is how the calculation behind the panel defines them, and they read the same on every account. A tile titled **Below 20 L** keeps that title on an Imperial account, while the volumes in the tables and charts around it appear in gallons.

## Exporting data

Every panel in the Dashboards app, both charts and tables, has a download icon in its top-right corner. Click it and choose **Export to CSV** or **Export to Excel**. The export reflects the panel's state at the moment you click, including your column choices. Use it to capture a snapshot for a handover, a weekly summary, or further analysis in a spreadsheet.

To export an entire tab rather than one panel at a time, use **Export PDF** in the top-right corner. It captures the tab as currently displayed, matching its current sort order and light or dark theme. A table too long to fit is cut off with a note pointing you to that panel's own CSV or Excel export instead.

{% hint style="info" %}
The dashboards don't currently support global filters, custom time ranges, or shared links.
{% endhint %}

## When a dashboard can't load

Three messages explain a dashboard that doesn't open, beyond the permission messages covered in [What a sub-user sees](#what-a-sub-user-sees).

| Message                                                        | What it means                                                                                                                                                              |
| -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| "This account has too many objects for this panel to load."   | The panel reached the row limit it can process for this account. It won't resolve on its own, so contact support with the tab and panel name.                              |
| "Too many requests. Please wait a moment and refresh."        | The app sent more queries than the rate limit allows, usually after repeated manual refreshes. Wait a few seconds, then reload the page.                                    |
| **Session key required** or **Invalid session key**           | The app was opened outside a Navixy session. Open it from the Navixy sidebar rather than from a saved link, because the link carries a session key that eventually expires. |

## Sending feedback

The **Send feedback** button in the top-right corner of any dashboard tab opens a short form. You can select the specific widget your feedback relates to (or the dashboard as a whole), write a message up to 399 characters, and send it directly to the product team.

During the beta, the **Send feedback** button is the most direct channel for influencing how the dashboards evolve. Feedback about a specific panel is more actionable than a general comment, though both are welcome. For example: "the Top 10 longest unreported table would be more useful if it showed the last known location".

## Moving to Dashboard Studio

The dashboards on this page use a fixed set of panels and metrics built around the most common operational needs. [Dashboard Studio](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/dashboard-studio), built on [IoT Query](https://navixy.com/en/iot-query), removes that fixed set. It lets you choose your own metrics, create and manage your own dashboard tabs, add and remove panels, and build custom reports from the same telemetry.

The app offers the move in four places:

* A dismissible banner above the tabs.
* A **Customize in Dashboard Studio** action on each panel.
* An **Upgrade to Dashboard Studio** button in the layout editor.
* A **Need different KPIs?** block at the bottom of the page.

Any of them opens the **Unlock full customization** dialog. Describe the dashboards or metrics you need in the optional message box, then click **Get access to Dashboard Studio**. The request goes to the Navixy team, who contact you about activation. The dialog needs an active Navixy session, so open the dashboards from the Navixy sidebar before you send a request.
