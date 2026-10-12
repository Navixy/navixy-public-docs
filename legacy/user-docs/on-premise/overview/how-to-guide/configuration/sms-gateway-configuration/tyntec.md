---
description: Configure the Tyntec cloud communications gateway for Navixy on-premise SMS messaging. Add the JSON configuration parameters for messaging and voice integration.
---

# Tyntec

[Tyntec](https://www.tyntec.com/) is a cloud communications provider that offers a range of messaging and voice solutions, including SMS, voice, and chat APIs.

With Navixy's support for Tyntec as an SMS gateway provider, you can leverage Tyntec's messaging capabilities and global coverage to send and receive SMS messages.

## Navixy JSON configuration for Tyntec

To connect Navixy with Tyntec, you will need to obtain your Tyntec API credentials and configure the JSON settings for the Tyntec gateway in the **'sms\_gates'** table of the **'google'** database.

```json5
provider: tyntec 
type: transceiver 
params:
{
  url: <mandatory, string>, 
  username: <mandatory, string>,
  password: <mandatory, string> 
}
```
