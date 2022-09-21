## Color Bulb BLE open API

- [Color Bulb Broadcast Message](#color-bulb-broadcast-message)
- [BLE communication packet basic format](#ble-communication-packet-basic-format)
- [BLE communication packet basic format](#broadcast-message-format)
  - [0x570F4701 set the status and color of the Bulb](#0x570F4701-set-the-status-and-color-of-the-Bulb)
  - [0x570F4801 read the status of Bulb](#0x570F4801-read-the-status-of-Bulb)

### Color Bulb Broadcast Message

The following table is the Manufacture Data of ADV_IND.

<table>
    <thead>
        <tr>
            <th colspan=4></th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=2>UUID</td>
            <td rowspan=1>0x09</td>
            <td rowspan=1>UUID is 0x0969, big-endian</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>0x69</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=6>MAC Address</td>
            <td rowspan=1>0x01</td>
            <td rowspan=1>Device Mac Address 0x010203040506, big-endian</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 3</td>
            <td rowspan=1>0x02</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 4</td>
            <td rowspan=1>0x03</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 5</td>
            <td rowspan=1>0x04</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 6</td>
            <td rowspan=1>0x05</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 7</td>
            <td rowspan=1>0x06</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 8</td>
            <td rowspan=1>Sequence Number</td>
            <td rowspan=1>1~255</td>
            <td rowspan=1>1~255 increments, 256 overflows to 1, and the broadcast packet data is automatically incremented when there is an update</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 9</td>
            <td rowspan=1>bit[7] power state</td>
            <td rowspan=1>0-power off, 1-power on</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>bit[6:0] light level</td>
            <td rowspan=1>1~100%</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=4>Byte: 10</td>
            <td rowspan=1>bit[7] is delay</td>
            <td rowspan=1>0-no delay，1-has delay</td>
            <td rowspan=1>Is there an delay</td>
        </tr>
        <tr>
            <td rowspan=1>bit[6:4] Network status</td>
            <td rowspan=1>0-Wi-Fi Connecting 1-IoT Connecting 2-IoT Connected</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>bit[3] is on state preset</td>
            <td rowspan=1>0-not preset，1-preset</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>bit[2:0] light state</td>
            <td rowspan=1>1-white, 2-color, 3-dynamic</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 11</td>
            <td rowspan=1>bit[7] RSSI quality</td>
            <td rowspan=1>0-normal；1-Bad</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>bit[6:0] dynamic rate</td>
            <td rowspan=1>1-100%</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 12</td>
            <td rowspan=1>bit[7:2]  Loop Index</td>
            <td rowspan=1></td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>bit[1:0] NC</td>
            <td rowspan=1></td>
            <td rowspan=1></td>
        </tr>
    </tbody>
</table>


### BLE communication packet basic format

- he controlling terminal (short as the Terminal below) and the configured device (short as the Device below) use BLE to communicate with each other wirelessly. During the communication, the Terminal acts as a central device, while the Device acts as peripheral device. The Terminal gets basic device info by reading the broadcast message of the Device. They exchange data by using read and write characteristic of communication service.
- All bilateral communication is after the BLE connection established. The Terminal send a REQ message to the Device, and then the Device returns a RESP message.
- All communication message data length is 1-20 bytes, i.e. MTU=20.
- ### RX characteristic UUID of the message from the Terminal to the Device:
  - UUID: ''cba20002-224d-11e6-9fb8-0002a5d5c51b''
  - UUID TYPE: Vendor UUID types start at this index (128-bit)
  - Char Attribute: RW
  - Char Properties: notify      
- ### TX  characteristic UUID of the message from the Device to the Terminal:'''
  - cba20003-224d-11e6-9fb8-0002a5d5c51b
  - UUID TYPE: Vendor UUID types start at this index (128-bit)
  - Char Attribute: RW
- The control terminal sends the REQ packet format, and the specific content format of payload varies from command:

<table>
    <thead>
        <tr>
            <th colspan=3>REQ Packet No encryption</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Magic Number</td>
            <td rowspan=1>0x57 – (fixed value)</td>
        </tr>
        <tr>
            <td rowspan=3>Byte: 1</td>
            <td rowspan=3>Header</td>
            <td rowspan=1>Bit [7:6] – Version 0 – (fixed value)</td>
        </tr>
        <tr>
            <td rowspan=1>Bit [5:4] – reserve</td>
        </tr>
        <tr>
            <td rowspan=1>Bit [3:0] – command 0x0F – <a href="http://192.168.2.110/文件:///E:/卧槽科技蓝牙通信协议与规范-20181227copy.docx&action=edit&redlink=1&action=edit&redlink=1">expansion command</a></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2-19</td>
            <td rowspan=1>Payload</td>
            <td rowspan=1>Depends on Command</td>
        </tr>
    </tbody>
</table>

### 0x0F Expansion command
#### 0x570F4701 set the status and color of the Bulb

<table>
    <thead>
        <tr>
            <th colspan=1>REQ Packet Payload</th>
            <th colspan=1>Turn On Bulb</th>
            <th colspan=1>Turn Off Bulb</th>
            <th colspan=1>TOGGLE</th>
            <th colspan=1>Change Lvl + RGB</th>
            <th colspan=1>Change Lvl + CW</th>
            <th colspan=1>Change Lvl</th>
            <th colspan=1>Change RGB</th>
            <th colspan=1>Change CW</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>0x47</td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>0x01</td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=1>0x01</td>
            <td rowspan=1>0x02</td>
            <td rowspan=1>0x03</td>
            <td rowspan=1>0x12</td>
            <td rowspan=1>0x13</td>
            <td rowspan=1>0x14</td>
            <td rowspan=1>0x16</td>
            <td rowspan=1>0x17</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 3</td>
            <td rowspan=1>0x01</td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1>Lvl (0 - 100)</td>
            <td rowspan=1>Lvl (0 - 100)</td>
            <td rowspan=1>Lvl (0 - 100)</td>
            <td rowspan=1>Lvl (0 - 100)</td>
            <td rowspan=2>C/W (2700K - 6500K)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 4</td>
            <td rowspan=1>0x01</td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1>R (0 - 255)</td>
            <td rowspan=2></td>
            <td rowspan=1></td>
            <td rowspan=1>R (0 - 255)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 5</td>
            <td rowspan=1>0x01</td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1>G (0 - 255)</td>
            <td rowspan=1></td>
            <td rowspan=1>G (0 - 255)</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 6</td>
            <td rowspan=1>0x01</td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1>B (0 - 255)</td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1>B (0 - 255)</td>
            <td rowspan=1></td>
        </tr>
    </tbody>
</table>

<table>
    <thead>
        <tr>
            <th colspan=1>RESP Packet</th>
            <th colspan=1></th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>0x01</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>bit[7]: power status 0 OFF 1 ON bit[6]: light status 0 no preset</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=1>Lvl (0 - 100)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 3</td>
            <td rowspan=1>R (0 - 255)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 4</td>
            <td rowspan=1>G (0 - 255)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 5</td>
            <td rowspan=1>B (0 - 255)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 6</td>
            <td rowspan=2>C/W （2700K- 6500K)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 7</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 8</td>
            <td rowspan=1>0xFF no care 0x01 white 0x02 color 0x03 dyanmic 0x04 dynamic group 0x06 music</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 9</td>
            <td rowspan=1>0xFF no care other preset idx</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 10</td>
            <td rowspan=1>current mode 0x01 white 0x02 color 0x03 dyanmic</td>
        </tr>
    </tbody>
</table>

### Example:

- Turn On Bulb REQ 0x570f470101 RESP 0x018032FF00000000FFFF02
- Turn Off Bulb REQ 0x570f470102 RESP 0x010032FF00000000FFFF02
- Set Bulb into a blue 50% brightness REQ 0x570f470112320000ff RESP 0x0180320000FF0000FFFF02
- Adjust the brightness of Bulb REQ 0x570f47011420 RESP 0x0180200000FF0000FFFF02

#### 0x570F4801 read the status of Bulb
The payload length of the request packet is 0.

The response will return the current Bulb status.

<table>
    <thead>
        <tr>
            <th colspan=2>RESP Packet payload</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>0x01</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>power and light status</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=1>bulb brightness</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 3</td>
            <td rowspan=1>bulb R</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 4</td>
            <td rowspan=1>bulb G</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 5</td>
            <td rowspan=1>bulb B</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 6-7</td>
            <td rowspan=1>bulb temperature</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 5-10</td>
            <td rowspan=1>bulb mode</td>
        </tr>
    </tbody>
</table>

### Example:
- Send 0x570F4801 query Bulb status RESP 0180200000FF0000FFFF02

CopyRight@2022 Wonderlabs, Inc.

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)
