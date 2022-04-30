# SwitchBotAPI-BLE

- [Device Types](#device-type)
- [Bot BLE open API](#bot-BLE-open-API)
- [Color Bulb BLE open API](#color-bulb-BLE-open-API)
- [Contact Sensor BLE open API](#contact-sensor-BLE-open-API)
- [Curtain BLE open API](#curtain-BLE-open-API)
- [LED Strip Light BLE open API](#LED-strip-light-BLE-open-API)
- [Meter BLE open API](#meter-BLE-open-API)
- [Motion Sensor BLE open API](#motion-sensor-BLE-open-API)
- [Plug Mini BLE open API](#plug-mini-BLE-open-API)

## Device Types

| Product         | Device Type |
|-----------------|-------------|
| Bot             | H (0x48)    |
| Meter           | T (0x54)    |
| Humidifier      | e (0x65)    |
| Curtain         | c (0x63)    |
| Motion Sensor   | s (0x73)    |
| Contact Sensor  | d (0x64)    |
| Color Bulb      | u (0x75)    |
| LED Strip Light | r (0x72)    |
| Smart Lock      | o (0x6F)    |
| Plug Mini       | g (0x67)    |
| Meter Plus      | i (0x69)    |

The device type is in the service data of SCAN_RSP.

| Service data |          |                         |
|--------------|----------|-------------------------|
| Byte: 0      | Enc type | Bit[7] NC               |
| Byte: 0      | Dev Type | Bit [6:0] – Device Type |

## Bot BLE open API

- [Bot Broadcast Message](#bot-broadcast-message)
- [Broadcast Message Format](#broadcast-message-format)
  - [(Old) Broadcast Message](#old-broadcast-message)
  - [(New) Broadcast Message](#new-broadcast-message)
- [Broadcast Mode](#broadcast-mode)
  - [SwitchBot Mode (Default)](#switchBot-mode-default)
  - [Simple Mode](#simple-mode)
  - [iBeacon Mode (if you need welcome function)](#ibeacon-mode)
- [BLE Communication Data Message Basic Format](#ble-communication-data-message-basic-format)
- [0x01 Execute an Action](#0x01-execute-an-action)
- [0x02 Get Device Basic Info](#0x02-get-device-basic-info)
- [0x03 Set Device Basic Info](#0x03-set-device-basic-info)
- [0x08 Get Device Time Management Info](#0x08-get-device-time-management-info)
- [0x09 Set Device Time Management Info](#0x09-set-device-time-management-info)
- [0x0f Extended Command](#0x0f-extended-command)
  - [0x08 Set long press duration](#0x08-set-long-press-duration)

### Bot Broadcast Message

  - This broadcast message defines the Service data of Scan Rsp of the
    specific Device.

<!-- end list -->

  - The length of the Service data is different based on Device Type.
    The Service data can be 8 bytes max. The Byte: 0, Byte: 1 and Byte:
    2 are for every Device Type. The bytes start from Byte: 3 depends on
    different Device Type. Please refer to Device Type definition in the
    table below.
  - Broadcast modes are defined as:
      - SwitchBot Mode (Default)
      - Simple Mode
      - iBeacon Mode

| Product Name                     | HEX  | ASCII       | Note        |
| -------------------------------- | ---- | ----------- | ----------- |
| SwitchBot Bot (WoHand)           | 0x48 | 'H'         |             |
| WoButton                         | 0x42 | 'B'         |             |
| SwitchBot Hub (WoLink)           | 0x4C | 'L'         | Add Mode    |
|                                  | 0x6C | 'l'         | Normal      |
| SwitchBot Hub Plus (WoLink Plus) | 0x50 | 'P'         | Add Mode    |
|                                  | 0x70 | 'p'         | Normal Mode |
| SwitchBot Fan (WoFan)            | 0x46 | 'F'         | Add Mode    |
|                                  | 0x66 | 'f'         | Normal Mode |
| SwitchBot MeterTH (WoSensorTH)   | 0x74 | 't'         | Add Mode    |
|                                  | 0x54 | 'T'         | Normal Mode |
| SwitchBot Mini (HubMini)         | 0x4D | 'M'         | Add Mode    |
|                                  | 0x6D | 'm'         | Normal Mode |

Device Type

### Broadcast Message Format

#### (Old) Broadcast Message

Use this for firmware version before Bot v30, Remote v20 and Hub v6.

**Advertise:**

  - Manufacturer data: includes device's company ID and MAC address
    (support WeChat protocol)

**Scanrsp:**

  - Device Name: "Bot", "Remote", "Hub", "WoSensorTH"
  - UUID: *cba20d00-224d-11e6-9fb8-0002a5d5c51b* and *fee7*

  
\====(New) Broadcast Message==== The length of the Service data is
different based on Device Type. The Service data can be 8 bytes max.

The bit\[6:0\] in Byte: 0 of the broadcast message is Device Type. The
Byte: 0, Byte: 1 and Byte: 2 are for every Device Type. The bytes start
from Byte: 3 depends on different Device Type, serving as general
registers.

The backend also needs a mapping table of the Service data, so both the
backend and app can parse the Service data.

<table>
<tbody>
<tr class="odd">
<td><p>Service data</p></td>
</tr>
<tr class="even">
<td><p>Byte: 0</p></td>
</tr>
<tr class="odd">
<td><p>Bit[6:0] - Device Type Please refer to the Device Type table above.</p></td>
</tr>
<tr class="even">
<td><p>Byte: 1</p></td>
</tr>
<tr class="odd">
<td><p>Bit[6] - State</p>
<ul>
<li>0: on</li>
<li>1: off</li>
</ul></td>
</tr>
<tr class="even">
<td><p>Bit[5] - Enc type</p>
<ul>
<li>0: encryption algorithm (the same as the Bot)</li>
<li>1: encryption algorithm 2 or 3 (TBD)</li>
</ul>
<p>Please refer to Byte: 0 Bit[7]</p></td>
</tr>
<tr class="odd">
<td><p>Bit[4] - Data Commit Flag</p>
<ul>
<li>0: service data no data update</li>
<li>1: service data has data update</li>
</ul>
<p>The flag bit needs to be cleared by the Hub or App after the data is successfully updated, and the Device itself will not actively clear this flag.</p></td>
</tr>
<tr class="even">
<td><p>Bit[3] – Group D</p>
<p>Bit[2] – Group C</p>
<p>Bit[1] – Group B</p>
<p>Bit[0] – Group A</p>
<ul>
<li>0: the Device is not in this group</li>
<li>1: the Device is in this group</li>
</ul></td>
</tr>
<tr class="odd">
<td><p>Byte: 2</p></td>
</tr>
<tr class="even">
<td><p>Bit[6:0] - Remaining Battery 0~100%</p></td>
</tr>
<tr class="odd">
<td><p>Byte: 3</p></td>
</tr>
<tr class="even">
<td><p>Bit[5:4] – Humidity Alert Status</p>
<ul>
<li>0: no alert</li>
<li>1: low humidity alert (humidity lower than the low limit)</li>
<li>2: high humidity alert (humidity higher than the high limit)</li>
<li>3: temp humidity (humidity higher than than the low limit and lower than the high limit)</li>
</ul></td>
</tr>
<tr class="odd">
<td><p>Bit[3:0] – Decimals of the Temperature</p>
<ul>
<li>0000 – 1001:0~9</li>
</ul></td>
</tr>
<tr class="even">
<td><p>Byte: 4</p></td>
</tr>
<tr class="odd">
<td><p>Bit[6:0] – Integers of the Temperature 000 0000 – 111 1111: 0~127 °C</p>
<p>Celsius to Fahrenheit: F=(C*1.8)+32</p></td>
</tr>
<tr class="even">
<td><p>Byte: 5</p></td>
</tr>
<tr class="odd">
<td><p>Bit[6:0] – Humidity Value 000 0000 – 110 0011: 0~99%</p></td>
</tr>
</tbody>
</table>

### Broadcast Mode

#### SwitchBot Mode (Default)

**Advertise:**

  - Manufacturer data: include device's company ID (0x0059 Nordic) and
    MAC address (support WeChat protocol)
  - BLE\_ADVDATA\_NO\_NAME

**Scanrsp:**

  - UUID: *cba20d00-224d-11e6-9fb8-0002a5d5c51b* and *fee7*
  - BLE\_ADVDATA\_NO\_NAME

**Example**:

<img alt="broadcast_1" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/broadcast_1.png"></a>
<img alt="broadcast_2" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/broadcast_2.png"></a>
  
\====Simple Mode==== **Advsertise:**

  - BLE\_ADVDATA\_NO\_NAME

**Scanrsp:**

  - UUID (cba20d00-224d-11e6-9fb8-0002a5d5c51b)
  - BLE\_ADVDATA\_NO\_NAME

  

#### iBeacon Mode (if you need welcome function)

**Advertise:**

Standard iBeacon package

  - UUID:cba20d00-224d-11e6-9fb8-0002a5d5c51b
  - Major ID:
  - High Byte: bit 7:4 - beacon type
  - Bit\[3:0\] - beacon data
  - Low Byte: beacon data
  - Minor ID:beacon data
  - TX power: 0xc3
  - BLE\_ADVDATA\_NO\_NAME

| beacon data instruction |
| ----------------------- |
| Beacon Type             |
| 0                       |

**Scanrsp:**

  - UUID(*cba20d00-224d-11e6-9fb8-0002a5d5c51b*)
  - BLE\_ADVDATA\_NO\_NAME

### BLE Communication Data Message Basic Format

  - The controlling terminal (short as the Terminal below) and the
    configured device (short as the Device below) use BLE to communicate
    with each other wirelessly. During the communication, the Terminal
    acts as a central device, while the Device acts as peripheral
    device. The Terminal gets basic device info by reading the broadcast
    message of the Device. They exchange data by using read and write
    characteristic of communication service.

<!-- end list -->

  - All bilateral communication is after the BLE connection established.
    The Terminal send a REQ message to the Device, and then the Device
    returns a RESP message.

<!-- end list -->

  - All communication message data length is 1-20 bytes, i.e. MTU=20.
    There are 2 types of REQ messages sending from the Terminal to the
    Device, encrypted and unencrypted.

<!-- end list -->

  - **Communication service UUID:**
      - *cba20d00-224d-11e6-9fb8-0002a5d5c51b*
      - UUID TYPE:Vendor UUID types start at this index (128-bit)

<!-- end list -->

  - **RX characteristic UUID of the message from the Terminal to the
    Device:**
      - UUID: *cba20002-224d-11e6-9fb8-0002a5d5c51b*
      - UUID TYPE: Vendor UUID types start at this index (128-bit)
      - Char Attribute: RW
      - Char Properties: notify      

<!-- end list -->

  - **TX  characteristic UUID of the message from the Device to the
    Terminal:**
      - *cba20003-224d-11e6-9fb8-0002a5d5c51b*
      - UUID TYPE: Vendor UUID types start at this index (128-bit)
      - Char Attribute: RW

All communication message data length is 1-20 bytes, i.e. MTU=20.

The REQ data from the Terminal to the Device is as below:

<table>
<tbody>
<tr class="odd">
<td><p>REQ message</p></td>
</tr>
<tr class="even">
<td><p>Byte: 0</p></td>
</tr>
<tr class="odd">
<td><p>Byte: 1</p></td>
</tr>
<tr class="even">
<td><p>Bit [5:4] - Encryption Mode 0 - no encryption</p></td>
</tr>
<tr class="odd">
<td><p>Bit [3:0] - Command 0x01 - Execute an Action</p>
<p>0x02 - Get Device Basic Info</p>
<p>0x03 - Set Device Basic Info</p>
<p>0x08 - Get Device Time Management Info</p>
<p>0x09 - Set Device Time Management Info</p>
<p>0x0f – Enable command_ext in Byte: 2</p></td>
</tr>
<tr class="even">
<td><p>Byte: 2</p></td>
</tr>
<tr class="odd">
<td><p>Byte: 3-16</p></td>
</tr>
</tbody>
</table>

Payload complement the data info to the Commands. The Payload format
varies for different Commands.

The basic format of RESP message from the Terminal to the Device:

|              |
| ------------ |
| RESP message |
| Byte: 0      |
| Byte: 1-19   |

Status returns the handling result of the Device to the earlier command,
which is the 1st Byte of every RESP message, and also mandatory.

Payload complement the data info to the Commands. The data length is
0-19 bytes depends on different cases, and can also be omitted.

The format of the Payload is up to the Command type it is responding and
the handling result.

  
\===0x01 Execute an Action===

  - By sending this command, the Bot will complete a major action:
  - Bot: Execute an action of moving the arm to press down and then move
    it back.

<!-- end list -->

  - If executed successfully, the Status Byte of the RESP message will
    be 1.

|                                         |
| --------------------------------------- |
| REQ message payload                     |
| byte: 0                                 |
| Byte: 1-2                               |
| Byte:2                                  |
| Byte: 3-4                               |
| Byte: 4                                 |
| Byte:5-6                                |
| Byte: 6                                 |
| Byte: 7-8                               |
| Byte: 8                                 |
| Byte:9-10                               |
| Byte: 10                                |
| Byte:11-12                              |
| Byte: 12                                |
| Byte:13-14                              |
| Byte: 14                                |
| Byte:15                                 |
| Unencrypted Mode                        |
| Byte: 16 Not existed for Encrypted Mode |
| Byte: 17                                |

Note: Byte: every 2 bytes are the same group for byte 1-16. The odd byte
represents the time interval of the coming action to the previous one,
the even byte means the action to be executed in the action list. The
time internal should not be set as 0, because the first time interval of
0 means the end of the action list. Regardless the value of Byte 17, the
action list never stop.

**Example**:

In current firmware version, Terminal send Device a message of "0x57
0x01 0x00" to control the Bot to move the arm.

The Status Byte of the RESP message will be 1 when executed
successfully, and the Payload will be like:

|                      |
| -------------------- |
| RESP message payload |
| byte: 0              |

Use NRF Connect to issue command "0x570100" to the Device, the Bot arm
moves.

Note:

When the Device is in the Add-on mode，the replied RESP message will be
0x0548C0, Status is 0x05 Device does not support this Command.

When the Device is not in the Add-on mode, i.e the normal press mode,
the replied RESP message will be 0x01FF00, Status is 0x01 OK Action
executed.

<img alt="Execute_1" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/Execute_1.png"></a>
<img alt="Execute_2" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/Execute_2.png"></a>
<img alt="Execute_3" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/Execute_3.png"></a>

### 0x02 Get Device Basic Info

  - This command is used to get the basic info of a Device:

<!-- end list -->

  - REQ message payload is 0.

<!-- end list -->

  - If executed successfully, the Status Byte of the RESP message will
    be 1. The payload is the Device basic info:
      - This command is used to get the basic info of a SwitchBot (the
        Bot), remaining battery, firmware version, number of timers, act
        mode, hold-and-press timer, etc.

REQ message payload is 0.

|                        |
| ---------------------- |
| REQ message payload: 0 |

If executed successfully, the Status Byte of the RESP message will be 0,
payload format is as below:

|                      |
| -------------------- |
| RESP message payload |
| Byte: 0              |
| Byte: 1              |
| Byte: 2              |
| Byte: 3-4            |
| Byte: 5-6            |
| Byte: 7              |
| Byte: 8              |
| Byte: 9              |
| Byte: 10             |
| Byte: 11             |

**Example**:

Use NRF Connect to issue command 0x5702 to the Device and get relevant
info.

Replied RESP message is 01642C64000000A10000004800

Status Byte is 0x01 OK Action executed;

RESP message payload Byte 0 is 0x64: 100% remaining battery.

RESP message payload Byte 1 is 0x2C: 44\*0.1=4.4 Firmware Version;

RESP message payload Byte 2 is 0x64: 100 The strength to push button;

RESP message payload Byte 3-4 is 0x00 0x00: The ADC value read from
sensor;

RESP message payload Byte 5-6 is 0x00 0xA1: The motor calibration value;

RESP message payload Byte 7 is 0x00: The number of Timer is 0;

RESP message payload Byte 8 is 0x00: The act mode of Bot: Not Add-on
Mode, i.e. normal press mode;

RESP message payload Byte 9 is 0x00: Hold-and-press Times;

RESP message Payload Byte 10 is 0x48: service data Byte 0;

RESP message Payload Byte 11 is 0x00: service data Byte 1;

<img alt="get_info_1" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/get_info_1.png"></a>

### 0x03 Set Device Basic Info

  - This command is used to set basic info of a Device:

<!-- end list -->

  - REQ message payload is 2.
      - This command could be used to set SwitchBot act mode.

<!-- end list -->

  - If executed successfully, the Status Byte of the RESP message will
    be 1. The payload is 2:

|                      |
| -------------------- |
| REQ message payload: |
| Byte: 0              |
| Byte: 1              |

If executed successfully, the Status Byte of the RESP message will be 1.
The payload is 2:

|                        |
| ---------------------- |
| RESP message status: 1 |
| Byte: 0                |

|                       |
| --------------------- |
| RESP message payload: |
| Byte: 0               |
| Byte: 1               |

**Example**:

Use NRF Connect to issue command 0x57036310 to a Device, set the Device
as Add-on mode. If the Device (i.e. the Bot) was not in Add-on mode, the
Bot arm will moves accordingly once mode set.

Replied RESP message is 0x016300;

Status Byte is 0x01 OK Action executed

Then use NRF Connect to issue a command 0x5702 to the Device and get
some basic setting info.

The replied RESP message is, let's say, 01642C64000000A10000004800;

Status Byte is 0x01 OK Action executed

RESP message payload Byte 2 is 0x63: 99 The strength to push button;

RESP message payload Byte 8 is 0x10: The act mode of Bot: Add-on Mode;

<img alt="set_info_1" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/set_info_1.png"></a>
<img alt="set_info_2" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/set_info_2.png"></a>

### 0x08 Get Device Time Management Info

  - Used to get time management info, including current time, the number
    of alarms, the time of the alarms.

<!-- end list -->

  - REQ message payload is 1 (3 Subcmd: get current time, get the number
    of alarms, get the alarm time).

<!-- end list -->

  - If executed successfully, the Status Byte of the RESP message will
    be1，payload is up to Subcmd type.

REQ message payload:

|                      |
| -------------------- |
| REQ message payload: |
| Byte: 0              |

If executed successfully, the Status Byte of the RESP message will be 0.
For different subcmd, the Payload will be:

Subcmd=1:

|                       |
| --------------------- |
| RESP message payload: |
| Byte: 0-7             |

Subcmd=2:

|                       |
| --------------------- |
| RESP message payload: |
| Byte: 0               |

Subcmd=0xn3(n=0..4) what is the Nth task:

|                       |
| --------------------- |
| RESP message payload: |
| Byte: 0               |
| Byte: 1               |
| Byte: 2               |
| Byte: 3               |
| Byte: 4               |
| Byte: 5               |
| Byte: 6               |
| Byte: 7               |
| Byte: 8-10            |
| MM                    |
| SS (in step of 10)    |

**Example**:

Use NRF Connect to issue command 0x570802 to the Device, get the number
of alarms.

The replied RESP message is 0x0103;

Status Byte is 0x01 OK Action executed

RESP message payload Byte 0 is 0x03: Total number of tasks;

<img alt="get_time_1" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/get_time_1.png"></a>

### 0x09 Set Device Time Management Info

  - Used to set Device time management info, including current time, the
    number of alarms, the time of the alarms.
  - Always set all 5 build-in timers simultaneously when setting alarm
    time.

<!-- end list -->

  - REQ message payload depends on different Subcmd (3 types of Subcmd:
    set current time, set the number of alarms, set time of the alarms).

<!-- end list -->

  - If executed successfully, the Status Byte of the RESP message will
    be 1, payload will be 0.

There are 3 formats for REQ message payload:

1\.    

|                      |
| -------------------- |
| REQ message payload: |
| Byte: 0              |
| Byte: 1-8            |

2\.   ** **

|                      |
| -------------------- |
| REQ message payload: |
| Byte: 0              |
| Byte: 1              |

3\.    

|                       |
| --------------------- |
| REQ message payload:  |
| Byte: 0               |
| Byte: 1               |
| Byte: 2               |
| Byte: 3               |
| Byte: 4               |
| Byte: 5               |
| Byte: 6               |
| Byte: 7               |
| Byte: 8               |
| Byte: 9-11            |
| MM                    |
| SS (Multiples of 10s) |

If executed successfully, the Status Byte of the RESP message will be 1,
payload will be 0.

|                        |
| ---------------------- |
| RESP message status: 1 |
| Byte: 0                |

|                         |
| ----------------------- |
| RESP message payload: 0 |

**Example**:

Use NRF Connect to issue command 0x57090203 to a Device, and set 3
alarms to the Device.

Replied RESP message is 0x01;

Status Byte is 0x01 OK Action executed

<img alt="set_time_1" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/set_time_1.png"></a>

### 0x0f Extended Command

#### **0x08 Set long press duration**

  - To set the duration (in second) for SwitchBot arm to long press

<!-- end list -->

  - REQ Packet payload: 1

<!-- end list -->

  - If executed successfully, the Status Byte of the RESP message will
    be 1, payload will be 0.

REQ Packet payload: 1

|                     |
| ------------------- |
| REQ Packet payload: |
| Byte: 0             |

If executed successfully, the Status Byte of the RESP message will be 1,
payload will be 0.

|                          |
| ------------------------ |
| RESP Packet status:    1 |
| Byte: 0                  |

**Example**:

Use NRF Connect to send a command 0x570f0803 to a Device, and set the
long press duration to 3 seconds.

If the received RESP packet is 0x01

Replied RESP message is 0x01, meaning a successful setting.

<img alt="set_long_press_1.png" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/set_long_press_1.png"></a>

CopyRight@2019 Wonderlabs, Inc.

# Color Bulb BLE open API

# Contact Sensor BLE open API

# Curtain BLE open API

# LED Strip Light BLE open API

# Meter BLE open API

 # Motion Sensor BLE open API

# Plug Mini BLE open API
