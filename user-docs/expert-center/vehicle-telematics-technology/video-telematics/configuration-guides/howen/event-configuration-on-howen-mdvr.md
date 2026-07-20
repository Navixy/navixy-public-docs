---
description: >-
  Configure ADAS, DMS, and input-triggered event alerts on Howen MDVR devices
  using state field value rules with a full event code table.
---

# Event configuration on Howen MDVR

Howen devices can provide various types of events to the Navixy platform, including ADAS (Advanced Driver Assistance Systems), DMS (Driver Monitoring System), and other alerts. While these events may not include video footage, you can configure custom notifications using the [state field values alert type](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/events-and-notifications/inputs-and-outputs/state-field-value). This feature is particularly useful when you need alerts for events where video recording isn't necessary. For example, count how many particular events happen using the [report on all events](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/reports/specific-report-details/report-on-all-events).

## Event code types

Howen devices use two distinct state fields for different types of events:

1. **sub\_event\_code**

* ADAS alerts
* DMS alerts
* Input triggering events

2. **event\_code**

* Other device alerts and notifications

## How to set up event code alerts

1. Navigate to the alert configuration section
2. Select **State field value** as the alert type
3. In the settings:

* Choose the appropriate state field (`sub_event_code` or `event_code`)
* Enter the corresponding code as the expected value
* Configure additional notification preferences as needed

## Example use case: Lane departure warning

Let's walk through setting up an alert for lane departure warnings:

1. Create a new alert
2. Select **State field value** as the alert type\
   ![Live example: State field value alert rule type](<../../../../.gitbook/assets/image-20240417-091335 (1).png>)
3. In the settings tab:\
   ![Live example: State field and expected value settings](<../../../../.gitbook/assets/image-20240417-091116 (1).png>)

* State Field: `sub_event_code`
* Expected Value: `30_2` (lane departure warning code)

When the device sends a packet with sub\_event\_code 30\_2, the platform triggers the alert and notifies you or your driver.

## Event code tables

The following tables provide lists of supported event codes on Howen devices. All event codes align closely with original Howen documentation.

### Sub event codes

<details>

<summary>Input triggering codes</summary>

| Code  | Event                        |
| ----- | ---------------------------- |
| 4\_0  | Close door                   |
| 4\_1  | Emergency/Panic              |
| 4\_2  | F-door opening               |
| 4\_3  | M- door opening              |
| 4\_4  | B-door opening               |
| 4\_5  | Low beam                     |
| 4\_6  | High beam                    |
| 4\_9  | Right turn                   |
| 4\_10 | Left turn                    |
| 4\_11 | Braking                      |
| 4\_12 | Reverse                      |
| 4\_13 | Reservered 1                 |
| 4\_14 | F-door close                 |
| 4\_15 | M-Door Close                 |
| 4\_16 | B-door close                 |
| 4\_17 | Talking (start the intercom) |
| 4\_18 | Raise up                     |
| 4\_19 | Sealed                       |
| 4\_20 | Load                         |
| 4\_22 | Custom define                |

</details>

<details>

<summary>ADAS alarm codes</summary>

| Code   | Event                          |
| ------ | ------------------------------ |
| 30\_2  | Lane departure warning         |
| 30\_4  | Pedestrian collision alarm     |
| 30\_7  | FVS：Front vehicle start        |
| 30\_17 | FCW：Forward collision warning  |
| 30\_18 | HMW：Headway monitoring warning |

</details>

<details>

<summary>DMS alarm codes</summary>

| Code   | Event                             |
| ------ | --------------------------------- |
| 30\_33 | Fatigue driving alarm             |
| 30\_34 | Phone call alarm                  |
| 30\_35 | Smoking alarm                     |
| 30\_65 | Eyes closed                       |
| 30\_66 | Yawning                           |
| 30\_67 | Camera cover                      |
| 30\_68 | Distracted Driving                |
| 30\_69 | Seat belt not fastened            |
| 30\_70 | No driver                         |
| 30\_72 | Driver shift                      |
| 30\_73 | Driver back                       |
| 30\_80 | Infrared sunglasses               |
| 30\_81 | Driver ID identified successfully |
| 30\_82 | Driver ID identification failed   |

</details>

### Event codes

<details>

<summary>General device alert codes</summary>

<table><thead><tr><th width="86.63641357421875">Code</th><th>Event</th></tr></thead><tbody><tr><td>0</td><td>Unknown</td></tr><tr><td>1</td><td>Video lost</td></tr><tr><td>2</td><td>Motion detection</td></tr><tr><td>3</td><td>Video blind</td></tr><tr><td>4</td><td>Input trigger</td></tr><tr><td>5</td><td>Emergency alarm</td></tr><tr><td>6</td><td>Low speed alarm</td></tr><tr><td>7</td><td>Over speed alarm</td></tr><tr><td>8</td><td>Low temperature alarm</td></tr><tr><td>9</td><td>High temperature alarm</td></tr><tr><td>10</td><td>Humidity alarm</td></tr><tr><td>11</td><td>Parking over time</td></tr><tr><td>12</td><td>Acceleration alarm</td></tr><tr><td>13</td><td>GEO fencing</td></tr><tr><td>14</td><td>Electronic route</td></tr><tr><td>15</td><td>Abnormal open/close the door</td></tr><tr><td>16</td><td>Storage abnormal</td></tr><tr><td>17</td><td>fatigue driving</td></tr><tr><td>18</td><td>Fuel consumption abnormal</td></tr><tr><td>19</td><td>ACC off. (compatible with old firwmares. In old firmwares: During ACC-off delay, if ignites (et > st), will report “accoff ends”; in new firmware: During ACC-off delay, if ignites (et > st), will report “accon”)</td></tr><tr><td>20</td><td>GPS module abnormal</td></tr><tr><td>21</td><td>Front panel open</td></tr><tr><td>22</td><td>Swipe card</td></tr><tr><td>23</td><td>IButton</td></tr><tr><td>24</td><td>Harsh acceleration</td></tr><tr><td>25</td><td>Harsh braking</td></tr><tr><td>26</td><td>Low speed warning</td></tr><tr><td>27</td><td>High speed warning</td></tr><tr><td>28</td><td>Voltage alarm</td></tr><tr><td>29</td><td>People counting</td></tr><tr><td>30</td><td>DMS and ADAS alarm ((Driver monitoring system, and Advanced Drivign Assistant System)</td></tr><tr><td>31</td><td>"Acc on". Report once at boot</td></tr><tr><td>32</td><td>Idle</td></tr><tr><td>33</td><td>Gps antenna break</td></tr><tr><td>34</td><td>Gps antenna short</td></tr><tr><td>35</td><td>IO ouput</td></tr><tr><td>36</td><td>CAN bus connection abnormal</td></tr><tr><td>37</td><td>Towing</td></tr><tr><td>38</td><td>Free wheeling</td></tr><tr><td>39</td><td>RPM exceeds</td></tr><tr><td>40</td><td>Vehicle moves</td></tr><tr><td>41</td><td>Trip start (st/et/dtu time same）</td></tr><tr><td>42</td><td>In trip</td></tr><tr><td>43</td><td>Trip ends (periodical report after acc off)</td></tr><tr><td>44</td><td>GPS location recover</td></tr><tr><td>768</td><td>Trip notification</td></tr><tr><td>769</td><td>Upgrade notification</td></tr></tbody></table>

</details>

### Live example

For example, to get lane departure warning alerts, choose alert type **State field value**:

And in the settings tab use sub\_event\_code as the state field and 30\_2 as an expected value:

Once the device provides a packet with sub event code 30\_2, the platform triggers an alert and notifies you or your driver.
