---
title: 'Coban devices: SMS and GPRS command differences'
description: >-
  Why a Coban output command works over SMS but not from the server: the two
  channels use different command formats, and what to send over GPRS instead.
tags:
  - troubleshooting
---

# Coban devices: SMS and GPRS command differences

## Question

Why the Coban output works by SMS but not when sent from the server?

## Answer

The Coban 403 uses two different command formats depending on the channel:

| Channel                | Command to activate (immobilize / output ON) | Command to deactivate (resume / output OFF) |
| ---------------------- | -------------------------------------------- | ------------------------------------------- |
| SMS                    | `stop123456` _(password may differ)_         | `resume123456`                              |
| GPRS (server → device) | `**,imei:<IMEI>,109`                         | `**,imei:<IMEI>,110`                        |

`stop123456` is an SMS-only command (user manual, Remote immobilizing). Over GPRS, the device expects the protocol keyword `109` / `110`, not the SMS text.

So if the server sends `stop123456` (or the same SMS string) over GPRS, the device will not execute it. That is expected behavior, not a wiring or output failure. SMS working already shows the relay/output is fine.

What to do instead

1. From the platform/server, send the GPRS frame: `**,imei:<IMEI>,109` (and `110` to turn it off).
2. Or keep using SMS: `stop` / `resume` + device password.

Also check if GPRS still fails after using 109

* Device must be online in GPRS mode when the command is sent.
* With default delay mode, cut-off may wait until speed is < 20 km/h (device may reply `509`).
* Confirm the server is really sending `109`/`110`, not the SMS string.

## Links

[Coban Models](https://www.navixy.com/devices/coban/)

