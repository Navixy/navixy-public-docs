# Why Does a Black Screen Appear During Live Streaming on Jimi JC Cameras?

### Overview

It is common for monitoring centers to have multiple operators supervising the same fleet simultaneously. As a result, more than one user may attempt to open the live video stream from the same camera at the same time or within a short period.

In these situations, users may occasionally experience a black screen instead of the live video. Although this may appear to be a platform malfunction, it is an expected behavior caused by the way the Jimi JC series cameras manage live streaming sessions.

<img src="../../../.gitbook/assets/unknown (14).png" alt="" height="370.7054802958599" width="624">

### Is This an Integration Issue? No.

The JC261 and JC400 cameras use Navixy's native Jimi IoT integration. Live video is established directly between the platform and the camera using Jimi commands (SMS/RTMP), not through third-party video integrations.

Since these cameras communicate directly with Navixy, the black screen is not caused by the integration itself, but rather by the streaming architecture implemented by the device.

### Why Does the Black Screen Occur?

The behavior is the result of three different factors working together.

#### 1. Camera Limitation (Primary Cause)

The JC cameras are designed to maintain only one active RTMP streaming session at a time.

When a user starts Live View, the platform sends commands instructing the camera to begin streaming.

When the user closes the video, the platform sends another command instructing the camera to stop streaming.

| User Action       | Command Sent to Camera     |
| ----------------- | -------------------------- |
| Open Live Stream  | StartVideoStream + RTMP,ON |
| Close Live Stream | StopVideoStream + RTMP,OFF |

When several users repeatedly open and close the same camera within a short period, multiple RTMP ON/OFF commands are sent almost simultaneously.

This may leave the camera in an inconsistent streaming state where:

* The camera acknowledges the commands correctly.
* The RTMP session becomes unstable.
* The video stream is interrupted.
* Users receive a black screen instead of live video.

In previous investigations, Jimi confirmed through device logs that the camera correctly responds with RTMP:OK, but rapid consecutive streaming requests can still cause the stream to stop unexpectedly.

#### 2. Navixy Streaming Architecture

Unlike traditional video streaming platforms or CDNs (Content Deliverable/Distribution Network), Navixy does not receive a single stream and redistribute it to multiple viewers.

Instead, each Live View session interacts directly with the camera.

This means that every operator opening or closing the livestream may generate new commands to the device.

According to the platform documentation:

After you finish watching the live stream, close the video monitoring tab or browser tab. The platform will then stop receiving video from your device.

Therefore, the video session is directly linked to each user's interaction.

If multiple operators request the same livestream simultaneously, concurrent streaming commands may be issued to the camera.

#### 3. Cellular Network Limitations

Live video transmission also depends on:

* Cellular signal quality
* Available bandwidth
* Mobile network latency
* Data plan limitations

Even under normal conditions, live streaming requires a stable mobile connection.

When multiple streaming requests are issued simultaneously, the device must repeatedly establish and terminate RTMP sessions while also transmitting video over the cellular network, increasing the probability of interruptions or temporary black screens.

### Recommended Best Practices

To ensure the most stable live streaming experience, we recommend the following operational guidelines:

* Only one operator should view the livestream for a camera at any given time.
* Always close the current livestream session before opening it from another workstation.
* Wait approximately 10–15 seconds after closing a livestream before starting a new one.
* Avoid repeatedly clicking the Live View or Calendar buttons, as this may generate multiple consecutive RTMP commands.
* Ensure the camera has a stable cellular connection with sufficient bandwidth before initiating a livestream.
