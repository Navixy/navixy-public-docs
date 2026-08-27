# Sending commands to a GPS tracker via IP protocol (OTA)

Most modern GPS tracking devices can be reconfigured remotely using IP commands sent over-the-air (OTA). With the Navixy API, you can easily change device configurations or develop applications that allow users to send commands directly from the UI. These commands can also be integrated into automation rules, status changes, or parameter triggers, offering extensive flexibility and customization for managing your telematics solutions.

## Sending a command

To send a command to a device, you only need the following parameters:

* `tracker_id`: The ID of the device to which you want to send the command.
* `command`: The text or hexadecimal representation of the command in a protocol-dependent manner.

#### Example: reconfiguring a Teltonika FMB140 device

Suppose you have a Teltonika FMB140 device with an ID of `231402` on the Navixy platform, and you want to reconfigure its IP address to `52.57.1.136`. According to the protocol, the command should be:

`setparam 2004:52.57.1.136`

### API Request

To send this command, use the API request [`raw_command/send`](../../resources/tracking/tracker/#post-tracker-raw_command-send):

{% code title="cURL" %}
```shell
curl --request POST \
  --url 'https://api.eu.navixy.com/v2/tracker/raw_command/send' \
  --header 'Content-Type: application/json' \
  --data '{
        "hash": "a6aa75587e5c59c32d347da438505fc3",
        "tracker_id": 231402,
        "command": "setparam 2004:52.57.1.136"
    }'
```
{% endcode %}

Upon successful execution, the platform will confirm that the command has been sent.

This method lets you manage and control your GPS tracking devices via IP, with configuration updates applied in real time.
