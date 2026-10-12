---
title: Report
description: Generating tracker reports and delivering them on a schedule.
---

# Report

A report turns a period of tracking data into something a person reads. Generating one is asynchronous: the request starts a job, and the result is polled for and then retrieved or downloaded.

[Report tracker](report_tracker.md) covers that cycle. [Report schedule](report_schedule.md) covers having the Navixy platform mail a report out repeatedly instead.

For the report types available and the parameters each takes, see [Report plugins](../plugin/report_plugins.md).

## Operations in this section

<!-- endpoint-reference:start -->

#### Report schedule

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/report/schedule/create`](report_schedule.md#post-report-schedule-create) | POST | Create report schedule |
| [`/report/schedule/update`](report_schedule.md#post-report-schedule-update) | POST | Update report schedule |
| [`/report/schedule/list`](report_schedule.md#post-report-schedule-list) | POST | List report schedules |
| [`/report/schedule/delete`](report_schedule.md#post-report-schedule-delete) | POST | Delete report schedule |

#### Report tracker

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/report/tracker/generate`](report_tracker.md#post-report-tracker-generate) | POST | Generate report |
| [`/report/tracker/status`](report_tracker.md#post-report-tracker-status) | POST | Get report status |
| [`/report/tracker/retrieve`](report_tracker.md#post-report-tracker-retrieve) | POST | Retrieve report |
| [`/report/tracker/download`](report_tracker.md#post-report-tracker-download) | POST | Download report |
| [`/report/tracker/list`](report_tracker.md#post-report-tracker-list) | POST | List reports |
| [`/report/tracker/delete`](report_tracker.md#post-report-tracker-delete) | POST | Delete report |

<!-- endpoint-reference:end -->
