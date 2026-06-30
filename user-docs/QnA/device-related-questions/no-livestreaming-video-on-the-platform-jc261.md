---
title: No livestreaming video on the platform (JC261)
description: "Verify RSERVICE is set to rtmp.x-gpsmail.com:1935/encoder and SIM has active data plan with stable connectivity. Check signal strength"
---

# No livestreaming video on the platform (JC261)

## Question

Why is my JC261 camera not livestreaming?

## Answer

There are two main possible root causes for the lack of live streaming on the platform:

1.  **RSERVICE configuration**

    Make sure the camera is configured with the correct RSERVICE command for live streaming:

    `RSERVICE,rtmp.x-gpsmail.com:1935/encoder`

    If the server isn't set correctly, the customer can't view the live stream.
2.  **SIM / data plan / connectivity**

    Even when it looks like a platform issue, poor signal strength or insufficient SIM card data plan can prevent the camera from uploading the stream with the required quality.

    Check the SIM plan and verify that the connection is stable and working properly.
