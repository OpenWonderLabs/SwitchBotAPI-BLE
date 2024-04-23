# Curtain 3 BLE API

## Broadcast Package

The following table describes the Service Data of SCAN_RSP.
<table>
  <thead>
    <th colspan=5>SCAN RSP AD Structure</th>
  </thead>
  <tbody>
    <tr>
      <td rowspan=20>AD Structure 1</td>
      <td>len</td>
      <td>Byte 0</td>
      <td></td>
      <td>0x09</td>
    </tr>
    <tr>
      <td>type</td>
      <td>Byte 1</td>
      <td>Service Data</td>
      <td>0x16</td>
    </tr>
    <tr>
      <td rowspan=18>data</td>
      <td>Byte 2~3</td>
      <td></td>
      <td>0xFD3D</td>
    </tr>
    <tr>
      <td rowspan=2>Byte 4</td>
      <td>Enc type</td>
      <td>Bit[7] NC</td>
    </tr>
    <tr>
      <td>Device Type</td>
      <td>Bit [6:0] – Device Type, <br>‘[’Const Adv Mode, <br>‘{’Pair Mode</td>
    </tr>
    <tr>
      <td rowspan=5>Byte 5</td>
      <td rowspan=3>Status</td>
      <td>Bit [7] – Whether to allow connection <br>1- allowed, 0- not allowed</td>
    </tr>
    <tr>
      <td>Bit [6] – Calibration situation<br>1-calibrated, 0-not calibrated, need to be calibrated</td>
    </tr>
    <tr>
      <td>Bit [5-0] NC</td>
    </tr>
    <tr>
      <td>Update utc flag</td>
      <td>Bit[7] NC</td>
    </tr>
    <tr>
      <td>Battery</td>
      <td>Bit [6:0] – Battery capacity 0~100%</td>
    </tr>
    <tr>
      <td rowspan=2>Byte 6</td>
      <td>MotionState</td>
      <td>bit [7] - Movement status, 0-stationary, 1-movement</td>
    </tr>
    <tr>
      <td>Position</td>
      <td>Bit [6:0] - The current position of the device (%)</td>
    </tr>
    <tr>
      <td rowspan=2>Byte 7</td>
      <td>LightLevel</td>
      <td>bit [7:4] - Light level (1-10)</td>
    </tr>
    <tr>
      <td>DeviceChain</td>
      <td>bit [3:0] - Device chain length</td>
    </tr>
    <tr>
      <td rowspan=2>Byte 8</td>
      <td>LightLevel</td>
      <td>bit [7:4]-light level(1-10)</td>
    </tr>
    <tr>
      <td>DeviceChain</td>
      <td>bit [3:0] - Device chain length</td>
    </tr>
    <tr>
      <td>Byte 9</td>
      <td>CrashType</td>
      <td>bit [7:0]-NC</td>
    </tr>
  </tbody>
</table>

<a><img src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/curtain3-broadcast-1.jpeg"></a>



## Basic format of BLE communication data packet
1. UUID related to communication.

- Communication service UUID:
  *cba20d00-224d-11e6-9fb8-0002a5d5c51b*
  UUID TYPE: Vendor UUID types start at this index (128-bit)
- RX characteristic UUID of the terminal sending data to the device:
  *cba20002-224d-11e6-9fb8-0002a5d5c51b*
  UUID TYPE: Vendor UUID types start at this index (128-bit)
  Char Attribute: RW
  Char Properties: notify
- TX characteristic UUID of the device sending data to the terminal:
  *cba20003-224d-11e6-9fb8-0002a5d5c51b*
  UUID TYPE: Vendor UUID types start at this index (128-bit)
  Char Attribute: RW

2. All two-way communication occurs after the BLE connection is established. The communication form is that the terminal first sends a REQ request packet to the device, and the device returns an RSP packet.

The terminal first sends the REQ request packet to the device. The basic format is as follows:

<table>
    <thead>
        <tr>
            <th colspan=3>REQ Packet</th>
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
            <td rowspan=1>Bit [5:4] – Encryption Mode 0 – no encryption</td>
        </tr>
        <tr>
            <td rowspan=1>Bit [3:0] – command 0x02 – Get basic device information 0x0F – extended command</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2-19</td>
            <td rowspan=1>Payload</td>
            <td rowspan=1>Depends on Command</td>
        </tr>
    </tbody>
</table>

The basic format of the RSP packet returned by the device to the terminal is as follows:

<table>
    <thead>
        <tr>
            <th colspan=3>RSP Packet</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Response status</td>
            <td rowspan=1>0x01 - OK, Action executed<br>0x02 - ERROR, Error while executing an Action <br>0x03 - BUSY, Device is busy now, please try later <br>0x04 - Communication protocol version incompatible <br>0x05 - Device does not support this Command <br>0x06 - Device's battery is low<br>0x0D - This command is not supported in the current mode <br>0x0E - Disconnected from the device that needs to stay connected</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1-19</td>
            <td rowspan=1>Payload</td>
            <td rowspan=1>Depends on the Command to reply</td>
        </tr>
    </tbody>
</table>


## 0x02 Get the basic information of the device command

- Used to obtain basic information about the device.
- REQ Packet Payload is 0.
- The Status byte of the RSP packet after successful execution is 0x01, and the Payload is:

<table>
    <thead>
        <tr>
            <th colspan=3>RSP Packet Payload</th>
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
            <td rowspan=1>Chain Length</td>
            <td rowspan=1>Device Chain Length</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 3</td>
            <td rowspan=1>State 1</td>
            <td rowspan=1>bit7-direction, 0: default (open to the left), 1: reverse <br>bit6-touch and go, 0: disable, 1: enable <br>bit5-lighting effect, 0: disable, 1: enable <br>bit4-reserved <br>bit3-fault, 0-none, 1-faulty <br>bit2:0-reserved</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 4</td>
            <td rowspan=1>State 2</td>
            <td rowspan=1>bit3-Whether solar panel is plugged in, 0-No, 1-There is solar panel <br>bit2-calibrated, 0-not calibrated, 1-calibrated <br>bit1:0-motion status, 0-static, 1-open window, 2-close window</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 5</td>
            <td rowspan=1>Position</td>
            <td rowspan=1>Current location of the device (%)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 6</td>
            <td rowspan=1>Timer Amount</td>
            <td rowspan=1>Number of timers</td>
        </tr>
    </tbody>
</table>
<a><img src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/curtain3-0x02.jpeg"></a>

## 0x0F extended command

### 0x45 Curtain settings command

- Set Curtain's unique settings.

<table>
    <tbody>
        <tr>
            <td rowspan=1>Function code</td>
            <td rowspan=1>Function parameter code</td>
            <td rowspan=1>REQ Packet Payload</td>
          	<td></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=1>Byte: 3</td>
        </tr>
        <tr>
            <td rowspan=1>0x01-Action</td>
            <td rowspan=1>0x05-unified action of device chain</td>
            <td rowspan=1>0x00: high speed, 0x01: low speed</td>
            <td rowspan=1>The distance between the last position of the device and the starting position (%)</td>
        </tr>
    </tbody>
</table>
- REQ Packet returns different values with different commands.

<table>
    <thead>
        <tr>
            <th colspan=3>REQ Packet</th>
            <th colspan=3>RSP Packet</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Functioncode</td>
            <td rowspan=1>Function parameter code</td>
            <td rowspan=1>Payload Byte: 0</td>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>Byte: 2</td>
        </tr>
        <tr>
            <td rowspan=1>0x01-Action</td>
            <td rowspan=1>-</td>
            <td rowspan=1>-</td>
            <td rowspan=1>Command Status</td>
            <td rowspan=1>Device 0's current position in percentage</td>
            <td rowspan=1>Device 1's current position in percentage</td>
        </tr>
    </tbody>
</table>

<a><img src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/curtain3-0x45.jpeg"></a>

### 0x46 Curtain get command

- Get Curtain's unique settings.
- Action mode A: 0-performance mode, 1-silent mode, other-invalid value, the default invalid value is 1 for all bit values.
- Charging status A:
    - 0: Not charging
    - 1: Adapter charging
    - 2: Solar panel charging
    - 3: The adapter connection is full
    - 4: The solar panel connection is full
    - 5: The solar panel is connected and not charged when it is not fully charged
    - 6: Hardware error

<table>
<thead>
  <tr>
    <th colspan=1>Function Code</th>
    <th colspan=1>Function Parameter Code</th>
    <th colspan=2>REQ Packet Payload</th>
    <th colspan=8>RSP Packet</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td rowspan=1>Byte: 0</td>
    <td rowspan=1>Byte: 1</td>
    <td rowspan=1>Byte: 2</td>
    <td rowspan=1>Byte: 3</td>
    <td rowspan=1>Byte: 0</td>
    <td rowspan=1>Byte: 1</td>
    <td rowspan=1>Byte: 2</td>
    <td rowspan=1>Byte: 3</td>
    <td rowspan=1>Byte: 4</td>
    <td rowspan=1>Byte: 5</td>
    <td rowspan=1>Byte: 6</td>
    <td rowspan=1>Byte: 7</td>
  </tr>
  <tr>
    <td rowspan=2>0x04-Basic attributes</td>
    <td rowspan=1>0x01-Summary information</td>
    <td rowspan=1></td>
    <td rowspan=1></td>
    <td rowspan=3>Command Status</td>
    <td rowspan=1>Device 0 bit7-direction, 0: default, 1: reverse bit6-touch and go, 0: disable, 1: enable bit5-light sensor, 0: disable, 1: enable bit4-reserved bit3-window mode, 0-window to the left, 1-window to the right bit2:0-reserved</td>
    <td rowspan=1>Device 1 bit7-direction, 0: default (open to the left), 1: reverse bit6-touch and go, 0: disable, 1: enable bit5-light sensor 0: disable, 1: enable bit4-reserved bit3-window mode, 0-open window to the left, 1-open window to the right bit2:0-reserved</td>
    <td rowspan=1></td>
    <td rowspan=1></td>
    <td rowspan=1></td>
    <td rowspan=1></td>
    <td rowspan=1></td>
	</tr>
  <tr>
    <td rowspan=1>0x02-Advanced page</td>
    <td rowspan=1></td>
    <td rowspan=1></td>
    <td rowspan=1>Device 0 battery power</td>
    <td rowspan=1>Device 0 Firmware version</td>
    <td rowspan=1>Device 0 State of charge A</td>
    <td rowspan=1>Device 1 battery power</td>
    <td rowspan=1>Device 1 Firmware version</td>
    <td rowspan=1>Device 1 State of charge A</td>
    <td rowspan=1></td>
	</tr>
  <tr>
    <td rowspan=1>0x81-Command status</td>
    <td rowspan=1>0x01-Query the basic status of the device chain</td>
    <td rowspan=1></td>
    <td rowspan=1></td>
    <td rowspan=1>bit7-reserved bit6-delay action 0-none, 1-yes bit5:4-device chain head movement status, 0-static, 1-open window, 2-close window, bit3:0-the number of light sensing actions</td>
    <td rowspan=1>bit7:4-device chain head action mode A bit3:0-the number of device chain head timers</td>
    <td rowspan=1>Device chain length</td>
    <td rowspan=1>bit7-solar panel connected or not, 0-not connected, 1- connected; bit6:0-device 0's current location</td>
    <td rowspan=1>bit7-charing or not, 0-not charging, 1-charging; bit6:0-device 0's current battery level</td>
    <td rowspan=1>bit7-solar panel connected or not, 0-not connected, 1- connected; bit6:0-device 1's current location</td>
    <td rowspan=1>bit7-charing or not, 0-not charging, 1-charging; bit6:0-device 1's current battery level</td>
  </tr>
</tbody>
</table>

<a><img src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/curtain3-0x46-1.jpeg"></a>

<a><img src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/curtain3-0x46-2.jpeg"></a>

<a><img src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/curtain3-0x46-3.jpeg"></a>


CopyRight@2024 SWITCHBOT INC

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)