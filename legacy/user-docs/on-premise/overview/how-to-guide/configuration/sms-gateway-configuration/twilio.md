---
description: Configure the Twilio SMS gateway in Navixy on-premise for global connectivity. Add credentials using the SQL configuration example for the sms_gates table.
---

# Twilio

[Twilio](https://twilio.co) is a widely-used global connectivity service provider based in California, known for its reliability and coverage in many countries, particularly the US, Canada, and the UK.

To use Twilio as an SMS gateway in Navixy, you'll need to obtain your `ACCOUNT_SID` and `AUTH_TOKEN` (or `API_SID`/`API_SECRET` pair), which you can acquire by registering a Twilio account. These credentials are necessary for authentication and must be included in the JSON configuration for the Twilio gateway in the **'sms\_gates'** table of the **'google'** database.

## Navixy JSON configuration for Twilio

Navixy JSON configuration for Twilio is a set of parameters that are required to configure the Twilio SMS gateway in Navixy. These parameters are defined in JSON format and stored in the **'params'** field of the **'sms\_gates'** table in the **'google'** database.

Recommended configuration with `account_sid` and `auth_token`:

```json5
 field params:
 {
 "account_sid": "ACCOUNT_SID",
 "auth_token": "AUTH_TOKEN",
 "enable_status_callback": false
 }
 type: transceiver
 provider: twilio
```

or, alternatively, with `account_sid`, `api_sid`, `api_secret`:

```json5
 field params:
 {
 "account_sid": "ACCOUNT_SID",
 "api_sid": "API_SID",
 "api_secret": "API_SECRET",
 "enable_status_callback": false
 }
 type: transceiver
 provider: twilio
```

To locate your Twilio 'account\_sid' and 'auth\_token', log in to your [Twilio dashboard](https://www.twilio.com/user/account) and click on 'Show API Credentials' to access your account information.

## SQL queries to execute

To update the database with the correct settings for using Twilio as an SMS gateway, use the following SQL query:

{% code overflow="wrap" %}
```sql
INSERT INTO google.sms_gates (type, provider, params, enabled, class_filter) VALUES ('transceiver', 'twilio','{"account_sid": "ACCOUNT_SID","auth_token":"AUTH_TOKEN", "enable_status_callback": true}',1,'*');
```
{% endcode %}

### **Sender phone number**

To configure the phone number provideded by Twilio execute two additional SQL queries:

{% code overflow="wrap" %}
```sql
UPDATE google.dealers SET master_phone="TWILIO_PHONE", from_sms="TWILIO_PHONE" WHERE dealer_id=1;
INSERT INTO google.sms_gates_to_dealers (dealer_id, gate_id) VALUES (0, 1);
```
{% endcode %}

`TWILIO_PHONE` stands for the phone number you have leased from Twilio. The first SQL query adds the phone number that will be used in the '**from**' field with Twilio API. The second query one links the SMS gateway with the dealer (PaaS) account.

### **Incoming messages**

To get incoming messages via Twilio provider add the following URL into their configuration interface:

`http://$IPADDR:22000/sms/$GATEID/incoming`

Where

* `$IPADDR` - is an ip address of the server or its domain name
* `$GATEID` - id of sms gate which is processing incoming messages
* `22000` - port used to listen connects (default value)

## Known issues with Twilio

When using the Twilio SMS gateway with Navixy, there is a potential issue with SMS commands that contain leading spaces. In some cases, Twilio may remove these spaces when processing the message, which can result in a failure to execute the intended command.

For example, if you send an SMS command to a device with a leading space, such as `" 123456"`, Twilio may remove the space and send the command as `"123456"` to the device. If the device requires the space to be present in order to recognize the command, it will not be executed properly. This can cause confusion and frustration for both users and system administrators.

Specifically, this issue was confirmed with Teltonika and Ruptela devices when the password for protecting configuration is not set on the device.

To avoid this issue, we recommend using alternative SMS connectivity provides, such as [Vonage](vonage.md) or [Textlocal](textlocal.md).
