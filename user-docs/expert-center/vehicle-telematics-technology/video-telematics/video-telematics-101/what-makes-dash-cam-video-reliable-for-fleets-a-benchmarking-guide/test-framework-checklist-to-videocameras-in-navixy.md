---
description: Dashcam testing checklist for Navixy covering video quality, GPS accuracy, streaming stability, compression, and AI recognition rates.
---

# Test framework checklist for videocameras in Navixy

| Video quality             | Resolution                      | Pixels (e.g., 1920×1080) | ≥ 640x360 minimum to record an event                |
| ------------------------- | ------------------------------- | ------------------------ | --------------------------------------------------- |
| <p><br></p>               | Video sensibility               | High / Medium / Low      | Reducing or increasing the frequency of events.     |
| Frame Rate                | Video transition                | FPS                      | ≥ 10 FPS                                            |
| Dynamic Range             | Low-light clarity transition    | Seconds                  | At least 1 sec.                                     |
| GPS Performance           | Location accuracy               | Meters                   | ≤ 15 m (open sky)                                   |
| <p><br></p>               | Streaming stability             | Seconds                  | ≤30 seconds                                         |
| Network & Streaming       | Data usage at 720p              | MB/hour                  | ≤ 300 MB/h                                          |
| Compression Quality       | Protocol Data                   | H264 or H265             | At least H264                                       |
| Video Quality Recognition | Plate recognition               | Meters                   | Readability at a range of 0-30–80 km/h.             |
| <p><br></p>               | Face recognition/Id recognition | Meters                   | Alerts for ID recognition or driver not recognized. |
| <p><br></p>               | Stress test                     | Frequency                | Wrong events/minute                                |
| Range of temperature      | Operating temp                  | °C                       | –20°C to +60°C                                      |
| Maintenance               | OTA firmware                    | Pass/Fail                | Pass                                                |
