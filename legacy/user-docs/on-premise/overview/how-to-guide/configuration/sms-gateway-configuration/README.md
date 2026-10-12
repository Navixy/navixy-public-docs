---
description: Configure SMS gateways for Navixy on-premise. Add supported provider settings via SQL queries and manage multiple gateways in the database configuration table.
---

# SMS gateway configuration

Navixy platform supports popular SMS gateways, including Twilio, Nexmo, and others. This document provides instructions for setting up these gateways correctly, as well as information on using SMPP (Short Message Peer-to-Peer) protocol with Navixy.

To learn more about each gateway type, please refer to the corresponding page:

* [SMPP](smpp.md)
* [Twilio](twilio.md)
* [Vonage](vonage.md)
* [Textlocal](textlocal.md)
* [Tyntec](tyntec.md)
* [SMS Traffic](sms-traffic.md)
* [Yeastar](yeastar.md)

{% hint style="info" %}
If you plan to use an SMS gateway not listed in this section, verify with your gateway provider whether the SMPP v3.4 protocol is supported. Most gateways are capable of working with this protocol, giving you the opportunity to connect almost any gateway to Navixy.
{% endhint %}

## Data tables with SMS gateway information

To configure an SMS gateway in Navixy, navigate to the **'sms\_gates'** table in the **'google'** database. Here, you'll find all the necessary settings, which are stored in JSON format. Navixy can work with multiple SMS gateways simultaneously, so you can add and update settings for each gateway as needed.

In addition to the 'sms\_gates' table, there is also an 'sms\_gates\_to\_dealers' table that links specific SMS gateways to dealers.

To see the relevant rows in these tables, use the following commands in MySQL:

```sql
DESCRIBE google.sms_gates;
DESCRIBE google.sms_gates_to_dealer.
```

to see additional details:

```sql
mysql> DESCRIBE sms_gates;
 +--------------+---------------------+------+-----+---------+----------------+
 | Field | Type | Null | Key | Default | Extra |
 +--------------+---------------------+------+-----+---------+----------------+
 | id | int(11) unsigned | NO | PRI | NULL | auto_increment |
 | owner_id | int(11) | YES | | 1 | |
 | label | varchar(255) | YES | | | |
 | type | varchar(32) | NO | | NULL | |
 | provider | varchar(32) | NO | | NULL | |
 | params | text | NO | | NULL | |
 | enabled | tinyint(4) unsigned | NO | | 1 | |
 | class_filter | varchar(255) | NO | | * | |
 +--------------+---------------------+------+-----+---------+----------------+
```

### **Table fields description**

<table><thead><tr><th width="134.9091796875">Field</th><th>Description</th></tr></thead><tbody><tr><td><strong>id</strong></td><td>Identifier of the SMS gateway. When adding a new SMS gateway the identifier value is automatically incremented if the corresponding field is left empty. This identifier is used to uniquely identify the SMS gateway within the system.</td></tr><tr><td><strong>owner_id</strong></td><td>Points to a PaaS account account id (table 'dealer_id')</td></tr><tr><td><strong>label</strong></td><td>Descriptive name of SMS gateway to avoid confusion</td></tr><tr><td><strong>type</strong></td><td>The SMS gateway can operate in three modes: transmitter, transceiver, or receiver, depending on its capabilities and the requirements</td></tr><tr><td><strong>provider</strong></td><td>Name of your SMS provider</td></tr><tr><td><strong>params</strong></td><td>The main settings for an SMS gateway are stored in JSON format in the 'params' field. For some popular providers, we've included descriptions (see below) of the specific parameters required to configure the gateway correctly.</td></tr><tr><td><strong>enabled</strong></td><td>Each SMS gateway can be either enabled or disabled, with a value of 1 indicating that the gateway is active and 0 indicating that it's inactive.</td></tr><tr><td><strong>class_filter</strong></td><td>The field indicates the type of messages that the gateway can process. The possible values are 'notifications' (if the gateway only processes notifications), 'commands' (if the gateway only processes commands), or an asterisk (*) which indicates that the gateway can handle both notifications and commands. To restrict the gateway to a specific message type, you can add a minus sign (-) before the value ('-notification' or '-command'). This revokes the gateway's ability to handle the corresponding message type.</td></tr></tbody></table>

## Parameters for popular SMS gateways in JSON format

To configure an SMS gateway in Navixy, use the 'google.sms\_gates' table in the database. The table contains various settings for popular SMS gateways as well as SMPP connection configurations (which can be used with any SMS center that uses SMPP protocol). Further in this section, the most common settings required for SMS gateways are summarized, with only the mandatory settings listed.
