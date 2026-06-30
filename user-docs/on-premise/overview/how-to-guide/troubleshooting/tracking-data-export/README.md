---
description: Export raw tracking data from the Navixy on-premise database. Covers both bucket-structure and file-by-file database organization with SQL query examples.
---

# Tracking data export

As the tracking devices operate, their data is sent to and stored in the Navixy platform database, so you can view the trips in monitoring and build trip reports. However, there may be situations where you need to upload all of the recorded tracking information as geolocation points. This is usually necessary to analyze the data in controversial situations. In this case, you can request such information directly from the database.

{% hint style="info" %}
This method allows you to get data in a more detailed and "technical" way than you see in monitoring. But it is important to realize that to appear in the database, the data must be actually transmitted by a tracking device, and it will not be possible to restore trips that are completely missing in the monitoring in this way.
{% endhint %}

You will need **direct access to MySQL database** to pull the tracking data, which means your clients do not have rights to this operation, and only technicians with access to the server have the necessary authorization.

Tracking data is stored in the _**tracking**_ database. Depending on when and how your platform instance was deployed, two fundamentally different ways of organizing the database structure are possible.

* **Bucket structure**. In this case tracking data is partitioned in several buckets (16 by default).
* **File-by-file structure**, where each tracker corresponds to a separate table named by its IMEI.

The type of data organization you are using is easy to check by simply opening the storage directory of the database files - for example, on Linux this is `/var/lib/mysql/tracking` by default. Inside, you will find either partitioned files named like `bucket_****.ibd`, or multiple `ibd` and `frm` files named after the devices IMEI.

{% hint style="danger" %}
There is no conventional way to change the database from file-by-file to buckets or vice versa, as this would involve rebuilding the entire database. Once deployed, the database continues to exist in its initial structure.
{% endhint %}

## Bucket structure

This is a contemporary type of database organization where for performance optimization the data is stored not in individual tables for each tracker, but in the so-called buckets. By default there are 16 of them, trackers are placed into them randomly. Each tracker has its own `storage_id`, according to which we can find the required bucket.

To request the data, you need the tracker's IMEI (in this example, we use the fake IMEI 987654321012345), and the internal device identifier named `source_id`.

Find out `source_id` and `storage_id` using this query:

```
SELECT storage_id, source_id FROM google.sources WHERE source_imei='987654321012345'; 
```

The resulting `storage_id` starts with the bucket number - from 1 to 16. For example, _storage\_id=**2**01000_ means _bucket\_**2**_ and _storage\_id=**13**01000_ means _bucket\_**13**_.

In the next query we need both the bucket number and source\_id. With this query we request tracking data for the required period and upload it to a CSV file.

Please note:

* we are using the `source_id` and `bucket` number found with the previous query.
* `get_time` is the time of data recording by the device, it is specified in UTC+0, regardless of the time zone of the user account.
* MySQL must have permissions to write to the folder specified in the query.

The SQL query itself is as follows:

```
SELECT 'Id','TrackID','Server time','Tracker time','Longitude','Latitude','Speed','Altitude','Satellites','Status','Heading','Event ID','Duration','Mileage','Inputs','Outputs','Address'   
UNION ALL   
SELECT id, track_id, actual_time, get_time, lng, lat, speed, alt, satellites, status, heading, event_id, duration, mileage, input_status, output_status, address  
FROM tracking.bucket_2 where source_id=12345 and get_time between '2024-07-20 20:11:00' and '2024-07-20 20:13:00'   
INTO OUTFILE "/var/lib/mysql-files/987654321012345.csv"
FIELDS TERMINATED BY ','   
ENCLOSED BY '"' LINES   
TERMINATED BY '\n';
```

For Windows the path is like:

```
INTO OUTFILE "C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/987654321012345.csv"
```

## File-by-file structure

This is an older way of organizing the database structure, which however remains relevant for many Navixy instances with a multiple year history.

To request the data in this case, you only need the tracker's IMEI (in this example, we use the fake IMEI 987654321012345).

Please note:

* `get_time` is the time of data recording by the device, it is specified in UTC+0, regardless of the time zone of the user account.
* MySQL must have permissions to write to the folder specified in the query.

The SQL query is as follows:

```
SELECT 'Id','TrackID','Server time','Tracker time','Longitude','Latitude','Speed','Altitude','Satellites','Status','Heading','Event ID','Duration','Mileage','Inputs','Outputs','Address'  
UNION ALL  
SELECT id, track_id, actual_time, get_time, point_y, point_x, speed, altitude, satellites, status, heading, event_id, duration, mileage, input_status, output_status, address 
FROM tracking.987654321012345 where get_time between '2024-07-20 20:11:00' and '2024-07-20 20:13:00'  
INTO OUTFILE "/var/lib/mysql-files/987654321012345.csv"  
FIELDS TERMINATED BY ','  
ENCLOSED BY '"' LINES  
TERMINATED BY '\n';
```

For Windows the path is like:

```
INTO OUTFILE "C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/987654321012345.csv"
```

## Data output

The above SQL query will generate a CSV file containing all tracking points recorded for the device.

This file can be imported into Excel or used in any internal development.
