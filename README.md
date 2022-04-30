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

* This broadcast message defines the Service data of Scan Rsp of the specific Device.

* The length of the Service data is different based on Device Type. The Service data can be 8 bytes max. The Byte: 0, Byte: 1 and Byte: 2 are for every Device Type. The bytes start from Byte: 3 depends on different Device Type. Please refer to Device Type definition in the table below.
* Broadcast modes are defined as:
  * SwitchBot Mode (Default)
  * Simple Mode
  * iBeacon Mode

| Product Name                     | HEX  | ASCII       | Note     |
|----------------------------------|------|-------------|----------|
| SwitchBot Bot (WoHand)           | 0x48 | 'H'         |          |
| WoButton                         | 0x42 | 'B'         |          |
| SwitchBot Hub (WoLink)           | 0x4C | 'L'         | Add Mode |
|                                  | 0x6C                             | 'l'  | Normal      |
| SwitchBot Hub Plus (WoLink Plus) | 0x50 | 'P'         | Add Mode |
|                                  | 0x70                             | 'p'  | Normal Mode |
| SwitchBot Fan (WoFan)            | 0x46 | 'F'         | Add Mode |
|                                  | 0x66                             | 'f'  | Normal Mode |
| SwitchBot MeterTH (WoSensorTH)   | 0x74 | 't'         | Add Mode |
|                                  | 0x54                             | 'T'  | Normal Mode |
| SwitchBot Mini (HubMini)         | 0x4D | 'M'         | Add Mode |
|                                  | 0x6D                             | 'm'  | Normal Mode |

### Broadcast Message Format

#### (Old) Broadcast Message
Use this for firmware version before Bot v30, Remote v20 and Hub v6.

'''Advertise:'''

*Manufacturer data: includes device's company ID and MAC address (support WeChat protocol)


'''Scanrsp:'''

*Device Name: "Bot", "Remote", "Hub", "WoSensorTH"
*UUID: ''cba20d00-224d-11e6-9fb8-0002a5d5c51b'' and ''fee7''

<br />
#### (New) Broadcast Message
The length of the Service data is different based on Device Type. The Service data can be 8 bytes max. 

The bit[6:0] in Byte: 0 of the broadcast message is Device Type. The Byte: 0, Byte: 1 and Byte: 2 are for every Device Type. The bytes start from Byte: 3 depends on different Device Type, serving as general registers.

The backend also needs a mapping table of the Service data, so both the backend and app can parse the Service data.

{| class="wikitable"
| colspan="3" |Service data
|-
| rowspan="2" |Byte: 0
| rowspan="2" |Enc Type

Dev Type
|Bit[7] - Reserved
When Enc type - Byte: 1 Bit[5] = 0

*0: no encryption
*1: encryption algorithm 1 (the same as the Bot)

When Enc type - Byte: 1 Bit[5] = 1

*0: encryption algorithm 2 (TBD)
*1: encryption algorithm 3 (TBD)
|-
|Bit[6:0] - Device Type
Please refer to the Device Type table above.
|-
| rowspan="5" |Byte: 1
| rowspan="5" |Status
|Bit[7] - Switchbot Mode

*0: one state mode
*1: on/off state mode
|-
|Bit[6] - State

*0: on
*1: off
|-
|Bit[5] - Enc type

*0: encryption algorithm (the same as the Bot)
*1: encryption algorithm 2 or 3 (TBD)

Please refer to Byte: 0 Bit[7]
|-
|Bit[4] - Data Commit Flag

*0: service data no data update
*1: service data has data update

The flag bit needs to be cleared by the Hub or App after the data is successfully updated, and the Device itself will not actively clear this flag.
|-
|Bit[3] – Group D

Bit[2] – Group C

Bit[1] – Group B

Bit[0] – Group A

*0: the Device is not in this group
*1: the Device is in this group
|-
| rowspan="2" |Byte: 2
| rowspan="2" |Update UTC Flag

Battery
|Bit[7] – Sync UTC

*0: no need to synchronize
*1: the Device has passed a time synchronization cycle (10 days by default) and requires a time synchronization

The Device uses UTC time when synchronizing. The Device will actively clear this flag after the Hub or App synchronize successfully.
|-
|Bit[6:0] - Remaining Battery 0~100%
|-
| rowspan="3" |Byte: 3
| rowspan="3" |alarm flag

the fraction part of temperatrue value

(WoSensorTH)
|Bit[7:6] – Temperature Alert Status

*0: no alert
*1: low temp alert (temperature lower than the low limit)
*2: high temp alert (temperature higher than the high limit)
*3: temp alert (temperature higher than than the low limit and lower than the high limit)
|-
|Bit[5:4] – Humidity Alert Status

*0: no alert
*1: low humidity alert (humidity lower than the low limit)
*2: high humidity alert (humidity higher than the high limit)
*3: temp humidity (humidity higher than than the low limit and lower than the high limit)
|-
|Bit[3:0] – Decimals of the Temperature

*0000 – 1001:0~9
|-
| rowspan="2" |Byte: 4
| rowspan="2" |temperature mode
temperature sign

the integer part of temperature value

(WoSensorTH)
|Bit[7] – Temperature Sign

*0: subzero temperature
*1: temperature above zero
|-
|Bit[6:0] – Integers of the Temperature
000 0000 – 111 1111: 0~127 °C

Celsius to Fahrenheit: F=(C*1.8)+32
|-
| rowspan="2" |Byte: 5
| rowspan="2" |humidity value
(WoSensorTH)
|Bit[7] – Temperature Scale

*0: Celsius scale (°C)
*1: Fahrenheit scale (°F)
|-
|Bit[6:0] – Humidity Value
000 0000 – 110 0011: 0~99%
|}


===Broadcast Mode===

====SwitchBot Mode (Default)====
'''Advertise:'''

*Manufacturer data: include device's company ID (0x0059 Nordic) and MAC address (support WeChat protocol)
*BLE_ADVDATA_NO_NAME


'''Scanrsp:'''

*UUID: ''cba20d00-224d-11e6-9fb8-0002a5d5c51b'' and ''fee7''
*BLE_ADVDATA_NO_NAME


'''Example''':

[[https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/broadcast_1.png]][[https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/broadcast_2.png]]

<br />
====Simple Mode====
'''Advsertise:'''

*BLE_ADVDATA_NO_NAME


'''Scanrsp:'''

*UUID (cba20d00-224d-11e6-9fb8-0002a5d5c51b)
*BLE_ADVDATA_NO_NAME

<br />

====iBeacon Mode (if you need welcome function)====

'''Advertise:'''

Standard iBeacon package

*UUID:cba20d00-224d-11e6-9fb8-0002a5d5c51b
*Major ID:
*High Byte: bit 7:4 - beacon type
*Bit[3:0] - beacon data
*Low Byte: beacon data
*Minor ID:beacon data
*TX power: 0xc3
*BLE_ADVDATA_NO_NAME

{| class="wikitable"
! colspan="5" |beacon data instruction
|-
|Beacon Type
|Major ID High byte bit[3:0]
|Major ID Low byte
|Minor ID High byte
|Minor ID Low byte
|-
|0
|Alert
|MAC [3]
|MAC [4]
|MAC [5]
|}

'''Scanrsp:'''

*UUID(''cba20d00-224d-11e6-9fb8-0002a5d5c51b'')
*BLE_ADVDATA_NO_NAME


===BLE Communication Data Message Basic Format===

*The controlling terminal (short as the Terminal below) and the configured device (short as the Device below) use BLE to communicate with each other wirelessly. During the communication, the Terminal acts as a central device, while the Device acts as peripheral device. The Terminal gets basic device info by reading the broadcast message of the Device. They exchange data by using read and write characteristic of communication service.

*All bilateral communication is after the BLE connection established. The Terminal send a REQ message to the Device, and then the Device returns a RESP message.

*All communication message data length is 1-20 bytes, i.e. MTU=20. There are 2 types of REQ messages sending from the Terminal to the Device, encrypted and unencrypted.

*'''Communication service UUID:''' 
**''cba20d00-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE:Vendor UUID types start at this index (128-bit)

*'''RX characteristic UUID of the message from the Terminal to the Device:'''
**UUID: ''cba20002-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE: Vendor UUID types start at this index (128-bit)
**Char Attribute: RW
**Char Properties: notify      

*'''TX  characteristic UUID of the message from the Device to the Terminal:'''
**''cba20003-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE: Vendor UUID types start at this index (128-bit)
**Char Attribute: RW

All communication message data length is 1-20 bytes, i.e. MTU=20.

The REQ data from the Terminal to the Device is as below:
{| class="wikitable"
| colspan="3" |REQ message
|-
|Byte: 0
|Magic Number
|0x57 - (fixed value)
|-
| rowspan="3" |Byte: 1
| rowspan="3" |Header
|Bit [7:6] - Version
0 - (fixed value)
|-
|Bit [5:4] - Encryption Mode
0 - no encryption
|-
|Bit [3:0] - Command
0x01 - Execute an Action

0x02 - Get Device Basic Info

0x03 - Set Device Basic Info

0x08 - Get Device Time Management Info

0x09 - Set Device Time Management Info

0x0f – Enable command_ext in Byte: 2
|-
|Byte: 2
|Payload or command_ext depend on command
|if command == 0x0f{
command_ext :

0x08 - Set long press duration

}

else{

Payload

}
|-
|Byte: 3-16
|Payload
|Depends on Command
|}


Payload complement the data info to the Commands. The Payload format varies for different Commands.

The basic format of RESP message from the Terminal to the Device:
{| class="wikitable"
| colspan="3" |RESP message
|-
|Byte: 0
|Response status
|0x01 - OK Action executed
0x02 - ERROR Error while executing an Action

0x03 - BUSY Device is busy now, please try later

0x04 - Communication protocol version incompatible

0x05 - Device does not support this Command

0x06 - Device low battery

0x07 - Device is encrypted

0x08 - Device is unencrypted

0x09 - Password error

0x0A - Device does not support this encription method

0x0B - Failed to locate a nearby mesh Device

0x0C - Failed to connect to the network
|-
|Byte: 1-19
|Payload
|Depends on the Command to reply
|}

Status returns the handling result of the Device to the earlier command, which is the 1st Byte of every RESP message, and also mandatory.

Payload complement the data info to the Commands. The data length is 0-19 bytes depends on different cases, and can also be omitted.

