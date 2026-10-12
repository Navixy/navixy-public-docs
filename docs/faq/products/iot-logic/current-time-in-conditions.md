---
title: Current time in IF/THEN Logic conditions
description: "An IF/THEN Logic condition reads the message's own timestamps through event_time."
tags:
  - reference
---

# Current time in IF/THEN Logic conditions

## Question

Can an IF/THEN Logic condition use the current time?

## Answer

No. A condition reads the time of the message itself, through `event_time`, and not the current time.

See [Names that are always available](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/expressions#names-that-are-always-available) and [IF/THEN Logic](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/logic).
