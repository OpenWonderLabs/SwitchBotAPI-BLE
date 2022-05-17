## Motion Sensor BLE open API

- [Color Bulb Broadcast Message](#color-bulb-broadcast-message)
- [BLE communication packet basic format](#ble-communication-packet-basic-format)
- [BLE communication packet basic format](#broadcast-message-format)
  - [0x570F4701 set the status and color of the Bulb](#old-broadcast-message)
  - [0x570F4801 read the status of Bulb](#new-broadcast-message)

<table>
    <thead>
        <tr>
            <th colspan=3>RESP Packet status: 1</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Status</td>
            <td rowspan=1>1 – OK</td>
        </tr>
    </tbody>
</table>

### Motion Sensor Broadcast Message

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

<img alt="boardcast_1" src="https://switchbot-open-api.s3.amazonaws.com/Motion+Sensor+BLE+open+api/boardcast_1.png"></a>

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

<img alt="570011" src="https://switchbot-open-api.s3.amazonaws.com/Motion+Sensor+BLE+open+api/570011.png"></a>

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

<img alt="5702" src="https://switchbot-open-api.s3.amazonaws.com/Motion+Sensor+BLE+open+api/5702.png"></a>

CopyRight@2022 Wonderlabs, Inc.

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)