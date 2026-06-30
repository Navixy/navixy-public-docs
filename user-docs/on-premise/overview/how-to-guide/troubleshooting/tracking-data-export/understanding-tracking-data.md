---
description: Interpret exported Navixy tracking data columns including status codes, event IDs, and binary input/output values from the on-premise database.
---

# Understanding tracking data

The `tracking` database stores the data in a fairly technical way necessary for the Navixy platform to work. This page will show you how to read the values stored in this database to understand what they mean. This can be useful if you have exported tracking data from the database for some further analysis.

Here we will describe the data contained in each column.

### ID

Unique point identifier.

### Track ID

The value required to identify valid tracks, parkings, and points not involved in tracking:

* Positive value - track point. Repeated value defines points belonging to the same track.
* 0 - parking.
* Negative value - a point that does not participate in the track formation:
  * -1 = invalid point (typically zero coordinates)
  * -2 = invalid time
  * -3 = surrogate point created by the server to save the event recorded for the device.
  * -4 = redundant point that does not change the track geometry.

### Server time

Labeled as `actual_time` in the database. The time when the point was received and saved on the server.

### Tracker time

Labeled as `get_time` in the database. The time at which the point was recorded by device. This timestamp may be significantly different from the server time, for example, when data is buffered on the device due to lack of connectivity.

### Longitude, Latitude, Speed, Altitude

The names of the columns speak for themselves. Point coordinates, movement speed and altitude reported by device.

### Satellites

Number of satellites by which GPS coordinates are determined. In the absence of satellites, points are generally recognized as invalid.

### Status

Tecnical value - the point status on the platform. For more information expand the collapsible panel below.

<details>

<summary>Statuses list</summary>

<table data-header-hidden><thead><tr><th width="40.272705078125"></th><th></th></tr></thead><tbody><tr><td><strong>0</strong></td><td>Track start</td></tr><tr><td><strong>1</strong></td><td>Track end</td></tr><tr><td><strong>2</strong></td><td>Regular track point</td></tr><tr><td><strong>3</strong></td><td>Parking</td></tr><tr><td><strong>4</strong></td><td>Invalid point</td></tr><tr><td><strong>5</strong></td><td>Single-point track. Common for devices operating in interval mode.</td></tr><tr><td><strong>6</strong></td><td>Surrogate point created by the server to save the event recorded for the device.</td></tr><tr><td><strong>7</strong></td><td>Track split - the start of the track after the break, if there were no points for 20 minutes and there was no parking (two points in a row with zero speed)</td></tr><tr><td><strong>8</strong></td><td>LBS single point</td></tr></tbody></table>

</details>

### Heading

Device movement direction. Specified as an angle clockwise from north.

### Event ID

Tecnical value - the indication of event recorded for device. For more information expand the collapsible panel below.

<details>

<summary>Events list</summary>

