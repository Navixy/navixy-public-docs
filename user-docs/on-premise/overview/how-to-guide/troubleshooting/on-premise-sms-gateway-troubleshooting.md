---
description: Troubleshoot SMS gateway failures on Navixy on-premise. Check activation status, verify service operation, and resolve connection and authentication errors.
---

# On-Premise SMS gateway troubleshooting

The Navixy platform offers the capability to seamlessly integrate with SMS gateways, enabling the sending of messages. To leverage all the advantages of this feature, it's necessary to establish a connection with an SMS gateway. It's important to note that this functionality is not activated by default and can only be accessed once you configure the SMS gateway settings and activate it in the database.

{% hint style="info" %}
SMS gateway is not configured in Admin panel. To apply or change its settings, you need a direct access to database.
{% endhint %}

SMS gateway is used for the following purposes:

* Device activation (sending activation SMS commands).
* Sending event notifications (alerts).
* Device configuration directly from the user interface.

Here we outline troubleshooting steps if you are experiencing problems with text message delivery as well as automatic device activation.

## Check SMS gateway settings

First thing to check is whether you actually have an SMS gateway activated for the platform. To do this, access your database and execute the following SQL query:

```
select * from google.sms_gates_to_dealers;
```

If the output is empty, then you have no SMS gateway activated. Proceed to [SMS gateway configuration](../configuration/sms-gateway-configuration/) section to find information on setting it up.

If the output contains information, remember the `gate_id` value and execute the following query:

```
select * from google.sms_gates;
```

Find the active gateway by its `id` value (obtained as `gate_id` previously) and make sure its connection parameters in `params` column are valid. If they are incorrect, edit them.

If all the parameters are fine, and SMS gateway is activated, but no messages are delivered, proceed to the next step.

## Check messaging service status

If setting seem correct, make sure that [Navixy SMS-server](system-components.md#navixy-sms-server) is up and running. This is the service responsible for all the messaging. Without it, neither emails nor SMS can be sent.

[Check the service status](checking-service-statuses.md), and if it is down, try [restarting](../maintenance/restarting-instance.md) it or the entire platform.

If SMS-server is running but messages are still not delivered, proceed to the next step.

## Check service logs

Check [SMS-server log](system-components.md) for any errors.

To find errors related to SMS message delivery to a specific phone number, search for log entries containing that phone.

For Linux, use the following command:

```
grep "12345678910" log.txt
```

For Windows, use any advanced text editor, as the default Notepad is unable to handle large text files properly.

The most common errors in SMS gateway operation are as follows:

* Incorrect authentication data (login/password or API tokens).
* SMS gateway address and/or port unavailable (network issues).
* SMS gateway address and/or port incorrectly specified.
* Insufficient SMS gateway funds.
* Exceeded SMS gateway message quota.

All of the above problems can be clearly identified based on log entries.

{% hint style="danger" %}
Certain SMS gateways have been observed to remove the initial double space in an SMS message. However, it is important to note that this space is necessary for activating certain devices, such as Teltonika. If you encounter difficulties while activating your devices, we recommend reaching out to your SMS gateway provider to verify their support for double spaces.
{% endhint %}

If you find any errors indicating a failure on the Navixy platform side, be sure to report them to [technical support](mailto:support@navixy.com), and we will provide all the necessary assistance.
