---
description: Configure Yeastar TG Series GSM gateway hardware for Navixy on-premise SMS delivery. Suited for smaller instances with limited SMS load requirements.
---

# Yeastar

[Yeastar TG Series](https://www.yeastar.com/voip-gsm-gateway/) are hardware GSM gateways that may not be suitable for high-load services, but it can be a good option for smaller Navixy instances, such as those used by mid-sized businesses.

## Navixy JSON configuration for Yeastar

```json5
 provider: yeastar
 type: transceiver
 params:
 {
 "address":<address>,
 "port":<port>,
 "login":<login>,
 "password":<password>,
 "gsm_span":<GSM span identifier>,
 "connection_timeout":30000,
 "send_timeout":30000,
 "reconnect_timeout":30000,
 "ping_timeout":60000,
 "time_shift":0
 }
```
