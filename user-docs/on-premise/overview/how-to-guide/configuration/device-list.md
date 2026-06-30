---
description: Filter GPS device manufacturers and models visible in Navixy on-premise. Configure show or hide rules in the database to control device visibility per vendor.
---

# Device list

With Navixy, you have the flexibility to customize the platform to display only the devices you need, ensuring an optimized user experience. The hidden devices cannot be activated by users. This page provides detailed instructions on how to hide specific device manufacturers and tracking devices on the Navixy platform.

## Hide specific device manufacturers

To hide certain vendors from your platform, you must use the column “**tracker\_vendor\_filter**” in the **google.paas\_settigns** table of the database.

1. Look in the "google.models" table and identify the vendor names in the "vendor" column that you wish to filter.
2. Edit the filter using the following syntax:
  - To show only specified trackers, list the vendor names separated by a comma.  
Example: `Teltonika, Queclink, Meitrack`
  - To show all vendors except the ones specified, list the vendor names separated by a comma with the minus symbol in the beginning.  
Example: `-Teltonika, Queclink, Meitrack`
  - Use the "`*`" symbol to show all trackers.

## Hide specific devices

To hide specific trackers from the Navixy platform, you need to use the "tracker\_model\_filter" column in the "google.paas\_settings" table of the database. The filter works by following these steps:

1. Look in the "google.models" table and note the model names in the "model" column that you wish to filter.
2. Edit the filter using the following syntax:
  - To show only specified trackers, list the model names separated by a comma.  
Example: `telfm1010, qlgv65, meitrackt1`
  - To show all trackers except the ones specified, list the model names separated by a comma with the minus symbol in the beginning.  
Example: `-telfm1010, qlgv65, meitrackt1`
  - Use the "`*`" symbol to show all trackers.