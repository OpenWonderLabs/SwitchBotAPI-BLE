## Curtain BLE open API

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

## Curtain Broadcast package

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

<img alt="boardcast_1" src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/boardcast_1.png"></a>


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

<img alt="5702_1" src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/5702_1.png"></a>

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

<img alt="570f450105ff32" src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/570f450105ff32.png"></a>

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

<img alt="570f460401" src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/570f460401.png"></a>
<img alt="570f460402" src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/570f460402.png"></a>
<img alt="570f468101" src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/570f468101.png"></a>


CopyRight@2022 Wonderlabs, Inc.

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)