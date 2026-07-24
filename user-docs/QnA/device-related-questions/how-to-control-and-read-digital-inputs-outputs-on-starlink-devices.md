# How to Control and Read Digital Inputs/Outputs on Starlink Devices

### Question

How do I control digital outputs on Starlink devices, and how do I get the status of inputs and outputs?

### Answer

The ERM output control mechanism is a bit complex. However, this quick guide explains the output command structure, the output control commands, the checksum calculation formula, and the command required to retrieve the input and output status. This will help verify that the device is transmitting the correct information to the platform.

#### Output command structure

General format:

**$SRVFFFFFFF,13,01,XX,Y\*CS**

| Part      | Example    | Meaning                                                                                         |
| --------- | ---------- | ----------------------------------------------------------------------------------------------- |
| `$SRV`    | `$SRV`     | Command prefix                                                                                  |
| `FFFFFFF` | `FFFFFFF`  | Device identifier / password When sending commands, the destination ID may be set to `FFFFFFF`. |
| `13`      | `13`       | Command code (output control)                                                                   |
| `01`      | `01`       | Fixed command parameter                                                                         |
| `XX`      | `01`…`04`  | Digital output number                                                                           |
| `Y`       | `1` or `0` | State: `1` = ON (activate), `0` = OFF (deactivate)                                              |
| `*CS`     | `*A6`      | `*` separator + 2-digit hex checksum                                                            |

Example:

$SRVFFFFFFF,13,01,01,1\*A6

* Output `01`
* State `1` → activation
* Checksum `A6`

***

#### Output commands (outputs 1 to 4)

$SRVFFFFFFF,13,01,01,1\*A6 ← Output 1 ON

$SRVFFFFFFF,13,01,01,0\*A5 ← Output 1 OFF

$SRVFFFFFFF,13,01,02,1\*A7 ← Output 2 ON

$SRVFFFFFFF,13,01,02,0\*A6 ← Output 2 OFF

$SRVFFFFFFF,13,01,03,1\*A8 ← Output 3 ON

$SRVFFFFFFF,13,01,03,0\*A7 ← Output 3 OFF

$SRVFFFFFFF,13,01,04,1\*A9 ← Output 4 ON

$SRVFFFFFFF,13,01,04,0\*A8 ← Output 4 OFF

***

#### Checksum formula

The checksum is additive (not NMEA XOR):

checksum = (0xA4 + N + S) & 0xFF

* `N` = output number (`1`…`4`)
* `S` = state (`1` = ON, `0` = OFF)
* The result is a 2-digit hexadecimal value

Quick rules:

* Change state `1 → 0` → checksum −1
* Increase output number by 1 → checksum +1

Examples:

* Output 1 ON → `A4 + 1 + 1 = A6`
* Output 1 OFF → `A4 + 1 + 0 = A5`
* Output 4 ON → `A4 + 4 + 1 = A9`
* Output 4 OFF → `A4 + 4 + 0 = A8`

***

#### Get input/output status

Request:

$SRVFFFFFF,12,01\*BB

Response structure:

$SLU,12,11496,\<number\_of\_inputs\_and\_outputs>,OUT1,in1,OUT2,in2,OUT3,in3;OUT4,in4

| Field                            | Meaning                              |
| -------------------------------- | ------------------------------------ |
| `$SLU`                           | Status response prefix               |
| `12`                             | Status command code                  |
| `11496`                          | Device / response identifier         |
| `<number_of_inputs_and_outputs>` | Count of I/O channels reported       |
| `OUT1`…`OUT4`                    | Current state of each digital output |
| `in1`…`in4`                      | Current state of each digital input  |

Example pattern: each output is followed by its related input value (`OUT1,in1`, `OUT2,in2`, etc.).



## Links

[Air Console](https://navixy.com/docs/admin/devices/air-console)

[ERM Advance Telematics Devices](https://navixy.com/ms/devices/erm-advanced-telematics/)
