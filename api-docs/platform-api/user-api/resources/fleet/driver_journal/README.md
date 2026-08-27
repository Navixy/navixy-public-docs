---
title: Driver journal
description: Trips categorised as business or private, so transport use can be split between them.
---

# Driver journal

A driver journal is the record of who drove what and why. Each entry is a trip with a category attached, which is what separates a reimbursable journey from a personal one.

Entries are not created from nothing. [Trip proposals](proposal.md) lists the raw trips the Navixy platform detected over a period, and [Driver journal entry](entry.md) turns a chosen one into a categorised record.

## Operations in this section

<!-- endpoint-reference:start -->

#### Driver journal entry

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/driver/journal/entry/list`](entry.md#post-driver-journal-entry-list) | POST | List driver journal entries |
| [`/driver/journal/entry/create`](entry.md#post-driver-journal-entry-create) | POST | Create driver journal entries |
| [`/driver/journal/entry/update`](entry.md#post-driver-journal-entry-update) | POST | Update driver journal entry |
| [`/driver/journal/entry/delete`](entry.md#post-driver-journal-entry-delete) | POST | Delete driver journal entries |
| [`/driver/journal/entry/download`](entry.md#post-driver-journal-entry-download) | POST | Download driver journal |

#### Trip proposals

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/driver/journal/proposal/list`](proposal.md#post-driver-journal-proposal-list) | POST | List trip proposals |

<!-- endpoint-reference:end -->
