---
description: Integrate a Telegram bot with Navixy IoT Logic to send automated notifications. Set up the bot, configure the chat ID, and connect the webhook.
---

# How to connect Telegram with Navixy IoT Logic

This is a quick guide of the steps that might be followed to create the connection between IoT Logic and Telegram.&#x20;

1. Create a bot and get your token

- In Telegram, open the chat with @BotFather.
- Run the command /newbot.
- Follow the instructions and copy the bot token provided (it looks like: 123456:ABC-...).

2. Get the chat_id you want to message

Common ways to do this:

- Send a message to your bot (or add it to a group and send a message there).
- Then call the getUpdates method from the Telegram API.
- If it's a group, the chat_id is usually a negative number.
- Make sure the bot has received at least one message so it appears in getUpdates.

<figure><img src="../../.gitbook/assets/unknown (6).png" alt=""><figcaption></figcaption></figure>

3. Send the message (REST call)

- Use the sendMessage method from the Telegram API.
- In the request body, include the chat_id and the message text.
- The request should be sent in JSON format with the header Content-Type: application/json.

<figure><img src="../../.gitbook/assets/unknown (7).png" alt=""><figcaption></figcaption></figure>

4. Create the IoT Flow in Navixy

- Once the flow has been created, connect the Webhook node in the section where you want to send the data.
- Follow the same structure as the API call. In the URL field, place the request endpoint:

https://api.telegram.org/bot\<BOT_TOKEN>/sendMessage

- In the Headers section:
- Key: Content-Type
- Value: application/json
- Finally, in the body of the request, include the telemetry parameters you want to send, making sure to include the chat_id again, for instance:

{

&#x20; "chat_id": X,

&#x20; "text": "Device 12563254, latitude: \{{latitude\}}, longitude: \{{longitude\}}, satellites: \{{satellites\}}, speed: \{{speed\}}"

}

![](<../../.gitbook/assets/unknown (8).png>) ![](<../../.gitbook/assets/unknown (9).png>)

With these steps, your bot will be able to receive messages successfully, including automated telemetry data from Navixy.
