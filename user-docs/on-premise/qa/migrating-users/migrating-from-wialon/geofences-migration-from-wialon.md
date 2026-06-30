---
description: Export geofences from Wialon as KML or KMZ files and import them into Navixy. Covers polygon and circle geofence handling with custom radius configuration.
---

# Geofences migration from Wialon

Geofences can be migrated seamlessly from Wialon to Navixy, allowing your users to continue using them without disruption. To transfer geofences to your users, follow the instruction below.

1. Open user account in Wialon and click on the username in the upper right corner:

![](../../../.gitbook/assets/image-20230810-122626.png)

2. Select Export to KML/KMZ

![](../../../.gitbook/assets/image-20230810-122657.png)

3. Select all required geofences to transfer and press OK.

![](../../../.gitbook/assets/image-20230810-122805.png)

4. Open a user account in Navixy admin panel.

![](../../../.gitbook/assets/image-20230810-122828.png)

5. And activate geofences section here.

![](../../../.gitbook/assets/image-20230810-122852.png)

After pointing the cursor over the geofence import, two import options will be displayed. Choose import geofences from a KML file:

![](../../../.gitbook/assets/image-20230810-122933.png)

6. Add the exported file and specify the default radius for circle geofences.

For polygon geofences, the import will be done exactly as in the Wialon system. For circle geofences, the centers with radiuses specified in the previous step will be transferred. After that, you might need to change the radius of geofences manually. Place the cursor on the required geofence and press the pencil button that appears.

![](../../../.gitbook/assets/image-20230810-123001.png)

After transferring geofences, they can be used for rules and analytics on the Navixy platform.