The format of the Payload is up to the Command type it is responding and the handling result.

<br />
===0x01 Execute an Action===

*By sending this command, the Bot will complete a major action:
*Bot: Execute an action of moving the arm to press down and then move it back.

*If executed successfully, the Status Byte of the RESP message will be 1.

{| class="wikitable"
| colspan="3" |REQ message payload
|-
|byte: 0
|Action control
|0x0 - push and pull back
0x1 - light switch on

0x2 - light switch off

0x3 - push stop

0x4 - back
|-
| rowspan="2" |Byte: 1-2
|Byte: 1
|Time Internal (second)
|-
|Byte:2
|0x0 - push and pull back
0x1 - light switch on

0x2 - light switch off

0x3 - push stop

0x4 - back
|-
| rowspan="2" |Byte: 3-4
|Byte: 3
|Time Internal (second)
|-
|Byte: 4
|0x0 - push and pull back
0x1 - light switch on

0x2 - light switch off

0x3 - push stop

0x4 - back
|-
| rowspan="2" |Byte:5-6
|Byte: 5
|Time Internal (second)
|-
|Byte: 6
|0x0 - push and pull back
0x1 - light switch on

0x2 - light switch off

0x3 - push stop

0x4 - back
|-
| rowspan="2" |Byte: 7-8
|Byte: 7
|Time Internal (second)
|-
|Byte: 8
|0x0 - push and pull back
0x1 - light switch on

0x2 - light switch off

0x3 - push stop

0x4 - back
|-
| rowspan="2" |Byte:9-10
|Byte: 9
|Time Internal (second)
|-
|Byte: 10
|0x0 - push and pull back
0x1 - light switch on

0x2 - light switch off

0x3 - push stop

0x4 - back
|-
| rowspan="2" |Byte:11-12
|Byte: 11
|Time Internal (second)
|-
|Byte: 12
|0x0 - push and pull back
0x1 - light switch on

0x2 - light switch off

0x3 - push stop

0x4 - back
|-
| rowspan="2" |Byte:13-14
|Byte: 13
|Time Internal (second)
|-
|Byte: 14
|0x0 - push and pull back
0x1 - light switch on

0x2 - light switch off

0x3 - push stop

0x4 - back
|-
| rowspan="2" |Byte:15
|Encrypted Mode
|payload last byte, reserved
|-
|Unencrypted Mode
|Time Internal (second)
|-
|Byte: 16
Not existed for Encrypted Mode
|Unencrypted Mode
|0x0 - push and pull back
0x1 - light switch on

0x2 - light switch off

0x3 - push stop

0x4 - back
|-
|Byte: 17
|Not existed for Encrypted Mode
|This is the last byte of payload in Unencrypted Mode
|}
Note: Byte: every 2 bytes are the same group for byte 1-16. The odd byte represents the time interval of the coming action to the previous one, the even byte means the action to be executed in the action list. The time internal should not be set as 0, because the first time interval of 0 means the end of the action list. Regardless the value of Byte 17, the action list never stop.


'''Example''':

In current firmware version, Terminal send Device a message of "0x57 0x01 0x00" to control the Bot to move the arm.

The Status Byte of the RESP message will be 1 when executed successfully, and the Payload will be like:
{| class="wikitable"
| colspan="3" |RESP message payload
|-
|byte: 0
|Running Information
|Return the running information to controller for analyzing
|}
Use NRF Connect to issue command "0x570100" to the Device, the Bot arm moves.

Note:

When the Device is in the Add-on mode，the replied RESP message will be 0x0548C0, Status is 0x05 Device does not support this Command.

When the Device is not in the Add-on mode, i.e the normal press mode, the replied RESP message will be 0x01FF00, Status is 0x01 OK Action executed.<br />[[https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/Execute_1.png]][[https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/Execute_2.png]][[https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/Execute_3.png]] 

===0x02 Get Device Basic Info===

*This command is used to get the basic info of a Device:

*REQ message payload is 0.

*If executed successfully, the Status Byte of the RESP message will be 1. The payload is the Device basic info:
**This command is used to get the basic info of a SwitchBot (the Bot), remaining battery, firmware version, number of timers, act mode, hold-and-press timer, etc.


REQ message payload is 0.
{| class="wikitable"
|REQ message payload: 0
|}
If executed successfully, the Status Byte of the RESP message will be 0, payload format is as below: 
{| class="wikitable"
| colspan="3" |RESP message payload
|-
|Byte: 0
|Bat Per
|The battery  percentage
|-
|Byte: 1
|FW Ver
|Firmware  Version
|-
|Byte: 2
|NC
|
|-
|Byte: 3-4
|NC
|NC
|-
|Byte: 5-6
|NC
|NC
|-
|Byte: 7
|Timer Num
|The number of Timer
|-
|Byte: 8
|Act Mode
|The act mode  of Bot
|-
|Byte: 9
|Hold Times
|
|-
|Byte: 10
|Service data  byte 0
|
|-
|Byte: 11
|Service data  byte 1
|
|}


'''Example''':

Use NRF Connect to issue command 0x5702 to the Device and get relevant info.

Replied RESP message is 01642C64000000A10000004800

Status Byte is 0x01 OK Action executed;

RESP message payload Byte 0 is 0x64: 100% remaining battery.

RESP message payload Byte 1 is 0x2C: 44*0.1=4.4 Firmware Version;

RESP message payload Byte 2 is 0x64: 100 The strength to push button;

RESP message payload Byte 3-4 is 0x00 0x00: The ADC value read from sensor;

RESP message payload Byte 5-6 is 0x00 0xA1: The motor calibration value;

RESP message payload Byte 7 is 0x00: The number of Timer is 0;

RESP message payload Byte 8 is 0x00: The act mode of Bot: Not Add-on Mode, i.e. normal press mode;

RESP message payload Byte 9 is 0x00: Hold-and-press Times;

RESP message Payload Byte 10 is 0x48: service data Byte 0;

RESP message Payload Byte 11 is 0x00: service data Byte 1;

[[https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/get_info_1.png]]


===0x03 Set Device Basic Info===

*This command is used to set basic info of a Device:

*REQ message payload is 2.
**This command could be used to set SwitchBot act mode.

*If executed successfully, the Status Byte of the RESP message will be 1. The payload is 2:

{| class="wikitable"
| colspan="3" |REQ message payload:
|-
|Byte: 0
|NC
|The App must always give a value of 100.
|-
|Byte: 1
|act mode
|The mode of Bot.

Bit[7:4] Bot  mode:

    0 - One Bot can only press one button.

    1 - One Bot can control two state of  switch.

Bit[3:0]  Bot inverse

    0 - Act para 0x01 is push for turn on.

          Act para 0x02 is pull for turn off.

    1 - Act para 0x01 is push for turn off.

          Act para 0x02 is pull for turn on.

    Inverse won’t affect act para 0x00.

Eg.  0x10 is two state switch mode, and push for turn on, pull for turn off.
|}
If executed successfully, the Status Byte of the RESP message will be 1. The payload is 2:
{| class="wikitable"
| colspan="3" |RESP message status: 1
|-
|Byte: 0
|Status
|
|}
{| class="wikitable"
| colspan="3" |RESP message payload:
|-
|Byte: 0
|NC
|
|-
|Byte: 1
|Bot: Act mode
|The act mode  of Bot
|}

'''Example''':

Use NRF Connect to issue command 0x57036310 to a Device, set the Device as Add-on mode. If the Device (i.e. the Bot) was not in Add-on mode, the Bot arm will moves accordingly once mode set.

Replied RESP message is 0x016300;

Status Byte is 0x01 OK Action executed

Then use NRF Connect to issue a command 0x5702 to the Device and get some basic setting info.

The replied RESP message is, let's say, 01642C64000000A10000004800;

Status Byte is 0x01 OK Action executed

RESP message payload Byte 2 is 0x63: 99 The strength to push button;

RESP message payload Byte 8 is 0x10: The act mode of Bot: Add-on Mode;


[[https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/set_info_1.png]][[https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/set_info_2.png]]


===0x08 Get Device Time Management Info===

*Used to get time management info, including current time, the number of alarms, the time of the alarms.

*REQ message payload is 1 (3 Subcmd: get current time, get the number of alarms, get the alarm time).

*If executed successfully, the Status Byte of the RESP message will be1，payload is up to Subcmd type.

REQ message payload:
{| class="wikitable"
| colspan="3" |REQ message payload:
|-
|Byte: 0
|Subcmd
|Indicates what to obtain from the target Device
0x01: Device current time

0x02: the number of alarm tasks the Device has

0xn3(n=0..4): what is the Nth task
|}
If executed successfully, the Status Byte of the RESP message will be 0. For different subcmd, the Payload will be:

Subcmd=1:
{| class="wikitable"
| colspan="3" |RESP message payload:
|-
|Byte: 0-7
|Current system timestamp
|The current Unix timestamp in big endian
|}
Subcmd=2:
{| class="wikitable"
| colspan="3" |RESP message payload:
|-
|Byte: 0
|num
|Total number of tasks
|}
Subcmd=0xn3(n=0..4) what is the Nth task:
{| class="wikitable"
| colspan="3" |RESP message payload:
|-
|Byte: 0
|Num
|Total number of tasks
|-
|Byte: 1
|Index
|Task index number
|-
|Byte: 2
|Repeat
|Repeating method:
Bit [7]: repeating mode setting

1 - execute 1 time

0 - execute repeatedly

Bit [6:0]: Sun to Mon

  1 - valid for this date

  0 - invalid for this date
|-
|Byte: 3
|Time of HH
|HH
|-
|Byte: 4
|Time of MM
|MM
|-
|Byte: 5
|Action mode
|0 - Execute the job at HH:MM in a repeating method.
1 - Execute the job at HH:MM in a repeating method, and repeat Sum times at the interval of Interval.

2 - Execute the job at HH:MM in a repeating method, and repeat forever at the interval of Interval.
|-
|Byte: 6
|Job
|What to do when time is up.
0 - Action

1 - On

2 - Off
|-
|Byte: 7
|Sum
|The total times of the continuous actions executed by the timer
|-
| rowspan="3" |Byte: 8-10
| rowspan="3" |Interval: the interval between the continuous actions for the timer
|HH (Max. 5)
|-
|MM
|-
|SS (in step of 10)
|}


