---
description: Get event clips and memory card recordings from Jimi and Howen cameras through the Navixy API, with the video_monitoring right. Each upload uses the camera's mobile data
---

# API to retrieve videos from the platform

## Question

Is there an API to retrieve videos from the platform?

## Answer

Yes. The Navixy API returns video from Jimi JC400 and JC261 dashcams and most Howen video recorders. The caller needs the `video_monitoring` right.

* **Event clips:** the clips a camera uploads by itself when an event happens. They come with the event history.
* **Memory card recordings:** the API asks the camera to upload the minutes you request, and then returns a download link.

For the steps, see [Get dashcam video](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/guides/data-retrieval/dashcam-video). For every operation, see [Video](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/resources/tracking/tracker/multimedia).

Every upload uses the camera's mobile data, and a recording can be uploaded only while it is still on the camera's memory card. Request the minutes you need rather than whole days.

If you need a recording that the API can't reach, contact Support.