| **2**   | Track. No specific event, just a track point         |
| ------- | ---------------------------------------------------- |
| **4**   | Emergency contact number called                      |
| **5**   | Unauthorized movement event determined by the device |
| **11**  | Input 1 state change                                 |
| **12**  | Input 2 state change                                 |
| **13**  | Input 3 state change                                 |
| **14**  | Input 4 state change                                 |
| **15**  | Input 5 state change                                 |
| **16**  | Input 6 state change                                 |
| **17**  | Input 7 state change                                 |
| **18**  | Input 8 state change                                 |
| **34**  | Device wakes up from a sleep mode                    |
| **37**  | Sleep mode start                                     |
| **40**  | Main power low                                       |
| **41**  | Power lost or external power cut                     |
| **42**  | Power On button pressed                              |
| **43**  | Power recovered or external power connected          |
| **44**  | OBD Unplug from the car’s connector                  |
| **45**  | OBD Plug in                                          |
| **46**  | Backup device’s battery low                          |
| **50**  | Idle end (hardware related)                          |
| **51**  | Idle start (hardware-related)                        |
| **71**  | Idle sleep start                                     |
| **72**  | Low backup battery sleep start                       |
| **73**  | Timer wakeup                                         |
| **74**  | Motion wakeup                                        |
| **75**  | External power wakeup                                |
| **76**  | Timer sleep alert                                    |
| **81**  | Security mode on                                     |
| **82**  | User event                                           |
| **83**  | SOS button pressed event                             |
| **84**  | Security mode off                                    |
| **90**  | Antenna disconnection                                |
| **100** | Device detached from the object                      |
| **111** | Output 1 state change                                |
| **112** | Output 2 state change                                |
| **113** | Output 3 state change                                |
| **114** | Output 4 state change                                |
| **115** | Output 5 state change                                |
| **116** | Output 6 state change                                |
| **117** | Output 7 state change                                |
| **118** | Output 8 state change                                |
| **797** | Check-in sent from the mobile app                    |
| **798** | Task form submission                                 |
| **799** | Working status changing                              |
| **800** | GSM LBS point determined by a device                 |
| **802** | Track point by time                                  |
| **803** | Track point by distance                              |
| **804** | Track point by angle                                 |
| **811** | Track movement start                                 |
| **812** | Track movement end                                   |
| **813** | Unauthorized movement end                            |
| **814** | Non-track message                                    |
| **900** | Harsh driving quick lane change                      |
| **901** | GPS jamming                                          |
| **928** | Unplug from the tracked object                       |
| **929** | Frequent lane change                                 |
| **930** | Device can't detect human face                       |
| **931** | Seat belt unbuckled                                  |
| **932** | Drinking                                             |
| **933** | Eyes closed                                          |
| **934** | Attach device to the tracked object                  |
| **935** | MDSM 7 disconnected                                  |
| **936** | MDSM 7 connected                                     |
| **937** | Report new driver                                    |
| **938** | Driver enters cabin                                  |
| **939** | Start driver absence                                 |
| **940** | Driver stopped smoking (Driver distraction)          |
| **941** | Power off button pressed                             |
| **942** | Driver started smoking (Driver distraction)          |
| **943** | Driver finished using the phone (Driver distraction) |
| **944** | Driver started using the phone (Driver distraction)  |
| **945** | Yawning (Fatigue driving)                            |
| **946** | Driver stopped distraction (Driver distraction)      |
| **947** | Driver started distraction (Driver distraction)      |
| **948** | Driver stop drowsiness (Fatigue driving)             |
| **949** | Driver start drowsiness (Fatigue driving)            |
| **950** | Over speeding by hardware event                      |
| **951** | Cruise control switched on                           |
| **952** | Cruise control switched off                          |
| **953** | Unexpected movement start                            |
| **954** | Unexpected movement end                              |
| **955** | Car alarm                                            |
| **956** | Peds in danger zone (ADAS)                           |
| **957** | Traffic sign recognition (ADAS)                      |
| **958** | Peds collision warning (ADAS)                        |
| **959** | Check engine light                                   |
| **960** | Fatigue driving                                      |
| **961** | Headway warning (ADAS)                               |
| **962** | Right lane departure (ADAS)                          |
| **963** | Left lane departure (ADAS)                           |
| **964** | Lane departure (ADAS)                                |
| **965** | Forward collision warning (ADAS)                     |
| **966** | Tracker entered auto geofence                        |
| **967** | Tracker exited auto geofence                         |
| **968** | Force location response by SMS from UI               |
| **969** | Door alarm                                           |
| **970** | Ignition Off                                         |
| **971** | Ignition On                                          |
| **972** | Driver not identified                                |
| **973** | Driver identified                                    |
| **974** | Lock closed                                          |
| **975** | Lock opened                                          |
| **976** | Device power Off                                     |
| **977** | Device power On                                      |
| **978** | Case closed                                          |
| **979** | Case opened                                          |
| **980** | Call button pressed                                  |
| **981** | Light sensor determined dark                         |
| **982** | Light Sensor determined bright                       |
| **983** | Vibration end                                        |
| **984** | Vibration start                                      |
| **985** | Strap bolt Inserted                                  |
| **986** | Strap bolt cut                                       |
| **987** | Harsh driving acceleration and turn                  |
| **988** | Harsh driving braking and turn                       |
| **989** | Harsh driving turn                                   |
| **990** | Harsh driving acceleration                           |
| **991** | Harsh driving braking                                |
| **992** | GPS signal recovered                                 |
| **993** | GPS signal lost                                      |
| **994** | Crash alarm                                          |
| **995** | GSM signal damp alarm                                |
| **996** | Harsh driving                                        |
| **997** | Bracelet open                                        |
| **998** | Bracelet close                                       |
| **999** | G sensor alert                                       |

</details>

### Duration

Duration of the event.

### Mileage

Hardware mileage reported by device. May be different from the mileage calculated by the platform depending on the device settings.

### Inputs and Outputs

Input and output states (on/off). Depend on the number of supported inputs and outputs for the device.

{% hint style="info" %}
The value must be converted to binary form and read from right to left. For example, the value of `9` for inputs equals `1001` in binary, and it means the inputs #1 and #4 are on, inputs #2 and #4 are off.
{% endhint %}

### Address

The point address resolved by geocoder according to the coordinates.
