# Event count inconsistency when selecting multiple devices

## Question

Why are all events displayed correctly when a single device is selected, but they change when multiple devices are selected?

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

## Answer

This behavior is expected and is related to the way the History Tracker API applies its event limit.

According to the public documentation:

> <mark style="color:$info;">limit – Default and maximum value is 1000 (for StandAlone this is configurable through</mark> <mark style="color:$info;"></mark><mark style="color:$info;">`maxHistoryLimit`</mark><mark style="color:$info;">).</mark>

The important detail is that the limit is applied to the entire request, not individually to each selected tracker. In other words, the server returns up to 1000 of the most recent events across all selected devices, rather than up to 1000 events per device.

#### Example

* **1 device selected**
  * The device has 59 events.
  * Since the total is well below the limit, all 59 events are returned.
* **22 devices selected**
  * The combined number of events is still below the limit.
  * The device continues showing 59 events.
* **All devices selected**
  * Some devices generate events much more frequently than others.
  * The server returns only the 1000 most recent events across the entire selection.
  * As a result, older events from less active devices may be excluded from the response.
  * In this scenario, the selected device displays only 32 events, even though it originally had 59.

The event pool is shared among all selected devices. The more devices included in the request—especially those generating a high volume of events—the greater the chance that older events from other devices will fall outside the 1000-event limit.

Currently, the frontend does not indicate when the returned event list has been truncated due to the global limit, which may lead users to believe that events are missing when they have simply been excluded by the API limit.



## Links

[API- Tracker events](https://navixy.com/docs/navixy-api/user-api/backend-api/resources/commons/history/history_tracker)

[Event History](https://navixy.com/docs/user/guide/tracking/history-view/event-history#event-details)
