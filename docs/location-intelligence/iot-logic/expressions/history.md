---
description: >-
  How to read what a device reported before this message, how far back IoT
  Logic keeps values, and the difference between the two ways of counting.
tags:
  - fundamentals
  - beta
---

# Earlier values

A formula can read values from messages that came before the current message. With an earlier value, a formula can calculate a change: not that fuel is at 40 liters, but that fuel fell 40 liters since the last message.

## How to read an earlier value

Add an index to `value()`:

```
value('fuel_level', 1)
```

| Index | What you get |
| --- | --- |
| `0` | The value in the message being processed now |
| `1` | The value from one message back |
| `2` | The value from two messages back |
| … | … |
| `11` | The oldest value kept |

IoT Logic keeps the last 12 values of every attribute. Because the count starts at 0, index `11` gives the oldest value that you can ask for. A larger index is an error, and the flow doesn't save.

`genTime` and `srvTime` take an index the same way, so you can find out when an earlier value was recorded:

```
genTime('fuel_level', 1)
```

These two functions don't check the range of the index. A formula with an index above 11 saves without an error. The formula then returns nothing on every message, so the attribute stays empty and nothing explains why. Keep every index within 0 to 11.

## Index 0 is the current message

`value('speed', 0)` and the short form `speed` mean the same thing: the value in the message being processed right now.

{% hint style="warning" %}
**Index 0 doesn't fall back to an earlier value.** When this message doesn't contain the attribute, both forms return nothing, so the attribute isn't produced and the condition isn't true.

To get the last value that the device sent, ask for that value by index. Check this behavior first when a formula works for some messages and not for others.
{% endhint %}

## Two ways of counting: `valid` and `all`

The third argument chooses between two meanings of "the previous reading". Take a fuel sensor that reports on every tenth message. "The previous reading" can mean the previous time that the sensor reported, or the previous message from the device.

```
value('fuel_level', 1, 'valid')
```

```
value('fuel_level', 1, 'all')
```

**`valid` counts the attribute's own values.** Index 1 is the previous time that this attribute had a value, however many messages ago that time was. Use `valid` when you care about the reading and not about the message that it arrived in.

**`all` counts messages.** Index 1 is the previous message from this device, and you get the value that the attribute had in that message. If the attribute wasn't in that message, you get nothing. Use `all` when you compare the state of the device at two points in time.

`valid` and `all` differ only for an attribute that isn't in every message. A sensor that reports on every message gives the same answer either way.

| | `valid` | `all` |
| --- | --- | --- |
| Index 1 means | The previous value of this attribute | This attribute in the previous message |
| An attribute missing from a message | Skipped over | Counted, and returns nothing |
| Best for | A sensor that reports irregularly | Comparing two moments in time |

{% hint style="info" %}
`value('x', 1)` without a third argument counts messages, the same as `'all'`. Write the third argument whenever the difference matters, so that the formula states which way of counting it uses and doesn't depend on the default.
{% endhint %}

## Examples of formulas with earlier values

**How much fuel was used since the last reading:**

```
value('fuel_level', 1, 'valid') - fuel_level
```

**Whether the temperature has risen since the previous message:**

```
value('temperature', 0) > value('temperature', 1, 'all')
```

**How long ago the previous reading was recorded, in milliseconds:**

```
event_time - genTime('fuel_level', 1, 'valid')
```

## How IoT Logic stores earlier values

**History is per device.** Every device has its own 12 values of every attribute. A formula never reads the history of another device.

**Calculated attributes are stored too, per flow.** On the next message, a flow can read back a value that an **Initiate Attribute** node produced, the same as a device reading. A flow reads only the readings of the device and the attributes that the flow calculated itself, at any index. It never reads the attributes of another flow. To use a calculated value in a formula, calculate that value in the same flow.

**A new attribute has no history.** The first message after you add an attribute has no earlier value of that attribute. A formula that reads index 1 produces nothing until the device has reported twice.

**Out-of-order messages are dropped.** A message that is older than the last processed message from that device is discarded, so history never goes backwards. A device can store readings while it is offline and send them together later. Such a device loses each reading that arrives after a newer reading was already processed.
