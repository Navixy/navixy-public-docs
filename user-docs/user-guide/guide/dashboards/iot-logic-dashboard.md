# IoT Logic dashboard

The **IoT Logic Dashboard** is the fourth tab of the Dashboards app, and it answers "how is IoT Logic being used across the fleet?" (the [Technical Conditions Dashboard](technical-conditions-dashboard.md) covers "how well is the fleet's hardware performing, and what needs attention?"). It pulls flow and node execution activity into a single view. That way, you can see which devices, flows, and nodes are actually processing data without opening [IoT Logic](../account/iot-logic/README.md) itself.

{% hint style="info" %}
The IoT Logic Dashboard is currently in beta (v1.0.0), an early version released to be shaped by real feedback. If something is missing, confusing, or could be more useful, use the **Send Feedback** button at the top of the tab. The four dashboards version independently, so the Fleet Live Status, Trips, Technical Conditions, and IoT Logic tabs may show different version numbers.
{% endhint %}

Want more customization and detail? [Dashboard Studio](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/dashboard-studio) lets you build fleet analytics tailored to your own KPIs using IoT Query data.

## Data window and refresh

The dashboard reads from the full IoT Logic usage history available for your account. There is no date picker, and the window can't be changed. In practice this covers roughly the last 7 days, the typical retention horizon for the underlying store.

The dashboard refreshes automatically every **90 seconds** while the browser tab is in focus, and the **Update** button in the top-right corner forces an immediate refresh.

## Fleet and flow summary

Three KPI tiles frame how much of the fleet and how many flows were involved in IoT Logic activity over the past week: **Total objects**, **Objects active in IoT logic**, and **IoT flows in use**.

| Tile                             | What it shows                                                                                        |
| --------------------------------- | ----------------------------------------------------------------------------------------------------- |
| **Total objects**                 | Every device registered in your account.                                                              |
| **Objects active in IoT logic**   | Number of devices that sent data through at least one IoT Logic flow in the last 7 days.              |
| **IoT flows in use**              | Number of distinct IoT Logic flows that processed data from at least one device in the last 7 days.   |

<details>

<summary>What the gap between Total objects and Objects active in IoT logic means</summary>

The gap between these two is your automation coverage. Check it after rolling out a new flow to confirm every device you intended to include is actually feeding it. A device that shows Online on the [Fleet Live Status Dashboard](fleet-live-status-dashboard.md) but is absent from this count is connected to the Navixy platform without being connected to your logic.

</details>

## Node execution

Two bar charts, **Top 10 Action nodes executed** and **Top 10 Webhook nodes executed**, rank the busiest [Action](../account/iot-logic/nodes/action-node.md) and [Webhook](../account/iot-logic/nodes/webhook-node.md) nodes in your flows over the past week. Each bar represents one flow-and-node pair, labeled `<Flow> : <Node>`, ranked by total execution count. These two charts cover only Action and Webhook nodes. Other node types in your flows don't appear here.

<details>

<summary>What an unusual execution count tells you</summary>

A node executing far more than its peers usually means a trigger condition that's too loose, or a loop, rather than a genuinely busy fleet. On the webhook side, the count is also the volume your receiving endpoint gets. Check this chart before assuming a rate-limited integration is the endpoint's fault. The more common problem is the opposite one: a node you expect to see is missing from the top 10 entirely. That gap usually means a condition stopped matching after a device or configuration change, a silent failure nothing else on the platform surfaces.

</details>

## IoT Logic device drill-down

The **IoT Logic device drill-down** table is the day-by-day record of exactly which device triggered each flow and node, and how many times, over the last 7 days. Each row is one flow, node, object, and day combination. The same flow, node, and object can appear on several rows, one per day they were active together. It's the panel to open when someone asks whether a specific automation fired for a specific device on a specific day. None of the KPIs or charts above can answer that question on their own.

Every column is sortable by clicking its header.

When the table is empty, it shows "No IoT Logic usage found for the last 7 days." That message can mean either that you have no flows at all or that your flows exist but nothing matched. Check the KPI tiles above to tell which one you're looking at.

## Exporting panel data

Every panel here, both charts and tables, has the same CSV and PDF export options described in [Exporting data](README.md#exporting-data).
