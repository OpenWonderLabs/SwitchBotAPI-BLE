## Plug Mini BLE open API

- [Plug Mini Broadcast Message](#plug-mini-broadcast-message)
- [BLE communication packet basic format](#ble-communication-packet-basic-format)
- [0x0F Expansion command](#0x0F-expansion-command)
  - [0x570f5001 set state](#0x570f5001-set-state)
  - [0x570f5101 read state](#0x570f5101-read-state)


### Plug Mini Broadcast Message
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
            <td rowspan=1>Byte: 9</td>
            <td rowspan=1>Plug Mini state</td>
            <td rowspan=1>0x00 - power off 0x80 - power on</td>
            <td rowspan=1>Plug Mini's switch status</td>
        </tr>
        <tr>
            <td rowspan=3>Byte: 10</td>
            <td rowspan=1>bit[0] delay</td>
            <td rowspan=1>0-no delay， 1-has delay</td>
            <td rowspan=1>Is there a delay</td>
        </tr>
        <tr>
            <td rowspan=1>bit[1] timer</td>
            <td rowspan=1>0-no timer， 1-has timer</td>
            <td rowspan=1>Is there a Timer</td>
        </tr>
        <tr>
            <td rowspan=1>bit[2] sync utc time</td>
            <td rowspan=1>0-no sync time ， 1-already sync time</td>
            <td rowspan=1>Whether the UTC time has been synchronized</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 11</td>
            <td rowspan=1>wifi rssi</td>
            <td rowspan=1></td>
            <td rowspan=1>The rssi signal value of the currently connected wifi</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 12-13</td>
            <td rowspan=1>Overload</td>
            <td rowspan=1>byte 12[7] overload</td>
            <td rowspan=1>Whether the Plug Mini is overloaded, more than 15A current overload</td>
        </tr>
        <tr>
            <td rowspan=1>Power</td>
            <td rowspan=1>
            byte 12[0-6] Power MSB

byte 13[0-7] Power LSB</td>
            <td rowspan=1>Plug Mini current power value of the load</td>
        </tr>
    </tbody>
</table>

### BLE communication packet basic format
- he controlling terminal (short as the Terminal below) and the configured device (short as the Device below) use BLE to communicate with each other wirelessly. During the communication, the Terminal acts as a central device, while the Device acts as peripheral device. The Terminal gets basic device info by reading the broadcast message of the Device. They exchange data by using read and write characteristic of communication service.
- All bilateral communication is after the BLE connection established. The Terminal send a REQ message to the Device, and then the Device returns a RESP message.
- All communication message data length is 1-20 bytes, i.e. MTU=20.
- **RX characteristic UUID of the message from the Terminal to the Device:**
    - UUID: *cba20002-224d-11e6-9fb8-0002a5d5c51b*
    - UUID TYPE: Vendor UUID types start at this index (128-bit)
    - Char Attribute: RW
    - Char Properties: notify      

- **TX  characteristic UUID of the message from the Device to the Terminal:**
    - *cba20003-224d-11e6-9fb8-0002a5d5c51b*
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
            <td rowspan=1>Bit [7:6] – Version   0 – (fixed value)</td>
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

## 0x0F Expansion command

### 0x570f5001 set state

<table>
    <thead>
        <tr>
            <th colspan=4>REQ Packet payload</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>REQ Packet</td>
            <td rowspan=1>Turn On Plug Mini</td>
            <td rowspan=1>Turn Off Plug Mini</td>
            <td rowspan=1>TOGGLE Plug Mini</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td colspan=3>0x50</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td colspan=3>0x01</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td colspan=2>0x01</td>
            <td rowspan=1>0x02</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 3</td>
            <td rowspan=1>0x80</td>
            <td rowspan=1>0x00</td>
            <td rowspan=1>0x80</td>
        </tr>
    </tbody>
</table>

<table>
    <thead>
        <tr>
            <th colspan=2>RESP Packet</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>0x01</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>0x00 Plug Mini Off 0x80 Plug Mini On</td>
        </tr>
    </tbody>
</table>

**Example:**
- Turn On Plug Mini REQ   0x570f50010180 RESP 0x0180
- Turn Off Plug Mini REQ   0x570f50010100 RESP 0x0100


### 0x570f5101 read state

The payload length of the request packet is 0.

The response will return the current Plug Mini status.

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
            <td rowspan=1>0x00 Plug Mini Off 0x80 Plug Mini On</td>
        </tr>
    </tbody>
</table>

**Example:**
- Send 0x570f5101 query Plug Mini status RESP 0180

CopyRight@2022 Wonderlabs, Inc.

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)