'''Example''':

Use NRF Connect to issue command 0x570802 to the Device, get the number of alarms.

The replied RESP message is 0x0103;

Status Byte is 0x01 OK Action executed

RESP message payload Byte 0 is 0x03: Total number of tasks;

[[https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/get_time_1.png]]


===0x09 Set Device Time Management Info===

*Used to set Device time management info, including current time, the number of alarms, the time of the alarms.
*Always set all 5 build-in timers simultaneously when setting alarm time.

*REQ message payload depends on different Subcmd (3 types of Subcmd: set current time, set the number of alarms, set time of the alarms).

*If executed successfully, the Status Byte of the RESP message will be 1, payload will be 0.

There are 3 formats for REQ message payload:

1.    
{| class="wikitable"
| colspan="3" |REQ message payload:
|-
|Byte: 0
|Subcmd
|0x01: device current time
|-
|Byte: 1-8
|Current system timestamp
|The current Unix timestamp in big endian
|}
2.   ''' '''
{| class="wikitable"
| colspan="3" |REQ message payload:
|-
|Byte: 0
|Subcmd
|0x02: the number of alarm tasks the Device has
|-
|Byte: 1
|Num
|Total number of tasks
|}
3.    
{| class="wikitable"
| colspan="3" |REQ message payload:
|-
|Byte: 0
|Subcmd
|0xn3(n=0..4): set the Nth task
|-
|Byte: 1
|Num
|Total number of tasks
|-
|Byte: 2
|Index
|Rev
|-
|Byte: 3
|Repeat
|Repeating method:

Bit [7]: repeating mode setting

1 - execute 1 time

0 - execute repeatedly

Bit [6:0]: Sun to Mon

  1 - valid for this date

  0 - invalid for this date
|-
|Byte: 4
|Time of HH
|HH
|-
|Byte: 5
|Time of MM
|MM
|-
|Byte: 6
|Action mode
|0 - Execute the job at HH:MM in a repeating method.

1 - Execute the job at HH:MM in a repeating method, and repeat Sum times at the interval of Interval.

2 - Execute the job at HH:MM in a repeating method, and repeat forever at the interval of Interval.
|-
|Byte: 7
|Job
|What to do when time is up.
0 - Action

1 - On

2 - Off
|-
|Byte: 8
|Sum
|The total times of the continuous actions executed by the timer
|-
| rowspan="3" |Byte: 9-11
| rowspan="3" |Interval: Time interval for continuous operation of a timer
|HH (Max. 5h)
|-
|MM
|-
|SS (Multiples of 10s)
|}


If executed successfully, the Status Byte of the RESP message will be 1, payload will be 0.
{| class="wikitable"
| colspan="3" |RESP message status: 1
|-
|Byte: 0
|Status
|
|}
{| class="wikitable"
|RESP message payload: 0
|}


'''Example''':

Use NRF Connect to issue command 0x57090203 to a Device, and set 3 alarms to the Device.

Replied RESP message is 0x01;

Status Byte is 0x01 OK Action executed

[[https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/set_time_1.png]]

===0x0f Extended Command===

===='''0x08 Set long press duration'''====

*To set the duration (in second) for SwitchBot arm to long press

*REQ Packet payload: 1

*If executed successfully, the Status Byte of the RESP message will be 1, payload will be 0.


REQ Packet payload: 1
{| class="wikitable"
| colspan="3" |REQ Packet payload:
|-
|Byte: 0
|Time
|
|}
If executed successfully, the Status Byte of the RESP message will be 1, payload will be 0.
{| class="wikitable"
| colspan="3" |RESP Packet status:    1
|-
|Byte: 0
|Status
|1 – OK
|}
'''Example''':

Use NRF Connect to send a command 0x570f0803 to a Device, and set the long press duration to 3 seconds.

If the received RESP packet is 0x01

Replied RESP message is 0x01, meaning a successful setting.

[[https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/set_long_press_1.png]]


CopyRight@2019 Wonderlabs, Inc.

# Color Bulb BLE open API

==='''Color Bulb Broadcast Message'''===
The following table is the Manufacture Data of ADV_IND.
{| class="wikitable"
|Byte 0
| rowspan="2" |UUID
|0x09
|UUID is 0x0969, big-endian
|-
|Byte 1
|0x69
|　
|-
|Byte 2
| rowspan="6" |MAC Address
|0x01
|Device Mac Address 0x010203040506, big-endian
|-
|Byte 3
|0x02
|　
|-
|Byte 4
|0x03
|　
|-
|Byte 5
|0x04
|　
|-
|Byte 6
|0x05
|　
|-
|Byte 7
|0x06
|　
|-
|Byte 8
|Sequence Number
|1~255
|1~255 increments, 256 overflows to 1, and the broadcast packet data is automatically incremented when there is an update
|-
| rowspan="2" |byte 9
|bit[7] power state
|0-power off, 1-power on
|　
|-
|bit[6:0] light level
|1~100%
|　
|-
| rowspan="4" |byte 10
|bit[7] is delay
|0-no delay，1-has delay
|Is there an delay
|-
|bit[6:4] Network status
|0-Wi-Fi Connecting
1-IoT Connecting

2-IoT Connected
|　
|-
|bit[3] is on state preset
|0-not preset，1-preset
|　
|-
|bit[2:0] light state
|1-white, 2-color, 3-dynamic
|　
|-
| rowspan="2" |byte 11
|bit[7] RSSI quality
|0-normal；1-Bad；
|　
|-
|bit[6:0] dynamic rate
|1-100%
|　
|-
| rowspan="2" |byte 12
|bit[7:2]  Loop Index
|
|　
|-
|bit[1:0] NC
|　
|　
|}
==='''BLE communication packet basic format'''===

*he controlling terminal (short as the Terminal below) and the configured device (short as the Device below) use BLE to communicate with each other wirelessly. During the communication, the Terminal acts as a central device, while the Device acts as peripheral device. The Terminal gets basic device info by reading the broadcast message of the Device. They exchange data by using read and write characteristic of communication service.

*All bilateral communication is after the BLE connection established. The Terminal send a REQ message to the Device, and then the Device returns a RESP message.

*All communication message data length is 1-20 bytes, i.e. MTU=20.

*'''RX characteristic UUID of the message from the Terminal to the Device:'''
**UUID: ''cba20002-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE: Vendor UUID types start at this index (128-bit)
**Char Attribute: RW
**Char Properties: notify      

*'''TX  characteristic UUID of the message from the Device to the Terminal:'''
**''cba20003-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE: Vendor UUID types start at this index (128-bit)
**Char Attribute: RW

*The control terminal sends the REQ packet format, and the specific content format of payload varies from command:

