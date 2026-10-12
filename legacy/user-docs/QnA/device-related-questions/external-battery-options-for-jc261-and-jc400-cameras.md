# External Battery Options for JC261 and JC400 Cameras

### Question

Is there any external battery for JC261 or JC400 cameras?

### &#x20;Answer

Yes, but only for the JC400 series.\
The JC261 doesn't have an external battery and has no battery connector. It depends entirely on vehicle power.

#### JC400 — external backup battery (supported)

The JC400 (EdgeCam 2 / JC400 series) includes a dedicated External battery interface. Jimi lists an External Battery × 1 as a standard accessory.

| **Specification**  | **Official value**                                                     |
| ------------------ | ---------------------------------------------------------------------- |
| Battery type       | External backup pack (connects to the device battery port)             |
| Capacity           | 450 mAh (not 5 mA)                                                     |
| Main vehicle power | DC 9–30 V via B+ / ACC / GND                                           |
| Purpose            | Keep the unit powered for a short time after the vehicle supply is cut |

<br>

How to connect it: plug the original Jimi pack into the Battery / External battery port on the JC400 and place it in a safe position (typically behind the unit / under the dash). Use only the manufacturer battery; non-original packs are not supported.

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

**Runtime (important)**

Jimi does not publish a runtime in hours. This pack is not meant to keep livestreaming or parking surveillance running for hours.

A 450 mAh cell is a short emergency backup:

* Energy is roughly 450 mAh × 3.7 V ≈ 1.7 Wh.
* A 4G dual-channel Android dashcam typically draws several watts when recording / online.
* In practice that is usually minutes of operation (often on the order of \~15–30 minutes under load), enough to finish a recording, send a last position / power-cut event, and shut down cleanly — not multi-hour online time.

If the camera goes offline after ignition-off or a power cut, that is expected once the 450 mAh pack is depleted.

#### JC261 — no external battery

Official JC261 specs and the series user manual do not list any battery (internal or external) and do not include a battery port.

| **Specification** | **Official value**                                  |
| ----------------- | --------------------------------------------------- |
| Interfaces        | Micro USB × 1, SOS × 1, TTL × 1, Relay × 1          |
| Power             | B+ / ACC / GND only                                 |
| Voltage           | DC 9–30 V                                           |
| Battery           | Not listed                                          |
| Accessories       | Power cables, cameras, mounts — no external battery |

If vehicle power is disconnected, the JC261 goes offline. There is no Jimi backup pack to keep it connected.

Jimi markets the JC261 as an upgrade of the JC400, but the external 450 mAh battery interface was not carried over.

\
Links:

[JC400 Configuration](https://navixy.com/docs/expert-center/vehicle-telematics-technology/video-telematics/configuration-guides/jimi-iot/jimi-jc400-troubleshooting)

