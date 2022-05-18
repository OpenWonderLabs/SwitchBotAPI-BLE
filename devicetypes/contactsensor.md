## Contact Sensor BLE open API

- [Contact Sensor Broadcast Message](#contact-sensor-broadcast-message)
- [BLE communication packet basic format](#ble-communication-packet-basic-format)
- [0x00 General Command](#0x00-general-command)
  - [0x11 Get device status data](#0x11-get-device-status-data)
- [0x02 Get device basic information command](#0x02-get-device-basic-information-command)

### Contact Sensor Broadcast Message

- The following table is the Service Data of SCAN_RSP.

<table>
    <thead>
        <tr>
            <th colspan=3>RESP Packet status: 1</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=2>Byte: 0</td>
            <td rowspan=2>Enctype Device type</td>
            <td rowspan=1>Bit[7]:NC 0: No encryption 1: With encryption, encryption method 1</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[6:0]-Device Type 'D' : Pair Mode 'd' : Const Adv Mode</td>
        </tr>
        <tr>
            <td rowspan=3>Byte: 1</td>
            <td rowspan=3>Status</td>
            <td rowspan=1>Bit[7]: Has it been scope tested 1: Tested 0: Not tested</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[6]: PIR State 0: No one moves 1: Someone is moving</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[5:0] NC</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 2</td>
            <td rowspan=2>update utc Battery</td>
            <td rowspan=1>Bit[7]:NC</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[6:0]: battery capacity</td>
        </tr>
        <tr>
            <td rowspan=5>Byte: 3</td>
            <td rowspan=5>Sensor Data</td>
            <td rowspan=1>Bit[7]: Since the last trigger PIR time（s）Highest bit (*65536)</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[6]: Since the last trigger HAL time（s）Highest bit(*65536)</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[3:5]:reserve</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[1:2]:Hal State 0:door close 1:door open 2:timeout not close</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[0]:Light Level 0:dark 1:light</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 4~Byte: 5</td>
            <td rowspan=1>PIR UTC</td>
            <td rowspan=1>Time from the last PIR trigger(s）low 16 Bit Byte4 High 8 Bit Byte5 Low 8 Bit</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 6~Byte: 7</td>
            <td rowspan=1>HAL UTC</td>
            <td rowspan=1>Time from the last HAL trigger(s）low 16 Bit Byte6 High 8 Bit Byte7 Low 8 Bit</td>
        </tr>
        <tr>
            <td rowspan=3>Byte: 8</td>
            <td rowspan=3>Act Counter</td>
            <td rowspan=1>Bit[6:7]: Number of entrances The number of door entry actions, the Counter value is increased by 1 (1~3)cycle, 3 Value changes after overflow 1</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[4:5]: Number of Go out Counter The number of times to go out, the Counter value is increased by 1 (1~3)cycle, 3 Value changes after overflow 1</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[0:3]:Button push counter Each time the button is pressed, the Counter value increases by 1 (1~15)cycle,16 Value changes after overflow 1</td>
        </tr>
    </tbody>
</table>

Example:

<img alt="boardcast_1" src="https://switchbot-open-api.s3.amazonaws.com/Contact+Sensor+open+api/boardcast_1.png"></a>

### BLE communication packet basic format

- The controlling terminal (short as the Terminal below) and the configured device (short as the Device below) use BLE to communicate with each other wirelessly. During the communication, the Terminal acts as a central device, while the Device acts as peripheral device. The Terminal gets basic device info by reading the broadcast message of the Device. They exchange data by using read and write characteristic of communication service.
- All bilateral communication is after the BLE connection established. The Terminal send a REQ message to the Device, and then the Device returns a RESP message.
- All communication message data length is 1-20 bytes, i.e. MTU=20.
- ### RX characteristic UUID of the message from the Terminal to the Device:
  - UUID: ''cba20002-224d-11e6-9fb8-0002a5d5c51b''
  - UUID TYPE: Vendor UUID types start at this index (128-bit)
  - Char Attribute: RW
  - Char Properties: notify      
- ### TX  characteristic UUID of the message from the Device to the Terminal:'''
  - ''cba20003-224d-11e6-9fb8-0002a5d5c51b''
  - UUID TYPE: Vendor UUID types start at this index (128-bit)
  - Char Attribute: RW
- The control terminal sends the REQ packet format, and the specific content format of PayLoad varies from Command:

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
            <td rowspan=1>Bit [7:6] – Version   0 – (fixed value)</td>
        </tr>
        <tr>
            <td rowspan=1>Bit [7:6] – Version   0 – (fixed value)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2-19</td>
            <td rowspan=1>Payload</td>
            <td rowspan=1>Depends on Command</td>
        </tr>
    </tbody>
</table>

- Contact Sensor Returns the RESP packet format of the control terminal as follows:

<table>
    <thead>
        <tr>
            <th colspan=3>REQ Packet No encryption</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Response status</td>
            <td rowspan=1>0x01 - OK Action executed 0x02 - ERROR Error while executing an Action 0x03 - BUSY Device is busy now, please try later 0x04 - Communication protocol version incompatible 0x05 - Device does not support this Command 0x06 - Device low battery 0x07 - Device is encrypted 0x08 - Device is unencrypted 0x09 - Password error 0x0A - Device does not support this encription method 0x0B - Failed to locate a nearby mesh Device 0x0C - Failed to connect to the network 0x0D - This command is not supported in the current mode 0x0E - Disconnected from the device that needs to stay connected</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1-19</td>
            <td rowspan=1>Payload</td>
            <td rowspan=1>Depends on the Command to reply</td>
        </tr>
    </tbody>
</table>

Note:

1. Status returns the processing status of the previous command, which is the first BYTE of each RESP packet, which is also necessary.

2. PayLoad's specific content format depends on Command and its processing results, the data length ranges from 0-19 bytes, that is, no.

### 0x00 General Command

#### 0x11 Get device status data

- Obtain the status information of the Contact Sensor device;
- The format of the command sent by the terminal to the device is as follows:

<table>
    <thead>
        <tr>
            <th colspan=3>REQ Packet CMD Format</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Index</td>
            <td rowspan=1>Section</td>
            <td rowspan=1>Value</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Magic num</td>
            <td rowspan=1>0x57</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>General cmd</td>
            <td rowspan=1>0x00</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=1>Subcmd</td>
            <td rowspan=1>0x11</td>
        </tr>
    </tbody>
</table>

- After successful execution, the Status byte of the RESP packet is 1, the Payload is 8 (index), and the format of the RESP Packet payload is as follows:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP Packet</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Status</td>
            <td rowspan=1>Command execution status</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=4>Payload</td>
            <td rowspan=1>rule code When the parsing rule is 0x01</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2~Byte: 5</td>
            <td rowspan=1>The number of seconds from the current HAL last trigger, door close trigger or door open trigger (normal mode) Device local UTC (test mode)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 6</td>
            <td rowspan=1>Pir Status 0：unmanned 1：someone</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 7</td>
            <td rowspan=1>Light Level 0: dark 1: bright</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 8</td>
            <td rowspan=1></td>
            <td rowspan=1>Door Status 0：Close 1：Open 2：Timeout not close</td>
        </tr>
    </tbody>
</table>

**Example**:

Use NRF Connect to issue command 0x570011 to the Device, Get device basic information.

The replied RESP message is 0x010100000DBB010100;

Status Byte is 0x01 OK Action executed.

RESP message payload Byte 0: 0x01, Command execution status.

RESP message payload Byte 1: 0x01, rule code.

RESP message payload Byte 2~Byte5: 0x00000DBB, utc timestamp (big endian).

RESP message payload Byte 6: 0x01, Pir Status.

RESP message payload Byte 7: 0x01, Light Level.

RESP message payload Byte 8: 0x00, Door Status.

<img alt="570011" src="https://switchbot-open-api.s3.amazonaws.com/Contact+Sensor+open+api/570011.png"></a>

### 0x02 Get device basic information command

- This command is used to obtain the basic information of the device:
- The Status byte of the RESP packet after successful execution is 1, and the Payload is the basic information of the device:
- This command is used to obtain the basic information of the Contact Sensor device (battery power, firmware version, Hall enable status, PIR detection distance, cloud service switch, door sensor installation position, PIR working mode, light effect switch, door access mode, timeout Unclosed time, whether it is in the adding process, environmental threshold state);
- The payload is 0 (index), and the format of the REQ Packet payload is as follows:

<table>
    <thead>
        <tr>
            <th colspan=3></th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>REQ Packet payload: 0</td>
        </tr>
    </tbody>
</table>

- After successful execution, the Status byte of the RESP packet is 1, the Payload is 6 (index), and the format of the RESP Packet payload is as follows:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP Packet payload</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Bat Per</td>
            <td rowspan=1>The battery percentage</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>FW Ver</td>
            <td rowspan=1>Firmware Version</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=1>Para Info</td>
            <td rowspan=1>bit[7-6]:Active Hal 0-reserve 1-Active left 2-Active right 3-Active both bit[5]:detect range 0-short 1-long bit[4]: 0-disable iot 1-enable iot bit[2:3] Installation location 0-door 1-wall 2-window Bit[1]:PIR Work Mode 0-Normal Mode 1-Test Mode Bit[0]: 0-Disable LED 1-Enable LED</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 3</td>
            <td rowspan=1>Total Act Num</td>
            <td rowspan=1>Total Act Num</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 4</td>
            <td rowspan=1>Go-Out Mode</td>
            <td rowspan=1>bit[7]: 0-Press Mode 1-Auto Detec Mode bit[6~0]: Detection Time</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 5</td>
            <td rowspan=1>Timeout Close Time</td>
            <td rowspan=1>Door and window open time overtime （min)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 6</td>
            <td rowspan=1>Add process Environment dark threshold setting</td>
            <td rowspan=1>Bit[2:7] reserve Bit[1]: Work flow identification 0-Normal work flow 1-Add process (use for adding process) Bit[0] Ambient dark threshold state 0-Can't set current status 1-Can set current status</td>
        </tr>
    </tbody>
</table>

**Example**:

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

<img alt="5702" src="https://switchbot-open-api.s3.amazonaws.com/Contact+Sensor+open+api/5702.png"></a>

CopyRight@2022 Wonderlabs, Inc.

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)