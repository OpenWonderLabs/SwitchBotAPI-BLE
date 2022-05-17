## Plug Mini BLE open API

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

### Plug Mini Broadcast Message
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

CopyRight@2022 Wonderlabs, Inc.

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)