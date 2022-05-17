## LED Strip Light BLE open API

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

### LED Strip Light Broadcast Message

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

CopyRight@2022 Wonderlabs, Inc.

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)