## Curtain BLE open API

- [Curtain Broadcast Package](#curtain-broadcast-package)
- [Basic format of BLE communication data packet](#basic-format-of-ble-communication-data-packet)
- [0x02 Get the basic information of the device command](#0x02-get-the-basic-information-of-the-device-command)
- [0x0F extended command](#0x0F-extended-command)
  - [0x45 Curtain setting command](#0x45-curtain-setting-command)
  - [0x46 Curtain get command](#0x46-curtain-get-command)


## Curtain Broadcast Package

- The following table is the Service Data of SCAN_RSP.

<table>
    <thead>
        <tr>
            <th colspan=3>Service data</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=2>Byte: 0</td>
            <td rowspan=1>Enc type</td>
            <td rowspan=1>Bit[7] NC</td>
        </tr>
        <tr>
            <td rowspan=1>Dev Type</td>
            <td rowspan=1>Bit [6:0] – Device Type ‘c’Const Adv Mode ‘C’Pair Mode</td>
        </tr>
        <tr>
            <td rowspan=3>Byte: 1</td>
            <td rowspan=3>Status</td>
            <td rowspan=1>Bit [7] – Whether to allow connection 1- allowed, 0- not allowed</td>
        </tr>
        <tr>
            <td rowspan=1>Bit [6] – Calibration situation 1-calibrated, 0-not calibrated, need to be calibrated</td>
        </tr>
        <tr>
            <td rowspan=1>Bit [5-0] NC</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 2</td>
            <td rowspan=1>Update utc flag</td>
            <td rowspan=1>Bit[7] NC</td>
        </tr>
        <tr>
            <td rowspan=1>Battery</td>
            <td rowspan=1>Bit [6:0] – Battery capacity 0~100%</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 3</td>
            <td rowspan=1>MotionState</td>
            <td rowspan=1>Bit [7] - Movement status, 0-stationary, 1-movement</td>
        </tr>
        <tr>
            <td rowspan=1>Position</td>
            <td rowspan=1>Bit [6:0] - The current position of the device (%)</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 4</td>
            <td rowspan=1>LightLevel</td>
            <td rowspan=1>Bit [7:4] - Light level (1-10)</td>
        </tr>
        <tr>
            <td rowspan=1>DeviceChain</td>
            <td rowspan=1>Bit [3:0] - Device chain length</td>
        </tr>
    </tbody>
</table>

<img alt="boardcast_1" src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/boardcast_1.png"></a>

## Basic format of BLE communication data packet
1. UUID related to communication.
    - Communication service UUID:

cba20d00-224d-11e6-9fb8-0002a5d5c51b    
UUID TYPE：Vendor UUID types start at this index (128-bit)

- RX characteristic UUID of the terminal sending data to the device:

cba20002-224d-11e6-9fb8-0002a5d5c51b    
UUID TYPE ：Vendor UUID types start at this index (128-bit)

Char Attribute ：RW

Char Properties： notify      

- TX characteristic UUID of the device sending data to the terminal:

cba20003-224d-11e6-9fb8-0002a5d5c51b    
UUID TYPE ：Vendor UUID types start at this index (128-bit)

Char Attribute ：RW

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
            <td rowspan=1>0x01 - OK, Action executed 0x02 - ERROR, Error while executing an Action 0x03 - BUSY, Device is busy now, please try later 0x04 - Communication protocol version incompatible 0x05 - Device does not support this Command 0x06 - Device is low power 0x0D - This command is not supported in the current mode 0x0E - Disconnected from the device that needs to stay connected</td>
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
            <td rowspan=1>bit7-direction, 0: default (open to the left), 1: reverse bit6-touch and go, 0: disable, 1: enable bit5-lighting effect, 0: disable, 1: enable bit4-reserved bit3-fault, 0-none, 1-faulty bit2:0-reserved</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 4</td>
            <td rowspan=1>State 2</td>
            <td rowspan=1>bit3-Whether solar panel is plugged in, 0-No, 1-There is solar panel bit2-calibrated, 0-not calibrated, 1-calibrated bit1:0-motion status, 0-static, 1-open window, 2-close window</td>
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
<img alt="5702_1" src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/5702_1.png"></a>

## 0x0F extended command
### 0x45 Curtain setting command

- Set Curtain's unique setting content.

<table>
    <thead>
        <tr>
            <th colspan=4></th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Function code</td>
            <td rowspan=1>Function parameter code</td>
            <td rowspan=1>REQ Packet Payload</td>
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
            <td rowspan=1>0xFF</td>
            <td rowspan=1>The distance between the final position of the device and the origin (%)</td>
        </tr>
        <tr>
            <td rowspan=2>0x03-Light sensor</td>
            <td rowspan=1>0x02-device chain light trigger source</td>
            <td rowspan=1>The serial number of the device where the photosensitive sensor is located (device identification B)</td>
            <td rowspan=1>0x0-Motherboard 0x01-Solar Panel</td>
        </tr>
        <tr>
            <td rowspan=1>0x03-delete all light sensing actions</td>
            <td rowspan=1></td>
            <td rowspan=1></td>
        </tr>
    </tbody>
</table>

- REQ Packet不同命令返回值不同。

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
            <td rowspan=1>Device 0 current percentage position</td>
            <td rowspan=1>Current percentage position of device 1</td>
        </tr>
    </tbody>
</table>

<img alt="570f450105ff32" src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/570f450105ff32.png"></a>

### 0x46 Curtain get command

- Get Curtain's unique setting content.
- Action mode A: 0-performance mode, 1-silent mode, other-invalid value, the default invalid value is 1 for all bit values.
- Charging status A:
    - 0:Not charging,
    - 1:Adapter charging,
    - 2:Solar panel charging,
    - 3:The adapter connection is full,
    - 4:The solar panel connection is full,
    - 5:The solar panel is connected and not charged when it is not fully charged.
    - 6:Hardware error.

<table>
    <thead>
        <tr>
            <th colspan=1>Function</th>
            <th colspan=1>Function</th>
            <th colspan=2>Function</th>
            <th colspan=6>Function</th>
        </tr>
    </thead>
    <tbody>
        <tr>
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
        </tr>
        <tr>
            <td rowspan=2>0x04-Basic attributes</td>
            <td rowspan=1>0x01-Summary information</td>
            <td rowspan=1></td>
            <td rowspan=3>Command Status</td>
            <td rowspan=1>Device 0 bit7-direction, 0: default, 1: reverse bit6-touch and go, 0: disable, 1: enable bit5-light sensor, 0: disable, 1: enable bit4-reserved bit3-window mode, 0-window to the left, 1-window to the right bit2:0-reserved</td>
            <td rowspan=1>Device 1 bit7-direction, 0: default (open to the left), 1: reverse bit6-touch and go, 0: disable, 1: enable bit5-light sensor 0: disable, 1: enable bit4-reserved bit3-window mode, 0-open window to the left, 1-open window to the right bit2:0-reserved</td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1></td>
        </tr>
            <td rowspan=1>0x02-Advanced page</td>
            <td rowspan=1></td>
            <td rowspan=1>Device 0 battery power</td>
            <td rowspan=1>Device 0 Firmware version</td>
            <td rowspan=1>Device 0 State of charge A</td>
            <td rowspan=1>Device 1 battery power</td>
            <td rowspan=1>Device 1 Firmware version</td>
            <td rowspan=1>Device 1 State of charge A</td>
        </tr>
        <tr>
            <td rowspan=1>0x01-Query the basic status of the device chain</td>
            <td rowspan=1></td>
            <td rowspan=1></td>
            <td rowspan=1>bit7-reserved bit6-delay action 0-none, 1-yes bit5:4-device chain head movement status, 0-static, 1-open window, 2-close window, bit3:0-the number of light sensing actions</td>
            <td rowspan=1>bit7:4-device chain head action mode A bit3:0-the number of device chain head timers</td>
            <td rowspan=1>Device chain length</td>
            <td rowspan=1>bit7- whether to insert solar panel, 0-no, 1- insert solar panel; bit6:0-sequence number 0 device current location</td>
            <td rowspan=1>bit7-whether to charge, 0-no, 1-charging; bit6:0-serial number 0 device current battery power</td>
            <td rowspan=1>bit7- whether to insert solar panel, 0-no, 1- insert solar panel; bit6:0-sequence number 1 current location of the device</td>
        </tr>
    </tbody>
</table>

<img alt="570f460401" src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/570f460401.png"></a>

<img alt="570f460402" src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/570f460402.png"></a>

<img alt="570f468101" src="https://switchbot-open-api.s3.amazonaws.com/Curtain+BLE+open+api/570f468101.png"></a>


CopyRight@2022 Wonderlabs, Inc.

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)