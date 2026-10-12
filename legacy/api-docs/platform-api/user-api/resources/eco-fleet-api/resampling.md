---
title: Fuel data resampling
---

# Fuel data resampling

> Navixy Eco Fleet Lab has developed a solution for complex fuel data analysis. This method regenerates data sets which will be beneficial for a variety of exercises, enabling researchers, developers, and diagnostics experts to use the **processed data**.

**Diagnostic process** is an important procedure undertaken by numerous partners and investigators. Its primary purpose is to identify the underlying causes, which is essential for efficient fuel management by identifying any abnormalities. For example, they analyze key events - drains and refueling for fraud or look for a reason for the appearance of noise in the fuel data.

When partners or integrators incorporate fuel-related data into third-party systems for **further analysis and processing, including machine learning**, it can be highly effective in identifying behavioral patterns and detecting exceptions. This allows for more efficient investigations and enhances overall data processing capabilities.

Effective data management relies on **accurate and synchronized raw data**. However, inconsistencies in different data sets (i.e. position, speed, and fuel level) across various timeframes can pose challenges.

To tackle the challenges posed by incomplete or inconsistent data, we utilize advanced statistical models. With our API requests, you can easily **access and download processed datasets** from our platform for specific time periods. This API request specifically provides fuel-related data in a convenient CSV format.

> **Data resampling** refers to the joint process of creating a uniform data structure by organizing existing values and generating new ones (for missing values) in a chronological order, while considering equal time intervals. This approach ensures data integrity and facilitates analysis.

## data_resampling
### Description

The API response is presented in a convenient CSV format, incorporating columns below:

* Time - timestamp (depending on the timezone the tracker is located)
* [FUEL_SENSOR_NAME] - Fuel level (The column name is derived based on the sensor name. There could be more than 1 column)
* SPEED - object speed (km/h)
* MOVEMENT - movement status (0 - parking, 1 - moving, 2 - Idle)
* LNG - Longitude
* LAT - Latitude

**API path:** `/trackers/$tracker_id/resampling`, on the `/eco_fleet/v1` base path.

Additional list of resampling parameters

| name              | description                                                                                              | type   | format      |
|:------------------|:---------------------------------------------------------------------------------------------------------|:-------|-------------|
| data_type         | Data type. Options: FUEL, SPEED,MOVEMENT, LNG, LAT                                                       | string | "SPEED"     |
| resampling_method | Resampling method.  Options: FOLLOWING, PREVIOUS, MEDIAN_IN_WINDOW, AVERAGE_IN_WINDOW, AVERAGE.          | string | "FOLLOWING" |
| delta             | Delta time in seconds. Has different meaning for different algorithms.                                   | int    | null/1      |
| fixed_value       | A fixed value that will be used if no data is found in the interval. You can specify null or any number. | float  | nulll/5.5   |

##### Description of resampling_method

* FOLLOWING - In this series, the subsequent value is utilized to substitute any missing values. Consequently, neighboring missing values are all substituted with the subsequent valid value. If there are any missing values at the end of the series, they are replaced with the preceding valid value. If delta is not null than algorithm changes: if the time interval [T-Δ, T+0] contains at least one value, otherwise fixed_value.
* PREVIOUS - The previous value in the series is utilized to substitute missing values. Consequently, the neighboring missing values are replaced with the earliest preceding valid value. If delta is not null than algorithm changes: if the time interval [T-0, T+Δ] contains at least one value, otherwise fixed_value.
* MEDIAN_IN_WINDOW - In this algorithm, the presence of delta is imperative. The average of all the neighboring values in the series within the interval [T-Δ, T+Δ], fixed_value if no values.
* AVERAGE_IN_WINDOW- In this algorithm, the presence of delta is imperative. The median of all the neighboring values in the series within the interval [T-Δ, T+Δ], fixed_value if no values.
* AVERAGE - To replace missing values in a series, we use the average of the two neighboring values. For any missing values between valid ones, we replace them with the average of the surrounding valid values. If the series begins or ends with missing values, we substitute them with the next or previous valid value accordingly. If delta is not equal to null than algorithm changes: if the interval [T-Δ, T+Δ] contains at least one value, otherwise fixed_value.

> We recommend utilizing distinct methods for varying data types, as outlined in the table below. However, the choice of which methods to employ ultimately depends on your individual needs and expectations.

| Method     | Data type            | Use case                                                                               |
|:-----------|:---------------------|:---------------------------------------------------------------------------------------|
| Following  | Ordered              | Data that is missing at the end of a time series or sequence, i.e Fuel, Movement       |
| Previous   | Ordered              | Data that is missing at the beginning of a time series or sequence, i.e Fuel, Movement |
| Median     | Evenly distributed   | Data that is not normally distributed, i.e. Fuel, Speed                                |
| Average    | Normally distributed | Data that is not evenly distributed, i.e. Fuel, Speed                                  |

Note that the response is CSV, not JSON, and that errors follow RFC 7807 problem details rather than the numeric `status.code` the Platform API uses.

{% openapi-operation spec="eco-fleet" path="/trackers/{tracker_id}/resampling" method="post" %}
[OpenAPI eco-fleet](../../reference/Eco_Fleet.json)
{% endopenapi-operation %}

#### Errors

This API reports errors as RFC 7807 problem-detail strings, so the [general error codes](../../../general/errors.md#error-codes) do not apply to it:

* `errors/entity/not-found` - if there is no tracker with such ID belonging to the authorized user.
* `errors/external-api/navixy` - error accessing the Navixy Platform API. The underlying numeric code and message arrive in the `detail` field, so this is where Platform API errors such as **204 Entity not found** and **208 Device blocked** surface, rather than as top-level codes of this API.
* `errors/default/unauthorized` - no credential supplied.
