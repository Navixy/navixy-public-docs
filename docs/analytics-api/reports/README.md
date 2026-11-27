---
description: >-
  Read Navixy report data from your own code with the Reports API: report
  types and assets, report summaries, and links that open reports.
tags:
  - overview
  - beta
---

# Reports API overview

The Reports API is the REST API of Navixy Reports. Your app names a report type, a period, and a set of assets. The API returns the summary of that report, such as the distance and the driving time of each asset. It can also return a link that opens the report in Location Intelligence. This page is for developers who connect their own app to Reports. You need an access token, as [Authentication](../authentication.md) describes.

Every request goes to `https://reports.<REGION>.navixy.com/api`, with `eu` or `us` in place of `<REGION>`. To learn what each report shows, see [Reports](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/reports) in the Location Intelligence documentation.

## What you can do with the Reports API

* Read the report types that your token can run and the assets that it can see. Read the dates of common periods too, such as last week.
* Get the summary of a report for a period of up to 31 days, such as the distance and the driving time of each asset.
* Get a link that opens a report in Location Intelligence with the period and the assets that you chose.

## Key concepts

| Term | Definition |
| --- | --- |
| Report type | What a report shows. Each report type has a code as its ID: `trips_and_stops`, `sensor_readings`, or `driver_performance`. |
| Report summary | The summary figures of a report for a period: the figures of its **Summary** tab in Location Intelligence, for all assets and for each asset or sensor. |
| Report run | One request for a report summary. The API limits the number of report runs. |
| Period | The first and the last day, `from` and `to`, both included, on the calendar of your `timeZone`. A period can be up to 31 days long and can't end after today. |
| Trip | A movement of an asset, from the moment it starts to move until it stands still. |
| Stop | A period when an asset stood still. |

## How the Reports API works

Every operation is a `POST` request with a JSON body, sent to a path under `/api/tools/`. Every body includes `timeZone`, the IANA time zone of the person who reads the result. See [Time zone](../README.md#time-zone). An app usually works in 2 steps:

1. Your app gets the [report types and assets](report-types-and-assets.md) with `POST /api/tools/context`. The response lists the report types and the assets that your token can use, and today's date.
2. Your app gets the [report summary](report-summary.md) with `POST /api/tools/run_report_totals`. The body names the report type in `report`, the period in `from` and `to`, and the assets in `assets`.

The following request returns the summary of Trips and stops for one asset on one day:

```bash
curl --request POST \
  --url 'https://reports.<REGION>.navixy.com/api/tools/run_report_totals' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{
    "timeZone": "Europe/Berlin",
    "report": "trips_and_stops",
    "from": "2026-11-26",
    "to": "2026-11-26",
    "assets": ["019a7f3e-5c21-7a10-8000-0b2c4d6e8f01"]
  }'
```

Parameters:

* `timeZone`: The time zone of Berlin. The day of 26 November starts and ends at midnight there.
* `from` and `to`: The same day, so the period is one day. Both days are included.
* `assets`: One asset. Send `"all"` to include every asset that your token can see.

Response:

```json
{
  "totals": {
    "trips": 16,
    "distanceM": 186300,
    "drivingS": 48600,
    "stops": 17,
    "stopS": 5520,
    "maxSpeedKmh": 40.0
  },
  "assetsInScope": 1,
  "assets": [
    {
      "assetId": "019a7f3e-5c21-7a10-8000-0b2c4d6e8f01",
      "title": "Van 01 (FMB920)",
      "figures": {
        "trips": 16,
        "distanceM": 186300,
        "drivingS": 48600,
        "stops": 17,
        "stopS": 5520,
        "maxSpeedKmh": 40.0
      }
    }
  ],
  "assetsOmitted": 0
}
```

Each field name ends with its unit of measure: `M` for meters, `S` for seconds, and `Kmh` for kilometers per hour. A figure that Reports has no value for is `null`, which isn't zero. Ignore any field of a response that this reference doesn't describe: newer versions of Reports add fields.

### Which data a response includes

* **Assets and devices.** A response includes only the assets of the workspace that your token's role lets you see. For each asset, it includes only the GPS devices that the role lets you see. An asset ID that the role doesn't let you see returns HTTP 400. So does the ID of an asset that Reports has no GPS device data for yet, although the ID is valid in Navixy GraphQL API.
* **The period.** A trip or a stop belongs to the period in which it starts.
* **New data.** Reports loads new trips and readings in blocks of 4 hours. A trip that is still in progress at the end of the last block isn't in the summary yet.
* **Old data.** Reports keeps report data for a limited time. See [How far back report data goes](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/reports/how-report-data-works#how-far-back-report-data-goes) in the Location Intelligence documentation.

## Access to the Reports API

The role of your API key decides what the API returns. The workspace must have Reports, and the role must give access to it. Otherwise, every operation returns HTTP 403. A role gives access to each report type separately, and the operations accept only the report types that the role lets you run. The list of report types and assets has them in `templates`.

The role also decides which assets and GPS devices a response includes. To learn how roles are set up, see [Users and roles](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/users-and-roles) in the Navixy Console documentation.

## Limits of the Reports API

The limits of the Analytics API apply to the Reports API. See [Limits](../limits.md). Each request for a report summary is one report run. The list of report types and assets and the links to reports aren't runs. See [Daily limits](../limits.md#daily-limits).

## Section contents

* [Report types and assets](report-types-and-assets.md): Learn what the list of report types, assets, and periods contains and how to use it.
* [Report summary](report-summary.md): Learn what a report summary contains for each report type and how to read its figures.
* [Links to reports](links-to-reports.md): Create a link that opens a report with the period, the assets, and the view that you chose.
* [Reports API reference](api-reference.md): Look up every operation, its parameters and responses, and the response models.
* [Reports API errors](errors.md): Look up the errors that only the Reports API returns and how report runs are counted.
