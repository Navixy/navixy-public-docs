---
description: >-
  How to find who changed a workspace, a role, or an API key in the audit log of
  Console, see the other changes of the same request, and pass a change to a developer.
tags:
  - how-to
  - beta
noIndex: true
---

# Investigate changes

## Find out who made a change

You know what changed, such as a workspace that was renamed or a member who was removed, and you want to know who did it and what exactly changed. To see the events, you need a role that includes the product that records it: **Console** for organizations, workspaces, members, invitations, and API keys, **IAM** (Identity and Access Management, which is represented in the **Users and roles** section) for roles, and **Billing** for plans and payments.

The audit log finds a record's events by its ID. A workspace's ID is on **Organization**: click the workspace's name on the **Workspaces** tab, then <picture><source srcset="../.gitbook/assets/icons/content_copy-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/content_copy.svg" alt="Copy workspace ID" data-size="line"></picture> next to **ID**. A person's ID is on **Users and roles**: click the person's name in the **Users** table, then <picture><source srcset="../.gitbook/assets/icons/content_copy-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/content_copy.svg" alt="Copy user ID" data-size="line"></picture> next to **User ID**. Copy the ID before you start. Console doesn't show the ID of a role or an API key. For them, filter the log by record type and date instead.

To find who made a change, follow these steps:

1. Click **Audit log** on the sidebar.
2. Click <picture><source srcset="../.gitbook/assets/icons/filter_list-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/filter_list.svg" alt="Filter events" data-size="line"></picture> above the list.
3. Click **Record ID** and paste the ID you previously copied into the row that appears. Otherwise, click **Record type** and select the type, then click **Add filter**, click **Date**, select the day of the change, and click **Done**. The **Record type** list offers only the types of the events already loaded.
4. Click **Apply**.
5. In the list, find the event with the time of the change and click <picture><source srcset="../.gitbook/assets/icons/open_in_full-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/open_in_full.svg" alt="Open event details" data-size="line"></picture> in its **Time** cell. If several records of the type changed that day, compare the ID under **Record** with the one you copied.

The details show who made the change under **Performed by** and the old and new values under **Event data**. If the performer is shown as **Unknown**, Console has no record of the person or key, and only their ID remains.

## Find the other changes that a request made

One action in Console or one request from an app can change records in several products at once. For example, creating an API key also gives the key a role, so the audit log shows events from both Console and IAM. To see the full effect of an action, such as which role a new API key received, list every event that it caused. These events have the same trace ID.

To find the other changes that a request made, follow these steps:

1. In the audit log, open the details of one of the events.
2. Click **View related events**.

The list shows every event with the same trace ID, from every product that recorded one. The details panel closes, the search and the other filters are cleared, and a **Trace ID** filter with that ID is set.

## Pass a change to a developer

A developer who looks into a change needs the event IDs, not its description. With the trace ID, they can find the request in the app's own logs. With the record ID and the time of the event, they can read the same event through the Navixy GraphQL API.

To pass a change to a developer, follow these steps:

1. In the audit log, open the details of the event.
2. Click <picture><source srcset="../.gitbook/assets/icons/content_copy-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/content_copy.svg" alt="Copy trace ID" data-size="line"></picture> next to **Trace ID**.
3. Click <picture><source srcset="../.gitbook/assets/icons/content_copy-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/content_copy.svg" alt="Copy record ID" data-size="line"></picture> next to the record's **ID**.
4. Click <picture><source srcset="../.gitbook/assets/icons/content_copy-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/content_copy.svg" alt="Copy event ID" data-size="line"></picture> next to **Event ID**.
5. Send the three IDs with the time of the event and the product that recorded it.

For the whole event, select it in the list and click **Export to JSON**. The file contains the event as the product recorded it, including the changed data. See [Audit log](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/audit-log) in Navixy GraphQL API to learn what the developer can do with the IDs.