<br />
{| class="wikitable"
| colspan="3" |REQ Packet No encryption
|-
|Byte: 0
|Magic Number
|0x57 – (fixed value)
|-
| rowspan="3" |Byte: 1
| rowspan="3" |Header
|Bit [7:6] – Version
0 – (fixed value)
|-
|Bit [5:4] – reserve
|-
|Bit [3:0] – command
0x0F – [http://192.168.2.110/%E6%96%87%E4%BB%B6:///E:/%E5%8D%A7%E6%A7%BD%E7%A7%91%E6%8A%80%E8%93%9D%E7%89%99%E9%80%9A%E4%BF%A1%E5%8D%8F%E8%AE%AE%E4%B8%8E%E8%A7%84%E8%8C%83-20181227copy.docx&action=edit&redlink=1&action=edit&redlink=1 expansion command]
|-
|Byte: 2-19
|Payload
|Depends on Command
|}
==='''0x0F Expansion command'''===
====0x570F4701 set the status and color of the Bulb====

*

{| class="wikitable"
|+
!REQ Packet Payload
!Turn On Bulb
!Turn Off Bulb
!TOGGLE
!Change Lvl + RGB
!Change Lvl + CW
!Change Lvl
!Change RGB
!Change  CW
|-
|Byte 0
| colspan="8" |0x47
|-
|Byte 1
| colspan="8" |0x01
|-
|Byte 2
|0x01
|0x02
|0x03
|0x12
|0x13
|0x14
|0x16
|0x17
|-
|Byte 3
|
|
|
|Lvl           (0 - 100)
|Lvl               (0 - 100)
|Lvl               (0 - 100)
|Lvl           (0 - 100)
| rowspan="2" |C/W (2700K - 6500K)
|-
|Byte 4
|
|
|
|R             (0 - 255)
| rowspan="2" |
|
|R             (0 - 255)
|-
|Byte 5
|
|
|
|G             (0 - 255)
|
|G             (0 - 255)
|
|-
|Byte 6
|
|
|
|B             (0 - 255)
|
|
|B             (0 - 255)
|
|}<br />
{| class="wikitable"
!RESP Packet
!
|-
|Byte 0
|0x01
|-
|Byte 1
|bit[7]:  power status    0 OFF  1 ON
bit[6]:  light status       0 no preset
|-
|Byte 2
|Lvl (0 - 100)
|-
|Byte 3
|R   (0 - 255)
|-
|Byte 4
|G   (0 - 255)
|-
|Byte 5
|B   (0 - 255)
|-
|Byte 6
| rowspan="2" |C/W  （2700K- 6500K）
|-
|Byte 7
|-
|Byte 8
|0xFF no care
0x01 white

0x02 color

0x03 dyanmic

0x04 dynamic group

0x06 music
|-
|Byte 9
|0xFF  no care
other  preset idx
|-
|Byte 10
|current mode
0x01 white

0x02 color

0x03 dyanmic
|}
'''Example:'''

*Turn On Bulb                                                  REQ       0x570f470101                       RESP       0x018032FF00000000FFFF02
*Turn Off Bulb                                                  REQ       0x570f470102                       RESP       0x010032FF00000000FFFF02
*Set Bulb into a blue 50% brightness              REQ       0x570f470112320000ff         RESP       0x0180320000FF0000FFFF02
*Adjust the brightness of Bulb                        REQ       0x570f47011420                    RESP       0x0180200000FF0000FFFF02

====0x570F4801 read the status of Bulb====
The payload length of the request packet is 0.

The response will return the current Bulb status.
{| class="wikitable"
| colspan="2" |RESP Packet payload
|-
|Byte: 0
|0x01
|-
|Byte: 1
|power and light status
|-
|Byte: 2
|bulb brightness
|-
|Byte: 3
|bulb R
|-
|Byte: 4
|bulb G
|-
|Byte: 5
|bulb B
|-
|Byte: 6-7
|bulb temperature
|-
|Byte: 5-10
|bulb mode
|}
'''Example:'''

*Send 0x570F4801 query Bulb status     RESP 0180200000FF0000FFFF02

# Contact Sensor BLE open API

===Contact Sensor Broadcast Message===

*The following table is the Service Data of SCAN_RSP.

{| class="wikitable"
| rowspan="2" |Byte: 0
| rowspan="2" |Enctype
Device type
|Bit[7]:NC
0: No encryption 1: With encryption, encryption method 1 
|-
|Bit[6:0]-Device Type
'D' : Pair Mode

'd' : Const Adv Mode
|-
| rowspan="3" |Byte: 1
| rowspan="3" |Status
|Bit[7]: Has it been scope tested
1: Tested

0: Not tested
|-
|Bit[6]: PIR State
0: No one moves

1: Someone is moving
|-
|Bit[5:0] NC
|-
| rowspan="2" |Byte: 2
| rowspan="2" |update utc
Battery
|Bit[7]:NC
|-
|Bit[6:0]: battery capacity
|-
| rowspan="5" |Byte: 3
| rowspan="5" |Sensor Data
|Bit[7]: Since the last trigger
PIR time（s）Highest bit (*65536)
|-
|Bit[6]: Since the last trigger
HAL time（s）Highest bit(*65536)
|-
|Bit[3:5]:reserve
|-
|Bit[1:2]:Hal State
0:door close

1:door open

2:timeout not close
|-
|Bit[0]:Light Level
0:dark

1:light
|-
|Byte: 4~Byte: 5
|PIR UTC
|Time from the last PIR trigger  （s）low 16 Bit
Byte4 High 8 Bit

Byte5 Low 8 Bit
|-
|Byte: 6~Byte: 7
|HAL UTC
|Time from the last HAL trigger  （s）low 16 Bit
Byte6 High 8 Bit

Byte7 Low 8 Bit
|-
| rowspan="3" |Byte: 8
| rowspan="3" |Act Counter
|Bit[6:7]: Number of entrances
The number of door entry actions, the Counter value is increased by 1 

(1~3)cycle, 3 Value changes after overflow 1
|-
|Bit[4:5]: Number of Go out Counter
The number of times to go out, the Counter value is increased by 1 

(1~3)cycle, 3 Value changes after overflow 1
|-
|Bit[0:3]:Button push counter
Each time the button is pressed, the Counter value increases by 1 

(1~15)cycle,16 Value changes after overflow 1
|}
'''Example''':

[[https://switchbot-open-api.s3.amazonaws.com/Contact+Sensor+open+api/boardcast_1.png]]

===BLE communication packet basic format===

*The controlling terminal (short as the Terminal below) and the configured device (short as the Device below) use BLE to communicate with each other wirelessly. During the communication, the Terminal acts as a central device, while the Device acts as peripheral device. The Terminal gets basic device info by reading the broadcast message of the Device. They exchange data by using read and write characteristic of communication service.

*All bilateral communication is after the BLE connection established. The Terminal send a REQ message to the Device, and then the Device returns a RESP message.

*All communication message data length is 1-20 bytes, i.e. MTU=20.

*'''RX characteristic UUID of the message from the Terminal to the Device:'''
**UUID: ''cba20002-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE: Vendor UUID types start at this index (128-bit)
**Char Attribute: RW
**Char Properties: notify      

*'''TX  characteristic UUID of the message from the Device to the Terminal:'''
**''cba20003-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE: Vendor UUID types start at this index (128-bit)
**Char Attribute: RW

*The control terminal sends the REQ packet format, and the specific content format of PayLoad varies from Command:

{| class="wikitable"
| colspan="3" |REQ Packet No encryption
|-
|Byte: 0
|Magic Number
|0x57 – (fixed value)
|-
| rowspan="3" |Byte: 1
| rowspan="3" |Header
|Bit [7:6] – Version
  0 – (fixed value)
|-
|Bit [5:4] – Encryption Mode
0 – no encryption
|-
|Bit [3:0] – command
0x00 – General command

0x02 – Get device basic info
|-
|Byte: 2-19
|Payload
|Depends on Command
|}

*Contact Sensor Returns the RESP packet format of the control terminal as follows:

{| class="wikitable"
|-
| colspan="3" |RESP message
|-
|Byte: 0
|Response status
|0x01 - OK Action executed
0x02 - ERROR Error while executing an Action

0x03 - BUSY Device is busy now, please try later

0x04 - Communication protocol version incompatible

0x05 - Device does not support this Command

0x06 - Device low battery

0x07 - Device is encrypted

0x08 - Device is unencrypted

0x09 - Password error

0x0A - Device does not support this encription method

0x0B - Failed to locate a nearby mesh Device

0x0C - Failed to connect to the network

0x0D - This command is not supported in the current mode

0x0E - Disconnected from the device that needs to stay connected
|-
|Byte: 1-19
|Payload
|Depends on the Command to reply
|}
Note:

1、Status returns the processing status of the previous command, which is the first BYTE of each RESP packet, which is also necessary.

2、PayLoad's specific content format depends on Command and its processing results, the data length ranges from 0-19 bytes, that is, no.

===0x00 General Command===

====0x11 Get device status data====

*Obtain the status information of the Contact Sensor device;
*The format of the command sent by the terminal to the device is as follows:

{| class="wikitable"
| colspan="3" |REQ Packet CMD Format
|-
|Index
|Section
|Value
|-
|Byte: 0
|Magic num
|0x57
|-
|Byte: 1
|General cmd
|0x00
|-
|Byte: 2
|Subcmd
|0x11
|}

*After successful execution, the Status byte of the RESP packet is 1, the Payload is 8 (index), and the format of the RESP Packet payload is as follows:

{| class="wikitable"
| colspan="3" |RESP Packet
|-
|Byte: 0
|Status
|Command execution status
|-
|Byte: 1
| rowspan="4" |Payload
|rule code
When the parsing rule is 0x01 
|-
|Byte: 2~Byte: 5
|The number of seconds from the current HAL last trigger, door close trigger or door open trigger (normal mode) 
Device local UTC (test mode) 
|-
|Byte: 6
|Pir Status
0：unmanned

1：someone
|-
|Byte: 7
|Light Level
0: dark

1: bright
|-
|Byte: 8
|
|Door Status
0：Close

1：Open

2：Timeout not close
|}
'''Example''':

Use NRF Connect to issue command 0x570011 to the Device, Get device basic information.

The replied RESP message is 0x010100000DBB010100;

Status Byte is 0x01 OK Action executed.

RESP message payload Byte 0: 0x01, Command execution status.

RESP message payload Byte 1: 0x01, rule code.

RESP message payload Byte 2~Byte5: 0x00000DBB, utc timestamp (big endian).

RESP message payload Byte 6: 0x01, Pir Status.

RESP message payload Byte 7: 0x01, Light Level.

RESP message payload Byte 8: 0x00, Door Status.

[[https://switchbot-open-api.s3.amazonaws.com/Contact+Sensor+open+api/570011.png]]

===0x02 Get device basic information command===

*This command is used to obtain the basic information of the device:
*The Status byte of the RESP packet after successful execution is 1, and the Payload is the basic information of the device:
*This command is used to obtain the basic information of the Contact Sensor device (battery power, firmware version, Hall enable status, PIR detection distance, cloud service switch, door sensor installation position, PIR working mode, light effect switch, door access mode, timeout Unclosed time, whether it is in the adding process, environmental threshold state);
*The payload is 0 (index), and the format of the REQ Packet payload is as follows:

{| class="wikitable"
|REQ Packet payload: 0
|}

*After successful execution, the Status byte of the RESP packet is 1, the Payload is 6 (index), and the format of the RESP Packet payload is as follows:

{| class="wikitable"
| colspan="3" |RESP Packet payload
|-
|Byte: 0
|Bat Per
|The battery percentage
|-
|Byte: 1
|FW Ver
|Firmware Version
|-
|Byte: 2
|Para Info
|bit[7-6]:Active Hal
0-reserve

1-Active left

2-Active right

3-Active both

bit[5]:detect range

0-short

1-long

bit[4]:

0-disable iot

1-enable iot

bit[2:3] Installation location

0-door

1-wall

2-window

Bit[1]:PIR Work Mode

0-Normal Mode

1-Test Mode

Bit[0]:

0-Disable LED

1-Enable LED
|-
|Byte: 3
|Total Act Num
|Total Act Num
|-
|Byte: 4
|Go-Out Mode
|bit[7]:
0-Press Mode

1-Auto Detec Mode

bit[6~0]:

Detection Time
|-
|Byte: 5
|Timeout Close Time
|Door and window open time overtime （min)
|-
|Byte: 6
|Add process 
Environment dark threshold setting 
|Bit[2:7]
reserve

Bit[1]: Work flow identification

0-Normal work flow

1-Add process (use for adding process)

Bit[0]

Ambient dark threshold state 

0-Can't set current status

1-Can set current status
|}
'''Example''':

Use NRF Connect to issue command 0x5702 to the Device, Get device basic information.

The replied RESP message is 0x01640B2101030501;

Status Byte is 0x01 OK Action executed.

RESP message payload Byte 0: 0x00, The battery percentage.

RESP message payload Byte 1: 0x10, Firmware Version.

RESP message payload Byte 2: 0x00, Para Info.

RESP message payload Byte 3: 0x10, Total Act Num.

RESP message payload Byte 4: 0x00, Go-Out Mode.

RESP message payload Byte 5: 0x10, Timeout Close Time.

RESP message payload Byte 6: 0x00, Add process and Environment dark threshold setting.

[[https://switchbot-open-api.s3.amazonaws.com/Contact+Sensor+open+api/5702.png]]

# Curtain BLE open API

==Broadcast package==

*The following table is the Service Data of SCAN_RSP.

{| class="wikitable"
| colspan="3" |Service data
|-
| rowspan="2" |Byte: 0
|Enc type
|Bit[7] NC
|-
|Dev Type
|Bit [6:0] – Device Type
‘c’Const Adv Mode

‘C’Pair Mode
|-
| rowspan="3" |Byte: 1
| rowspan="3" |Status
|Bit [7] – Whether to allow connection
1- allowed, 0- not allowed
|-
|Bit [6] – Calibration situation
1-calibrated, 0-not calibrated, need to be calibrated
|-
|Bit [5-0] NC
|-
| rowspan="2" |Byte: 2
|Update utc flag
|Bit[7] NC
|-
|Battery
|Bit [6:0] – Battery capacity 0~100%
|-
| rowspan="2" |Byte: 3
|MotionState
|bit [7] - Movement status, 0-stationary, 1-movement
|-
|Position
|Bit [6:0] - The current position of the device (%)
|-
| rowspan="2" |Byte: 4
|LightLevel
|bit [7:4] - Light level (1-10)
|-
|DeviceChain
|bit [3:0] - Device chain length
|}

[[https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/boardcast_1.png]]


==Basic format of BLE communication data packet==
1. UUID related to communication.

*'''Communication service UUID :'''

''cba20d00-224d-11e6-9fb8-0002a5d5c51b''

UUID TYPE：Vendor UUID types start at this index (128-bit)

*'''RX characteristic UUID of the terminal sending data to the device :'''

''cba20002-224d-11e6-9fb8-0002a5d5c51b''

UUID TYPE ：Vendor UUID types start at this index (128-bit)

Char Attribute ：RW

Char Properties： notify      

*'''TX characteristic UUID of the device sending data to the terminal :'''

''cba20003-224d-11e6-9fb8-0002a5d5c51b''

UUID TYPE ：Vendor UUID types start at this index (128-bit)

Char Attribute ：RW

2. All two-way communication occurs after the BLE connection is established. The communication form is that the terminal first sends a REQ request packet to the device, and the device returns an RSP packet.

The terminal first sends the REQ request packet to the device. The basic format is as follows:
{| class="wikitable"
| colspan="3" |REQ Packet
|-
|Byte: 0
|Magic Number
|0x57 – (fixed value)
|-
| rowspan="3" |Byte: 1
| rowspan="3" |Header
|Bit [7:6] – Version
  0 – (fixed value)
|-
|Bit [5:4] – Encryption Mode
0 – no encryption
|-
|Bit [3:0] – command
0x02 – Get basic device information

0x0F – extended command
|-
|Byte: 2-19
|Payload
|Depends on Command
|}The basic format of the RSP packet returned by the device to the terminal is as follows:
{| class="wikitable"
| colspan="3" |RSP Packet
|-
|Byte: 0
|Response status
|0x01 - OK, Action executed
0x02 - ERROR, Error while executing an Action

0x03 - BUSY, Device is busy now, please try later

0x04 - Communication protocol version incompatible

0x05 - Device does not support this Command

0x06 - Device is low power

0x0D - This command is not supported in the current mode

0x0E - Disconnected from the device that needs to stay connected
|-
|Byte: 1-19
|Payload
|Depends on the Command to reply
|}<br />
=='''0x02 Get the basic information of the device command'''==

*Used to obtain basic information about the device.

*REQ Packet Payload is 0.

*The Status byte of the RSP packet after successful execution is 0x01, and the Payload is:

{| class="wikitable"
| colspan="3" |RSP Packet Payload
|-
|Byte: 0
|Bat Per
|The battery percentage
|-
|Byte: 1
|FW Ver
|Firmware Version
|-
|Byte: 2
|Chain Length
|Device Chain Length
|-
|Byte: 3
|State 1
|bit7-direction, 0: default (open to the left), 1: reverse
bit6-touch and go, 0: disable, 1: enable

bit5-lighting effect, 0: disable, 1: enable

bit4-reserved

bit3-fault, 0-none, 1-faulty

bit2:0-reserved
|-
|Byte: 4
|State 2
|bit3-Whether solar panel is plugged in, 0-No, 1-There is solar panel
bit2-calibrated, 0-not calibrated, 1-calibrated

bit1:0-motion status, 0-static, 1-open window, 2-close window
|-
|Byte: 5
|Position
|Current location of the device (%)
|-
|Byte: 6
|Timer Amount
|Number of timers
|}

[[https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/5702_1.png]]

==0x0F extended command==
==='''0x45 Curtain setting command'''===

*Set Curtain's unique setting content.

{| class="wikitable"
|Function code
|Function parameter code
| colspan="2" |REQ Packet Payload
|-
|Byte: 0
|Byte: 1
|Byte: 2
|Byte: 3
|-
|0x01-Action
|0x05-unified action of device chain
|0xFF
|The distance between the final position of the device and the origin (%)
|-
| rowspan="2" |0x03-Light sensor
|0x02-device chain light trigger source
|The serial number of the device where the photosensitive sensor is located (device identification B)
|0x0-Motherboard
0x01-Solar Panel
|-
|0x03-delete all light sensing actions
|
|
|}

*REQ Packet不同命令返回值不同。

{| class="wikitable"
| colspan="3" |REQ Packet
| colspan="3" |RSP Packet
|-
|Function code
|Function parameter code
|Payload Byte: 0
|Byte: 0
|Byte: 1
|Byte: 2
|-
|0x01-Action
| -
| -
|Command
Status
|Device 0 current percentage position
|Current percentage position of device 1
|}

[[https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/570f450105ff32.png]]

==='''0x46 Curtain get command'''===

*Get Curtain's unique setting content.
*Action mode A: 0-performance mode, 1-silent mode, other-invalid value, the default invalid value is 1 for all bit values.

*Charging status A:
**0:Not charging,
**1:Adapter charging,
**2:Solar panel charging,
**3:The adapter connection is full,
**4:The solar panel connection is full,
**5:The solar panel is connected and not charged when it is not fully charged.
**6:Hardware error.

{| class="wikitable"
|Function code
|Function parameter code
| colspan="2" |REQ Packet Payload
| colspan="8" |RSP Packet
|-
|Byte: 0
|Byte: 1
|Byte: 2
|Byte: 3
|Byte: 0
|Byte: 1
|Byte: 2
|Byte: 3
|Byte: 4
|Byte: 5
|Byte: 6
|Byte: 7
|-
| rowspan="2" |0x04-Basic attributes
|0x01-Summary information
|
|
| rowspan="3" |Command
Status
|Device 0
bit7-direction, 0: default, 1: reverse

bit6-touch and go, 0: disable, 1: enable

bit5-light sensor, 0: disable, 1: enable

bit4-reserved

bit3-window mode, 0-window to the left, 1-window to the right bit2:0-reserved
|Device 1
bit7-direction, 0: default (open to the left), 1: reverse

bit6-touch and go, 0: disable, 1: enable

bit5-light sensor 0: disable, 1: enable

bit4-reserved

bit3-window mode, 0-open window to the left, 1-open window to the right

bit2:0-reserved
|
|
|
|
|
|-
|0x02-Advanced page
|
|
|Device 0
battery power
|Device 0
Firmware version
|Device 0
State of charge A
|Device 1
battery power
|Device 1
Firmware version
|Device 1
State of charge A
|
|-
|0x81-Status command
|0x01-Query the basic status of the device chain
|
|
|bit7-reserved
bit6-delay action 0-none, 1-yes

bit5:4-device chain head movement status, 0-static, 1-open window, 2-close window,

bit3:0-the number of light sensing actions
|bit7:4-device chain head action mode A
bit3:0-the number of device chain head timers
|Device chain length
|bit7- whether to insert solar panel, 0-no, 1- insert solar panel;
bit6:0-sequence number 0 device current location
|bit7-whether to charge, 0-no, 1-charging;
bit6:0-serial number 0 device current battery power
|bit7- whether to insert solar panel, 0-no, 1- insert solar panel;
bit6:0-sequence number 1 current location of the device
|bit7-whether to charge, 0-no, 1-charging;
bit6:0-sequence number 1 current battery power of the device
|}
[[https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/570f460401.png]]

[[https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/570f460402.png]]

[[https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/570f468101.png]]

# LED Strip Light BLE open API

<br />
==='''LED Strip Light Broadcast Message'''===
The following table is the Manufacture Data of ADV_IND.
{| class="wikitable"
|Byte 0
| rowspan="2" |UUID
|0x09
|UUID is 0x0969, big-endian
|-
|Byte 1
|0x69
|　
|-
|Byte 2
| rowspan="6" |MAC Address
|0x01
|Device Mac Address 0x010203040506, big-endian
|-
|Byte 3
|0x02
|　
|-
|Byte 4
|0x03
|　
|-
|Byte 5
|0x04
|　
|-
|Byte 6
|0x05
|　
|-
|Byte 7
|0x06
|　
|-
|Byte 8
|Sequence Number
|1~255
|1~255 increments, 256 overflows to 1, and the broadcast packet data is automatically incremented when there is an update
|-
| rowspan="2" |Byte 9
|bit[7] power state
|0-power off, 1-power on
|　
|-
|bit[6:0] light level
|1~100%
|　
|-
| rowspan="3" |Byte 10
|bit[7] is delay
|0-no delay，1-has delay
|Is there an delay
|-
|bit[6:4] 联网状态
|0-Wi-Fi Connecting
1-IoT Connecting

2-IoT Connected
|　
|-
|bit[3:0] ON state mode
|2-color, 3-scene ,4-music ,5-controller
|　
|-
|Byte 11
|bit[7:6]: R0
bit[5:4]: G0

bit[3:2]: B0

bit[1:0]: R1
|If the value is R: G: B = 0:0:0

Then the color does not exist

Namely: the number is less than 8
|
|-
|Byte 12
|bit[7:6]: G1
bit[5:4]: B1

bit[3:2]: R2

bit[1:0]: G2
|
|
|-
|Byte 13
|bit[7:6]: B2
bit[5:4]: R3

bit[3:2]: G3

bit[1:0]: B3
|
|
|-
|Byte 14
|bit[7:6]: R4
bit[5:4]: G4

bit[3:2]: B4

bit[1:0]: R5
|
|
|-
|Byte 15
|bit[7:6]: G5
bit[5:4]: B5

bit[3:2]: R6

bit[1:0]: G6
|
|
|-
|Byte 16
|bit[7:6]: B6
bit[5:4]: R7

bit[3:2]: G7

bit[1:0]: B7
|
|
|-
|Byte 17
|Latest fault code
|0 - No fault code 
|
|}
==='''BLE communication packet basic format'''===

*he controlling terminal (short as the Terminal below) and the configured device (short as the Device below) use BLE to communicate with each other wirelessly. During the communication, the Terminal acts as a central device, while the Device acts as peripheral device. The Terminal gets basic device info by reading the broadcast message of the Device. They exchange data by using read and write characteristic of communication service.

*All bilateral communication is after the BLE connection established. The Terminal send a REQ message to the Device, and then the Device returns a RESP message.

*All communication message data length is 1-20 bytes, i.e. MTU=20.

*'''RX characteristic UUID of the message from the Terminal to the Device:'''
**UUID: ''cba20002-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE: Vendor UUID types start at this index (128-bit)
**Char Attribute: RW
**Char Properties: notify      

*'''TX  characteristic UUID of the message from the Device to the Terminal:'''
**''cba20003-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE: Vendor UUID types start at this index (128-bit)
**Char Attribute: RW

*The control terminal sends the REQ packet format, and the specific content format of payload varies from command:

<br />
{| class="wikitable"
| colspan="3" |REQ Packet No encryption
|-
|Byte: 0
|Magic Number
|0x57 – (fixed value)
|-
| rowspan="3" |Byte: 1
| rowspan="3" |Header
|Bit [7:6] – Version
0 – (fixed value)
|-
|Bit [5:4] – reserve
|-
|Bit [3:0] – command
0x0F – [http://192.168.2.110/%E6%96%87%E4%BB%B6:///E:/%E5%8D%A7%E6%A7%BD%E7%A7%91%E6%8A%80%E8%93%9D%E7%89%99%E9%80%9A%E4%BF%A1%E5%8D%8F%E8%AE%AE%E4%B8%8E%E8%A7%84%E8%8C%83-20181227copy.docx&action=edit&redlink=1&action=edit&redlink=1 expansion command]
|-
|Byte: 2-19
|Payload
|Depends on Command
|}
==='''0x0F Expansion command'''===
====0x570f4901 set the status and color of the LED Strip Light====

*

{| class="wikitable"
|+REQ  Packet payload
!REQ Packet
!Turn On Strip Light
!Turn Off Strip Light
!TOGGLE
!Change Lvl + RGB
!Change Lvl
!Change RGB
|-
|Byte 0
| colspan="6" |0x49
|-
|Byte 1
| colspan="6" |0x01
|-
|Byte 2
|0x01
|0x02
|0x03
|0x12
|0x14
|0x16
|-
|Byte 3
|
|
|
|Lvl           (0 - 100)
|Lvl               (0 - 100)
|R             (0 - 255)
|-
|Byte 4
|
|
|
|R             (0 - 255)
|
|G             (0 - 255)
|-
|Byte 5
|
|
|
|G             (0 - 255)
|
|B             (0 - 255)
|-
|Byte 6
|
|
|
|B             (0 - 255)
|
|
|}<br />
{| class="wikitable"
!RESP Packet
!
|-
|Byte 0
|0x01
|-
|Byte 1
|bit[7]:  power status
0-OFF  1-ON
|-
|Byte 2
|Lvl (0 - 100)
|-
|Byte 3
|R   (0 - 255)
|-
|Byte 4
|G   (0 - 255)
|-
|Byte 5
|B   (0 - 255)
|-
|Byte 6
| rowspan="3" |Reserve
|-
|Byte 7
|-
|Byte 8
|-
|Byte 9
|0xFF  no care
other  preset idx
|-
|Byte 10
|current mode
0x02 - color

0x03 - scene

0x04 - music
|}'''Example:'''

*Turn On LED Strip Light                                                  REQ       0x570f490101                      RESP       0x018032FF00000000FFFF02
*Turn Off LED Strip Light                                                  REQ       0x570f490102                      RESP       0x010032FF00000000FFFF02
*Set LED Strip Light into a blue 50% brightness              REQ       0x570f490112320000ff        RESP       0x0180320000FF0000FFFF02
*Adjust the brightness of LED Strip Light                         REQ       0x570f49011420                  RESP       0x0180200000FF0000FFFF02

====0x570F4A01 read the status of LED Strip Light====
The payload length of the request packet is 0.

The response will return the current LED Strip Light status.
{| class="wikitable"
| colspan="2" |RESP Packet payload
|-
|Byte: 0
|0x01
|-
|Byte: 1
|bit[7]:  power status
0-OFF  1-ON
|-
|Byte: 2
|LED Strip Light brightness
|-
|Byte: 3
|LED Strip Light R
|-
|Byte: 4
|LED Strip Light G
|-
|Byte: 5
|LED Strip Light B
|-
|Byte: 6-8
|Reserve
|-
|Byte: 9
|0xFF  no care
other  preset idx
|-
|Byte: 10
|current mode
0x02 - color

0x03 - scene

0x04 - music
|}'''Example:'''

*Send 0x570f4A01 query LED Strip Light status     RESP 0180200000FF0000FFFF02

# Meter BLE open API

===Broadcast Message===

*This broadcast message defines the Service data of Scan Rsp of the specific Device.

*The length of the Service data is different based on Device Type. The Service data can be 8 bytes max. The Byte: 0, Byte: 1 and Byte: 2 are for every Device Type. The bytes start from Byte: 3 depends on different Device Type. Please refer to Device Type definition in the table below.
*Broadcast modes are defined as:
**SwitchBot Mode (Default)
**Simple Mode
**iBeacon Mode

{| class="wikitable"
|+Device Type
!Product Name
!HEX
!ASCII
!Note
|-
|SwitchBot Bot (WoHand)
|0x48
|'H'
|
|-
|WoButton
|0x42
|'B'
|
|-
| rowspan="2" |SwitchBot Hub (WoLink)
|0x4C
|'L'
|Add Mode
|-
|0x6C
|'l'
|Normal
|-
| rowspan="2" |SwitchBot Hub Plus (WoLink Plus)
|0x50
|'P'
|Add Mode
|-
|0x70
|'p'
|Normal Mode
|-
| rowspan="2" |SwitchBot Fan (WoFan)
|0x46
|'F'
|Add Mode
|-
|0x66
|'f'
|Normal Mode
|-
| rowspan="2" |SwitchBot MeterTH (WoSensorTH)
|0x74
|'t'
|Add Mode
|-
|0x54
|'T'
|Normal Mode
|-
| rowspan="2" |SwitchBot Mini (HubMini)
|0x4D
|'M'
|Add Mode
|-
|0x6D
|'m'
|Normal Mode
|}


===Broadcast Message Format===

====(Old) Broadcast Message====
Use this for firmware version before Bot v30, Remote v20 and Hub v6.

'''Advertise:'''

*Manufacturer data: includes device's company ID and MAC address (support WeChat protocol)


'''Scanrsp:'''

*Device Name: "Bot", "Remote", "Hub", "WoSensorTH"
*UUID: ''cba20d00-224d-11e6-9fb8-0002a5d5c51b'' and ''fee7''

<br />
====(New) Broadcast Message====
The length of the Service data is different based on Device Type. The Service data can be 8 bytes max. 

The bit[6:0] in Byte: 0 of the broadcast message is Device Type. The Byte: 0, Byte: 1 and Byte: 2 are for every Device Type. The bytes start from Byte: 3 depends on different Device Type, serving as general registers.

The backend also needs a mapping table of the Service data, so both the backend and app can parse the Service data.

{| class="wikitable"
| colspan="3" |Service data
|-
| rowspan="2" |Byte: 0
| rowspan="2" |Enc Type

Dev Type
|Bit[7] - Reserved
|-
|Bit[6:0] - Device Type
Please refer to the Device Type table above.
|-
| rowspan="2" |Byte: 1
| rowspan="2" |Status
|Bit[7:4] - Reserved
|-
|Bit[3] – Group D

Bit[2] – Group C

Bit[1] – Group B

Bit[0] – Group A

*0: the device is not in this group
*1: the device is in this group
|-
| rowspan="2" |Byte: 2
| rowspan="2" |Update UTC Flag

Battery
|Bit[7] – Reserved
|-
|Bit[6:0] - Remaining Battery 0~100%
|-
| rowspan="3" |Byte: 3
| rowspan="3" |alarm flag

the fraction part of temperatrue value

(WoSensorTH)
|Bit[7:6] – Temperature Alert Status

*0: no alert
*1: low temp alert (temperature lower than the low limit)
*2: high temp alert (temperature higher than the high limit)
*3: temp alert (temperature higher than than the low limit and lower than the high limit)
|-
|Bit[5:4] – Humidity Alert Status

*0: no alert
*1: low humidity alert (humidity lower than the low limit)
*2: high humidity alert (humidity higher than the high limit)
*3: temp humidity (humidity higher than than the low limit and lower than the high limit)
|-
|Bit[3:0] – Decimals of the Temperature

*0000 – 1001:0~9
|-
| rowspan="2" |Byte: 4
| rowspan="2" |temperature mode
Positive/Negative temperature flag

the integer part of temperature value

(WoSensorTH)
|Bit[7] – Positive/Negative temperature flag

*0: subzero temperature
*1: temperature above zero
|-
|Bit[6:0] – Integers of the Temperature
000 0000 – 111 1111: 0~127 °C

Celsius to Fahrenheit: F=(C*1.8)+32
|-
| rowspan="2" |Byte: 5
| rowspan="2" |humidity value
(WoSensorTH)
|Bit[7] – Temperature Scale

*0: Celsius scale (°C)
*1: Fahrenheit scale (°F)
|-
|Bit[6:0] – Humidity Value
000 0000 – 110 0011: 0~99%
|}


===Broadcast Mode===

====SwitchBot Mode (Default)====
'''Advertise:'''

*Manufacturer data: include device's company ID (0x0059 Nordic) and MAC address (support WeChat protocol)
*BLE_ADVDATA_NO_NAME


'''Scanrsp:'''

*UUID: ''cba20d00-224d-11e6-9fb8-0002a5d5c51b'' and ''fee7''
*BLE_ADVDATA_NO_NAME


'''Example''':

[[https://switchbot-open-api.s3.amazonaws.com/Meter+open+api/broadcast_1.png]][[https://switchbot-open-api.s3.amazonaws.com/Meter+open+api/broadcast_2.png]]

<br />
====Simple Mode====
'''Advsertise:'''

*BLE_ADVDATA_NO_NAME


'''Scanrsp:'''

*UUID (cba20d00-224d-11e6-9fb8-0002a5d5c51b)
*BLE_ADVDATA_NO_NAME

<br />

====iBeacon Mode (if you need welcome function)====

'''Advertise:'''

Standard iBeacon package

*UUID:cba20d00-224d-11e6-9fb8-0002a5d5c51b
*Major ID:
*High Byte: bit 7:4 - beacon type
*Bit[3:0] - beacon data
*Low Byte: beacon data
*Minor ID:beacon data
*TX power: 0xc3
*BLE_ADVDATA_NO_NAME

{| class="wikitable"
! colspan="5" |beacon data instruction
|-
|Beacon Type
|Major ID High byte bit[3:0]
|Major ID Low byte
|Minor ID High byte
|Minor ID Low byte
|-
|0
|Alert
|MAC [3]
|MAC [4]
|MAC [5]
|}


'''Scanrsp:'''

*UUID(''cba20d00-224d-11e6-9fb8-0002a5d5c51b'')
*BLE_ADVDATA_NO_NAME


===BLE Communication Data Message Basic Format===

*The controlling terminal (short as the Terminal below) and the configured device (short as the Device below) use BLE to communicate with each other wirelessly. During the communication, the Terminal acts as a central device, while the Device acts as peripheral device. The Terminal gets basic device info by reading the broadcast message of the Device. They exchange data by using read and write characteristic of communication service.

*All bilateral communication is after the BLE connection established. The Terminal send a REQ message to the Device, and then the Device returns a RESP message.

*All communication message data length is 1-20 bytes, i.e. MTU=20. There are 2 types of REQ messages sending from the Terminal to the Device, encrypted and unencrypted.

*'''Communication service UUID:'''
**''cba20d00-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE:Vendor UUID types start at this index (128-bit)

*'''RX characteristic UUID of the message from the Terminal to the Device:'''
**UUID: ''cba20002-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE :Vendor UUID types start at this index (128-bit)
**Char Attribute :RW
**Char Properties: notify      

*'''TX  characteristic UUID of the message from the Device to the Terminal:'''
**''cba20003-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE :Vendor UUID types start at this index (128-bit)
**Char Attribute :RW

All communication message data length is 1-20 bytes, i.e. MTU=20. The REQ data from the Terminal to the Device has encrypted and unencrypted format, as below:
<br />
{| class="wikitable"
| colspan="3" |REQ Packet (Unencrpted)
|-
|Byte: 0
|Magic Number
|0x57 – (fixed value)
|-
| rowspan="3" |Byte: 1
| rowspan="3" |Header
|Bit[7:6] – Version
0 – (fixed value)
|-
|Bit[5:4] – Reserved
|-
|Bit[3:0] – command
0x02 – Get Device Basic Info

0x0F – Expand Command
|-
|Byte: 2-19
|Payload
|Depends on Command
|}
Payload complement the data info to the Commands. The Payload format varies for different Commands.

The basic format of RESP message from the Terminal to the Device:
{| class="wikitable"
| colspan="3" |RESP Packet
|-
|Byte: 0
|Response status
|0x01 - OK Action executed
0x02 - ERROR Error while executing an Action

0x03 - BUSY Device is busy now, please try later

0x04 - Communication protocol version incompatible

0x05 - Device does not support this Command

0x06 - Device low battery

0x07 - Device is encrypted

0x08 - Device is unencrypted

0x09 - Password error

0x0A - Device does not support this encription method

0x0B - Failed to locate a nearby mesh device

0x0C - Failed to connect to the network
|-
|Byte: 1-19
|Payload
|Depends on the Command to reply
|}
Status returns the handling result of the Device to the earlier command, which is the 1st Byte of every RESP message, and also mandatory.

Payload complement the data info to the Commands. The data length is 0-19 bytes depends on different cases, and can also be omitted.

The format of the Payload is up to the Command type it is responding and the handling result.

<br />
===0x02 Get Device Basic Info===

*This command is used to get the basic info of a Device:

*REQ message payload is 0.

*If executed successfully, the Status Byte of the RESP message will be 1. The payload is the device basic info:
**This command is used to get the basic info of a Meter - remaining battery, firmware version, Service data[0], Service data[1].

REQ message payload is 0. 
{| class="wikitable"
|REQ message payload: 0
|}If executed successfully, the Status Byte of the RESP message will be 0, the payload format is as below: 

{| class="wikitable"
| colspan="3" |RESP Message payload
|-
|Byte: 0
|Bat Per
|The battery percentage
|-
|Byte: 1
|FW Ver
|Firmware Version
Initial FW Ver:  10   (0x0A)
|-
|Byte: 2
|Service_data
|Service_data[0]
|-
|Byte: 3
|Service_data
|Service_data[1]
|}


==='''0x0F Extend Command'''===
<br />
===='''0x14 Read Hardware Version'''====

*Read the information of Device hardware version.

*REQ Packet payload is 0。

*The Status byte of the RESP data packet after execution successfully is 1 and the payload is 0.

Read the hardware version of WoSensorTH

REQ Packet payload is 0.
{| class="wikitable"
|REQ Packet payload:   0
|}
The Status byte of the RESP data packet after execution successfully is 1 and the payload is 0. The payload is as below:
{| class="wikitable"
| colspan="3" |RESP Packet status:    1
|-
|Byte: 0
|Status
|Command
|}
{| class="wikitable"
| colspan="3" |RESP Packet payload:1
|-
|Byte: 0
|Hardware version
|0x01 – WoSensorTH  V1.0
|}
<br />

===='''0x30 Setting Temperature Display Mode'''====

*This command is for setting the display mode of temperature. You could set it to display Celsius or Fahrenheit.
*The REQ Packet payload is 1。
*The Status byte of the RESP data packet after execution successfully is 1 and the payload is 0.

REQ Packet payload is 1.

REQ Packet payload is as below:
{| class="wikitable"
| colspan="3" |REQ Packet payload:   1
|-
|Byte: 0
|Temperature Mode
|Bit[7-2]: Reserved
Bit[1-0]: Temperature display settings

11:Reserved

10:F mode     Fahrenheit mode

01:C mode     Celsius mode

00:Reserved
|}
The Status byte of the RESP data packet after execution successfully is 1 and the payload is 0, the payload is as below:
{| class="wikitable"
| colspan="3" |RESP Packet status:    1
|-
|Byte: 0
|Status
|Command
|}
{| class="wikitable"
|RESP Packet payload:   0
|}

===='''0x31 Read the Display Mode and Value of the Meter'''====

*This command is for reading the temperature and humidity from the WoSensorTH, the temperature value is one decimal, the range is (-20.0 °C ~ 60.0 °C) ；The humidity is an integer, the range is (0 ~ 99%).
*The REQ Packet payload is 0.
*The Status byte of the RESP data packet after execution successfully is 1 and the payload is 3.

The REQ Packet payload is 0.
{| class="wikitable"
|REQ Packet payload:   0
|}
The Status byte of the RESP data packet after execution successfully is 1 and the payload is 3.  The payload is as below:
{| class="wikitable"
| colspan="3" |RESP Packet status:    1
|-
|Byte: 0
|Status
|1 – OK
|}
The temperature value is one decimal, the range is (-20.0 °C ~ 60.0 °C) ；The humidity is an integer, the range is (0 ~ 99%).
{| class="wikitable"
| colspan="3" |RESP Packet payload:   3
|-
|Byte: 0
|The fractional part of temperature value
|bit[7-6]: Reserved
bit[3-0]: The fractional part of temperature value

    0000 – 1001: 0~9
|-
|Byte: 1
|Temperature Mode
Positive/Negative temperature flag

The integral part of temperature value
|bit[7]: Positive/Negative temperature flag
    0: subzero temperature

    1: positive value

bit[6-0]: The integral part of temperature value

    000 0000 –  111 1111: 0~127 °C

Formula for Celsius to Fahrenheit

F=(C*1.8)+32
|-
|Byte: 2
|Humidity value
|bit[7]: Temperature Mode
    0: °C

    1: °F

If the temperature mode did not be changed by the physical button behind the device, the default value is 0 °C.

bit[6-0]: Humidity value

    000 0000 –  110 0011: 0~99 %
|}

 # Motion Sensor BLE open API

===Motion Sensor Broadcast Message===
The following table is the Service Data of SCAN_RSP.
{| class="wikitable"
| rowspan="2" |Byte: 0
| rowspan="2" |Enctype
Device type
|Bit[7]:NC
|-
|Bit[6:0]-Device Type
'S' : Pair Mode

's' : Const Adv Mode
|-
| rowspan="3" |Byte: 1
| rowspan="3" |Status
|Bit[7]: Has it been scope tested
1: Tested

0: Not tested
|-
|Bit[6]: PIR State
0: No one moves

1: Someone is moving
|-
|Bit[5:0] NC
|-
| rowspan="2" |Byte: 2
| rowspan="2" |update utc
Battery
|Bit[7]:
|-
|Bit[6:0]: battery capacity
|-
|Byte: 3~Byte: 4
|PIR UTC
|Since the last trigger PIR time（s）Low16位
Byte16 High 8 Bit

Byte17 Low 8 Bit
|-
| rowspan="6" |Byte: 5
| rowspan="6" |Sensor Data
|Bit[7]:Since the last trigger PIR time（s）Highest bit(*65536)
|-
|Bit[6]:Reserve
|-
|Bit[5]:LED state
0:disable

1:enalbe
|-
|Bit[4]: IOT state
0:disable

1:enable
|-
|Bit[3:2]: Sensing distance
00:Long

01:Middle

10:Short

11:Reserve
|-
|Bit[1:0]: Light intensity
00:Rserve

01:dark

10:bright

11:Reserve
|}
'''Example''':

[[https://switchbot-open-api.s3.amazonaws.com/Motion+Sensor+BLE+open+api/boardcast_1.png]]

===BLE communication packet basic format===

*The controlling terminal (short as the Terminal below) and the configured device (short as the Device below) use BLE to communicate with each other wirelessly. During the communication, the Terminal acts as a central device, while the Device acts as peripheral device. The Terminal gets basic device info by reading the broadcast message of the Device. They exchange data by using read and write characteristic of communication service.

*All bilateral communication is after the BLE connection established. The Terminal send a REQ message to the Device, and then the Device returns a RESP message.

*All communication message data length is 1-20 bytes, i.e. MTU=20.

*'''RX characteristic UUID of the message from the Terminal to the Device:'''
**UUID: ''cba20002-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE: Vendor UUID types start at this index (128-bit)
**Char Attribute: RW
**Char Properties: notify      

*'''TX  characteristic UUID of the message from the Device to the Terminal:'''
**''cba20003-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE: Vendor UUID types start at this index (128-bit)
**Char Attribute: RW

*The control terminal sends the REQ packet format, and the specific content format of payload varies from command:

{| class="wikitable"
| colspan="3" |REQ Packet No encryption
|-
|Byte: 0
|Magic Number
|0x57 – (fixed value)
|-
| rowspan="3" |Byte: 1
| rowspan="3" |Header
|Bit [7:6] – Version
  0 – (fixed value)
|-
|Bit [5:4] – Encryption Mode
0 – no encryption
|-
|Bit [3:0] – command
0x00 – General command

0x02 – Get device basic info

0x05 – Get local trigger information command
|-
|Byte: 2-19
|Payload
|Depends on Command
|}
The RESP packet format returned by Motion Sensor to the control terminal is as follows: 
{| class="wikitable"
| colspan="3" |RESP message
|-
|Byte: 0
|Response status
|0x01 - OK Action executed
0x02 - ERROR Error while executing an Action

0x03 - BUSY Device is busy now, please try later

0x04 - Communication protocol version incompatible

0x05 - Device does not support this Command

0x06 - Device low battery

0x07 - Device is encrypted

0x08 - Device is unencrypted

0x09 - Password error

0x0A - Device does not support this encription method

0x0B - Failed to locate a nearby mesh Device

0x0C - Failed to connect to the network

0x0D - This command is not supported in the current mode

0x0E - Disconnected from the device that needs to stay connected
|-
|Byte: 1-19
|Payload
|Depends on the Command to reply
|}
Note:

1、Status returns the processing status of the previous command, which is the first BYTE of each RESP packet, which is also necessary.

2、PayLoad's specific content format depends on Command and its processing results, the data length ranges from 0-19 bytes, that is, no.

===0x00 General Command===

====0x11 Get device status data====

*Obtain the status information of the Motion Sensor device:
*The format of the command sent by the terminal to the device is as follows:

{| class="wikitable"
| colspan="3" |REQ Packet CMD Format
|-
|Index
|Section
|Value
|-
|Byte: 0
|Magic num
|0x57
|-
|Byte: 1
|General cmd
|0x00
|-
|Byte: 2
|Subcmd
|0x11
|}

*After successful execution, the Status byte of the RESP packet is 1, the Payload is 7 (index), and the format of the RESP Packet payload is as follows:

{| class="wikitable"
| colspan="3" |RESP Packet
|-
|Byte: 0
|Status
|Command execution status
|-
|Byte: 1
| rowspan="4" |Payload
|rule code
When the parsing rule is 0x01
|-
|Byte: 2~Byte: 5
|The number of seconds from the current HAL last trigger, door close trigger or door open trigger (normal mode)
Device local UTC (test mode)
|-
|Byte: 6
|Light Level
0:reserve

1:dark

2:bright
|-
|Byte: 7
|Pir status
0：unmaned

1：someone
|}
'''Example''':

Use NRF Connect to issue command 0x570011 to the Device, Get device basic information.

The replied RESP message is 0x0101000000160201;

Status Byte is 0x01 OK Action executed.

RESP message payload Byte 0: 0x01, Command execution status.

RESP message payload Byte 1: 0x01, rule code.

RESP message payload Byte 2~Byte5: 0x00000016, utc timestamp (big endian).

RESP message payload Byte 6: 0x02, Light level.

RESP message payload Byte 7: 0x01, Pir status.

[[https://switchbot-open-api.s3.amazonaws.com/Motion+Sensor+BLE+open+api/570011.png]]

===0x02 Get device basic information command===
This command is used to obtain the basic information of the device: 

The Status byte of the RESP packet after successful execution is 1, and the Payload is the basic information of the device: 

This command is used to obtain the basic information of the Motion Sensor device (battery level, firmware version, cloud service switch, PIR detection distance, PIR working mode, light effect switch): 

The payload is 0 (index), and the format of the REQ Packet payload is as follows: 
{| class="wikitable"
|REQ Packet payload: 0
|}

*After successful execution, the Status byte of the RESP packet is 1, the Payload is 6 (index), and the format of the RESP Packet payload is as follows:

{| class="wikitable"
| colspan="3" |RESP Packet payload
|-
|Byte: 0
|Bat Per
|The battery percentage
|-
|Byte: 1
|FW Ver
|Firmware Version
|-
|Byte: 2
|Para Info
|bit[7-5]: reverse
bit[4]:

0:disable iot

1:enable iot

bit[2:3]

0:Long

1:Middle

2:Short

Bit[1]:PIR Work Mode

0：Normal Mode

1：Test Mode

Bit[0]: LED Effect

0:Disable LED

1:Enable LED
|-
|Byte: 3
|Total Act Num
|Total Act Num
|}
'''Example''':

Use NRF Connect to issue command 0x5702 to the Device, Get device basic information.

The replied RESP message is 0x01640D0502;

Status Byte is 0x01 OK Action executed.

RESP message payload Byte 0: 0x64, The battery percentage.

RESP message payload Byte 1: 0x0D, Firmware Version.

RESP message payload Byte 2: 0x05, Para Info.

RESP message payload Byte 3: 0x02, Total Act Num.

[[https://switchbot-open-api.s3.amazonaws.com/Motion+Sensor+BLE+open+api/5702.png]]

# Plug Mini BLE open API

==='''Plug Mini Broadcast Message'''===
The following table is the Manufacture Data of ADV_IND.
{| class="wikitable"
|Byte 0
| rowspan="2" |UUID
|0x09
|UUID is 0x0969, big-endian
|-
|Byte 1
|0x69
|　
|-
|Byte 2
| rowspan="6" |MAC Address
|0x01
|Device Mac Address 0x010203040506, big-endian
|-
|Byte 3
|0x02
|　
|-
|Byte 4
|0x03
|　
|-
|Byte 5
|0x04
|　
|-
|Byte 6
|0x05
|　
|-
|Byte 7
|0x06
|　
|-
|Byte 8
|Sequence Number
|1~255
|1~255 increments, 256 overflows to 1, and the broadcast packet data is automatically incremented when there is an update
|-
|Byte 9
|Plug Mini state
|0x00  - power off
0x80  - power on
|Plug Mini's switch status
|-
| rowspan="3" |Byte 10
|bit[0]   delay
|0-no delay， 1-has delay
|Is there an delay
|-
|bit[1]   timer
|0-no timer， 1-has timer
|Is there an Timer
|-
|bit[2]   sync utc time
|0-no sync time ， 1-already sync time
|Whether the UTC time has been synchronized
|-
|Byte 11
|wifi rssi
|
|The rssi signal value of the currently connected wifi
|-
| rowspan="2" |Byte 12 - 13
|Overload
|byte 18[7]  overload
|Whether the Plug Mini is overloaded, more than 15A current overload
|-
|Power
|byte 18[0-6]  Power MSB
byte 18[0-7]  Power LSB
|Plug Mini current power value of the load
|}
==='''BLE communication packet basic format'''===

*he controlling terminal (short as the Terminal below) and the configured device (short as the Device below) use BLE to communicate with each other wirelessly. During the communication, the Terminal acts as a central device, while the Device acts as peripheral device. The Terminal gets basic device info by reading the broadcast message of the Device. They exchange data by using read and write characteristic of communication service.

*All bilateral communication is after the BLE connection established. The Terminal send a REQ message to the Device, and then the Device returns a RESP message.

*All communication message data length is 1-20 bytes, i.e. MTU=20.

*'''RX characteristic UUID of the message from the Terminal to the Device:'''
**UUID: ''cba20002-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE: Vendor UUID types start at this index (128-bit)
**Char Attribute: RW
**Char Properties: notify      

*'''TX  characteristic UUID of the message from the Device to the Terminal:'''
**''cba20003-224d-11e6-9fb8-0002a5d5c51b''
**UUID TYPE: Vendor UUID types start at this index (128-bit)
**Char Attribute: RW

*The control terminal sends the REQ packet format, and the specific content format of payload varies from command:

<br />
{| class="wikitable"
| colspan="3" |REQ Packet No encryption
|-
|Byte: 0
|Magic Number
|0x57 – (fixed value)
|-
| rowspan="3" |Byte: 1
| rowspan="3" |Header
|Bit [7:6] – Version
0 – (fixed value)
|-
|Bit [5:4] – reserve
|-
|Bit [3:0] – command
0x0F – [http://192.168.2.110/%E6%96%87%E4%BB%B6:///E:/%E5%8D%A7%E6%A7%BD%E7%A7%91%E6%8A%80%E8%93%9D%E7%89%99%E9%80%9A%E4%BF%A1%E5%8D%8F%E8%AE%AE%E4%B8%8E%E8%A7%84%E8%8C%83-20181227copy.docx&action=edit&redlink=1&action=edit&redlink=1 expansion command]
|-
|Byte: 2-19
|Payload
|Depends on Command
|}
==='''0x0F Expansion command'''===

====0x570f5001 set state====

{| class="wikitable"
|+REQ Packet payload
!REQ Packet
!Turn On Plug Mini
!Turn Off Plug Mini
!TOGGLE Plug Mini
|-
|Byte 0
| colspan="3" |0x50
|-
|Byte 1
| colspan="3" |0x01
|-
|Byte 2
| colspan="2" |0x01
|0x02
|-
|Byte 3
|0x80
|0x00
|0x80
|}
{| class="wikitable"
!RESP Packet
!
|-
|Byte 0
|0x01
|-
|Byte 1
|0x00 Plug Mini Off
0x80 Plug Mini On
|}
'''Example:'''

*Turn On Plug Mini REQ   0x570f50010180 RESP 0x0180
*Turn Off Plug Mini REQ   0x570f50010100 RESP 0x0100


====0x570f5101 read state====

The payload length of the request packet is 0.

The response will return the current Plug Mini status.
{| class="wikitable"
| colspan="2" |RESP Packet payload
|-
|Byte: 0
|0x01
|-
|Byte: 1
|0x00 Plug Mini Off
0x80 Plug Mini On
|}
'''Example:'''

*Send 0x570f5101 query Plug Mini status RESP 0180
