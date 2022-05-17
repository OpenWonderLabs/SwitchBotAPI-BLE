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

  - This broadcast message defines the Service data of Scan Rsp of the
    specific Device.

<!-- end list -->

  - The length of the Service data is different based on Device Type.
    The Service data can be 8 bytes max. The Byte: 0, Byte: 1 and Byte:
    2 are for every Device Type. The bytes start from Byte: 3 depends on
    different Device Type. Please refer to Device Type definition in the
    table below.
  - Broadcast modes are defined as:
      - SwitchBot Mode (Default)
      - Simple Mode
      - iBeacon Mode

<table>
    <thead>
        <tr>
            <th colspan=4>Device Types</th>
        </tr>
        <tr>
            <th rowspan=1>Product Name</th>
            <th rowspan=1>HEX</th>
            <th rowspan=1>ASCII</th>
            <th rowspan=1>Note</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>SwitchBot Bot (WoHand)</td>
            <td rowspan=1>0x48</td>
            <td rowspan=1>'H'</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>WoButton</td>
            <td rowspan=1>0x42</td>
            <td rowspan=1>'B'</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=2>SwitchBot Hub (WoLink)</td>
            <td rowspan=1>0x4C</td>
            <td rowspan=1>'L'</td>
            <td rowspan=1>'Add Mode'</td>
        </tr>
        <tr>
            <td rowspan=1>0x6C</td>
            <td rowspan=1>'l'</td>
            <td rowspan=1>'Normal Mode'</td>
        </tr>
        <tr>
            <td rowspan=2>SwitchBot Hub Plus (WoLink Plus)</td>
            <td rowspan=1>0x50</td>
            <td rowspan=1>'P'</td>
            <td rowspan=1>'Add Mode'</td>
        </tr>
        <tr>
            <td rowspan=1>0x70</td>
            <td rowspan=1>'p'</td>
            <td rowspan=1>'Normal Mode'</td>
        </tr>
        <tr>
            <td rowspan=2>SwitchBot Fan (WoFan)</td>
            <td rowspan=1>0x46</td>
            <td rowspan=1>'F'</td>
            <td rowspan=1>'Add Mode'</td>
        </tr>
        <tr>
            <td rowspan=1>0x66</td>
            <td rowspan=1>'f'</td>
            <td rowspan=1>'Normal Mode'</td>
        </tr>
        <tr>
            <td rowspan=2>SwitchBot MeterTH (WoSensorTH)</td>
            <td rowspan=1>0x74</td>
            <td rowspan=1>'t'</td>
            <td rowspan=1>'Add Mode'</td>
        </tr>
        <tr>
            <td rowspan=1>0x54</td>
            <td rowspan=1>'T'</td>
            <td rowspan=1>'Normal Mode'</td>
        </tr>
        <tr>
            <td rowspan=2>SwitchBot Mini (HubMini)</td>
            <td rowspan=1>0x4D</td>
            <td rowspan=1>'M'</td>
            <td rowspan=1>'Add Mode'</td>
        </tr>
        <tr>
            <td rowspan=1>0x6D</td>
            <td rowspan=1>'m'</td>
            <td rowspan=1>'Normal Mode'</td>
        </tr>
    </tbody>
</table>

Device Type

### Broadcast Message Format

#### (Old) Broadcast Message

Use this for firmware version before Bot v30, Remote v20 and Hub v6.

**Advertise:**

  - Manufacturer data: includes device's company ID and MAC address
    (support WeChat protocol)

**Scanrsp:**

  - Device Name: "Bot", "Remote", "Hub", "WoSensorTH"
  - UUID: *cba20d00-224d-11e6-9fb8-0002a5d5c51b* and *fee7*

  
### (New) Broadcast Message
The length of the Service data is different based on Device Type. The Service data can be 8 bytes max.

The bit\[6:0\] in Byte: 0 of the broadcast message is Device Type. The
Byte: 0, Byte: 1 and Byte: 2 are for every Device Type. The bytes start
from Byte: 3 depends on different Device Type, serving as general
registers.

The backend also needs a mapping table of the Service data, so both the
backend and app can parse the Service data.

<table>
    <thead>
        <tr>
            <th colspan=4>Service data</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=2>Byte: 0</td>
            <td rowspan=2>Enc Type Dev Type</td>
            <td rowspan=1>Bit[7] - Reserved When Enc type - Byte: 1 Bit[5] = 0
     
- 0: no encryption

- 1: encryption algorithm 1 (the same as the Bot)

When Enc type - Byte: 1 Bit[5] = 1
  - 0: encryption algorithm 2 (TBD)
  - 1: encryption algorithm 3 (TBD)</td>
            <td rowspan=1></td>
        </tr>
        <tr>
          <td rowspan=1>Bit[6:0] - Device Type Please refer to the Device Type table above.</td>
          <td rowspan=1></td>
        </tr>
        <tr>
          <td rowspan=5>Byte: 1</td>
          <td rowspan=5>Status</td>
          <td rowspan=1>Bit[7] - Switchbot Mode

    - 0: one state mode
    - 1: on/off state mode</td>
        </tr>
        <tr>
          <td rowspan=1>Bit[6] - State
        
      - 0: on
      - 1: off</td>
            <td rowspan=1></td>
        </tr>
        <tr>
          <td rowspan=1>Bit[5] - Enc type
      - 0: encryption algorithm (the same as the Bot)
      - 1: encryption algorithm 2 or 3 (TBD)

Please refer to Byte: 0 Bit[7]</td>
          <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Bit[4] - Data Commit Flag

- 0: service data no data update
- 1: service data has data update

The flag bit needs to be cleared by the Hub or App after the data is successfully updated, and the Device itself will not actively clear this flag.</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Bit[3] – Group D Bit[2] – Group C Bit[1] – Group B Bit[0] – Group A
- 0: the Device is not in this group
- 1: the Device is in this group</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 2</td>
            <td rowspan=2>Update UTC Flag Battery</td>
            <td rowspan=1>Bit[7] – Sync UTC
    - 0: no need to synchronize
    - 1: the Device has passed a time synchronization cycle (10 days by default) and requires a time synchronization

The Device uses UTC time when synchronizing. The Device will actively clear this flag after the Hub or App synchronize successfully.</td>
            <td rowspan=1></td>
        </tr>
        <tr>
          <td rowspan=1>Bit[6:0] - Remaining Battery 0~100%</td>
          <td rowspan=1></td>
        </tr>
    </tbody>
</table>

### Broadcast Mode

#### SwitchBot Mode (Default)

**Advertise:**

  - Manufacturer data: include device's company ID (0x0059 Nordic) and
    MAC address (support WeChat protocol)
  - BLE\_ADVDATA\_NO\_NAME

**Scanrsp:**

  - UUID: *cba20d00-224d-11e6-9fb8-0002a5d5c51b* and *fee7*
  - BLE\_ADVDATA\_NO\_NAME

**Example**:

<img alt="broadcast_1" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/broadcast_1.png"></a>
<img alt="broadcast_2" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/broadcast_2.png"></a>
  
### Simple Mode
**Advsertise:**

  - BLE\_ADVDATA\_NO\_NAME

**Scanrsp:**

  - UUID (cba20d00-224d-11e6-9fb8-0002a5d5c51b)
  - BLE\_ADVDATA\_NO\_NAME

### iBeacon Mode (if you need welcome function)

**Advertise:**

Standard iBeacon package

  - UUID:cba20d00-224d-11e6-9fb8-0002a5d5c51b
  - Major ID:
  - High Byte: bit 7:4 - beacon type
  - Bit\[3:0\] - beacon data
  - Low Byte: beacon data
  - Minor ID:beacon data
  - TX power: 0xc3
  - BLE\_ADVDATA\_NO\_NAME

<table>
    <thead>
        <tr>
            <th colspan=4>beacon data instruction</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Beacon Type</td>
            <td rowspan=1>Major ID High byte bit[3:0]</td>
            <td rowspan=1>Major ID Low byte</td>
            <td rowspan=1>Minor ID High byte</td>
            <td rowspan=1>Minor ID Low byte</td>
        </tr>
        <tr>
            <td rowspan=1>0</td>
            <td rowspan=1>Alert</td>
            <td rowspan=1>MAC [3]</td>
            <td rowspan=1>MAC [4]</td>
            <td rowspan=1>MAC [5]</td>
        </tr>
    </tbody>
</table>

**Scanrsp:**

  - UUID(*cba20d00-224d-11e6-9fb8-0002a5d5c51b*)
  - BLE\_ADVDATA\_NO\_NAME

### BLE Communication Data Message Basic Format

  - The controlling terminal (short as the Terminal below) and the
    configured device (short as the Device below) use BLE to communicate
    with each other wirelessly. During the communication, the Terminal
    acts as a central device, while the Device acts as peripheral
    device. The Terminal gets basic device info by reading the broadcast
    message of the Device. They exchange data by using read and write
    characteristic of communication service.

<!-- end list -->

  - All bilateral communication is after the BLE connection established.
    The Terminal send a REQ message to the Device, and then the Device
    returns a RESP message.

<!-- end list -->

  - All communication message data length is 1-20 bytes, i.e. MTU=20.
    There are 2 types of REQ messages sending from the Terminal to the
    Device, encrypted and unencrypted.

<!-- end list -->

  - **Communication service UUID:**
      - *cba20d00-224d-11e6-9fb8-0002a5d5c51b*
      - UUID TYPE:Vendor UUID types start at this index (128-bit)

<!-- end list -->

  - **RX characteristic UUID of the message from the Terminal to the
    Device:**
      - UUID: *cba20002-224d-11e6-9fb8-0002a5d5c51b*
      - UUID TYPE: Vendor UUID types start at this index (128-bit)
      - Char Attribute: RW
      - Char Properties: notify      

<!-- end list -->

  - **TX  characteristic UUID of the message from the Device to the
    Terminal:**
      - *cba20003-224d-11e6-9fb8-0002a5d5c51b*
      - UUID TYPE: Vendor UUID types start at this index (128-bit)
      - Char Attribute: RW

All communication message data length is 1-20 bytes, i.e. MTU=20.

The REQ data from the Terminal to the Device is as below:

<table>
    <thead>
        <tr>
            <th colspan=4>REQ message</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Magic Number</td>
            <td rowspan=1>0x57 - (fixed value)</td>
        </tr>
        <tr>
            <td rowspan=3>Byte: 1</td>
            <td rowspan=3>Header</td>
            <td rowspan=1>Bit [7:6] - Version 0 - (fixed value)</td>
        </tr>
        <tr>
            <td rowspan=1>Bit [5:4] - Encryption Mode 0 - no encryption</td>
        </tr>
        <tr>
            <td rowspan=1>Bit [3:0] - Command 0x01 - Execute an Action 0x02 - Get Device Basic Info 0x03 - Set Device Basic Info 0x08 - Get Device Time Management Info 0x09 - Set Device Time Management Info 0x0f – Enable command_ext in Byte: 2</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=1>Payload or command_ext depend on command</td>
            <td rowspan=1>if command == 0x0f{ command_ext : 0x08 - Set long press duration } else{ Payload }</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 3-16</td>
            <td rowspan=1>Payload</td>
            <td rowspan=1>Depends on Command</td>
        </tr>
    </tbody>
</table>

Payload complement the data info to the Commands. The Payload format
varies for different Commands.

The basic format of RESP message from the Terminal to the Device:

<table>
    <thead>
        <tr>
            <th colspan=4>RESP message</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Response status</td>
            <td rowspan=1>0x01 - OK Action executed 0x02 - ERROR Error while executing an Action 0x03 - BUSY Device is busy now, please try later 0x04 - Communication protocol version incompatible 0x05 - Device does not support this Command 0x06 - Device low battery 0x07 - Device is encrypted 0x08 - Device is unencrypted 0x09 - Password error 0x0A - Device does not support this encription method 0x0B - Failed to locate a nearby mesh Device 0x0C - Failed to connect to the network</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1-19</td>
            <td rowspan=1>Payload</td>
            <td rowspan=1>Depends on the Command to reply</td>
        </tr>
    </tbody>
</table>

Status returns the handling result of the Device to the earlier command,
which is the 1st Byte of every RESP message, and also mandatory.

Payload complement the data info to the Commands. The data length is
0-19 bytes depends on different cases, and can also be omitted.

The format of the Payload is up to the Command type it is responding and
the handling result.

  
### 0x01 Execute an Action

  - By sending this command, the Bot will complete a major action:
  - Bot: Execute an action of moving the arm to press down and then move
    it back.
  - If executed successfully, the Status Byte of the RESP message will
    be 1.

<table>
    <thead>
        <tr>
            <th colspan=4>REQ message payload</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Action control</td>
            <td rowspan=1>0x0 - push and pull back 0x1 - light switch on 0x2 - light switch off 0x3 - push stop 0x4 - back</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 1-2</td>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>Time Internal (second)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=1>0x0 - push and pull back 0x1 - light switch on 0x2 - light switch off 0x3 - push stop 0x4 - back</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 3-4</td>
            <td rowspan=1>Byte: 3</td>
            <td rowspan=1>Time Internal (second)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 4</td>
            <td rowspan=1>0x0 - push and pull back 0x1 - light switch on 0x2 - light switch off 0x3 - push stop 0x4 - back</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 5-6</td>
            <td rowspan=1>Byte: 5</td>
            <td rowspan=1>Time Internal (second)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 6</td>
            <td rowspan=1>0x0 - push and pull back 0x1 - light switch on 0x2 - light switch off 0x3 - push stop 0x4 - back</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 7-8</td>
            <td rowspan=1>Byte: 7</td>
            <td rowspan=1>Time Internal (second)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 8</td>
            <td rowspan=1>0x0 - push and pull back 0x1 - light switch on 0x2 - light switch off 0x3 - push stop 0x4 - back</td>
        </tr>
        <tr>
            <td rowspan=2>Byte:9-10</td>
            <td rowspan=1>Byte: 9</td>
            <td rowspan=1>Time Internal (second)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 10</td>
            <td rowspan=1>0x0 - push and pull back 0x1 - light switch on 0x2 - light switch off 0x3 - push stop 0x4 - back</td>
        </tr>
        <tr>
            <td rowspan=2>Byte:11-12</td>
            <td rowspan=1>Byte: 11</td>
            <td rowspan=1>Time Internal (second)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 12</td>
            <td rowspan=1>0x0 - push and pull back 0x1 - light switch on 0x2 - light switch off 0x3 - push stop 0x4 - back</td>
        </tr>
        <tr>
            <td rowspan=2>Byte:13-14</td>
            <td rowspan=1>Byte: 13</td>
            <td rowspan=1>Time Internal (second)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 14</td>
            <td rowspan=1>0x0 - push and pull back 0x1 - light switch on 0x2 - light switch off 0x3 - push stop 0x4 - back</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 15</td>
            <td rowspan=1>Encrypted Mode</td>
            <td rowspan=1>payload last byte, reserved</td>
        </tr>
        <tr>
            <td rowspan=1>Unencrypted Mode</td>
            <td rowspan=1>Time Internal (second)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 16 Not existed for Encrypted Mode</td>
            <td rowspan=1>Unencrypted Mode</td>
            <td rowspan=1>0x0 - push and pull back 0x1 - light switch on 0x2 - light switch off 0x3 - push stop 0x4 - back</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 17</td>
            <td rowspan=1>Not existed for Encrypted Mode</td>
            <td rowspan=1>This is the last byte of payload in Unencrypted Mode</td>
        </tr>
    </tbody>
</table>

Note: Byte: every 2 bytes are the same group for byte 1-16. The odd byterepresents the  time interval of the coming action to the previous one, the even byte means the action to be executed in the action list. The time internal should not be set as 0, because the first time interval of 0 means the end of the action list. Regardless the value of Byte 17, the action list never stop.

**Example**:

In current firmware version, Terminal send Device a message of "0x57
0x01 0x00" to control the Bot to move the arm.

The Status Byte of the RESP message will be 1 when executed
successfully, and the Payload will be like:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP message payload</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Running Information</td>
            <td rowspan=1>Return the running information to controller for analyzing</td>
        </tr>
    </tbody>
</table>

Use NRF Connect to issue command "0x570100" to the Device, the Bot arm moves.

Note:

When the Device is in the Add-on mode，the replied RESP message will be 0x0548C0, Status is 0x05 Device does not support this Command.

When the Device is not in the Add-on mode, i.e the normal press mode, the replied RESP message will be 0x01FF00, Status is 0x01 OK Action executed.

<img alt="Execute_1" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/Execute_1.png"></a>
<img alt="Execute_2" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/Execute_2.png"></a>
<img alt="Execute_3" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/Execute_3.png"></a>

### 0x02 Get Device Basic Info

  - This command is used to get the basic info of a Device:
  - REQ message payload is 0.
  - If executed successfully, the Status Byte of the RESP message will
    be 1. The payload is the Device basic info:
      - This command is used to get the basic info of a SwitchBot (the
        Bot), remaining battery, firmware version, number of timers, act
        mode, hold-and-press timer, etc.

REQ message payload is 0.

<table>
    <thead>
        <tr>
            <th colspan=1></th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>REQ message payload: 0</td>
        </tr>
    </tbody>
</table>

If executed successfully, the Status Byte of the RESP message will be 0,
payload format is as below:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP message payload</th>
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
            <td rowspan=1>NC</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 3-4</td>
            <td rowspan=1>NC</td>
            <td rowspan=1>NC</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 5-6</td>
            <td rowspan=1>NC</td>
            <td rowspan=1>NC</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 7</td>
            <td rowspan=1>Timer Num</td>
            <td rowspan=1>The number of Timer</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 8</td>
            <td rowspan=1>Act Mode</td>
            <td rowspan=1>The act mode of Bot</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 9</td>
            <td rowspan=1>Hold Times</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 10</td>
            <td rowspan=1>Service data byte 0</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 11</td>
            <td rowspan=1>Service data byte 1</td>
            <td rowspan=1></td>
        </tr>
    </tbody>
</table>

**Example**:

Use NRF Connect to issue command 0x5702 to the Device and get relevant
info.

Replied RESP message is 01642C64000000A10000004800

Status Byte is 0x01 OK Action executed;

RESP message payload Byte 0 is 0x64: 100% remaining battery.

RESP message payload Byte 1 is 0x2C: 44\*0.1=4.4 Firmware Version;

RESP message payload Byte 2 is 0x64: 100 The strength to push button;

RESP message payload Byte 3-4 is 0x00 0x00: The ADC value read from
sensor;

RESP message payload Byte 5-6 is 0x00 0xA1: The motor calibration value;

RESP message payload Byte 7 is 0x00: The number of Timer is 0;

RESP message payload Byte 8 is 0x00: The act mode of Bot: Not Add-on
Mode, i.e. normal press mode;

RESP message payload Byte 9 is 0x00: Hold-and-press Times;

RESP message Payload Byte 10 is 0x48: service data Byte 0;

RESP message Payload Byte 11 is 0x00: service data Byte 1;

<img alt="get_info_1" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/get_info_1.png"></a>

### 0x03 Set Device Basic Info

  - This command is used to set basic info of a Device:
  - REQ message payload is 2.
      - This command could be used to set SwitchBot act mode.
  - If executed successfully, the Status Byte of the RESP message will be 1. The payload is 2:

<table>
    <thead>
        <tr>
            <th colspan=3>REQ message payload:</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>NC</td>
            <td rowspan=1>The App must always give a value of 100.</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>act mode</td>
            <td rowspan=1>The mode of Bot. Bit[7:4] Bot mode:0 - One Bot can only press one button. 1 - One Bot can control two state of switch. Bit[3:0] Bot inverse 0 - Act para 0x01 is push for turn on. Act para 0x02 is pull for turn off.     1 - Act para 0x01 is push for turn off. Act para 0x02 is pull for turn on.     Inverse won’t affect act para 0x00. Eg. 0x10 is two state switch mode, and push for turn on, pull for turn off.</td>
        </tr>
    </tbody>
</table>

If executed successfully, the Status Byte of the RESP message will be 1.
The payload is 2:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP message status: 1</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Status</td>
            <td rowspan=1></td>
        </tr>
    </tbody>
</table>

<table>
    <thead>
        <tr>
            <th colspan=3>RESP message payload:</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>NC</td>
            <td rowspan=1></td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>Bot: Act mode</td>
            <td rowspan=1>The act mode of Bot</td>
        </tr>
    </tbody>
</table>

**Example**:

Use NRF Connect to issue command 0x57036310 to a Device, set the Device
as Add-on mode. If the Device (i.e. the Bot) was not in Add-on mode, the
Bot arm will moves accordingly once mode set.

Replied RESP message is 0x016300;

Status Byte is 0x01 OK Action executed

Then use NRF Connect to issue a command 0x5702 to the Device and get
some basic setting info.

The replied RESP message is, let's say, 01642C64000000A10000004800;

Status Byte is 0x01 OK Action executed

RESP message payload Byte 2 is 0x63: 99 The strength to push button;

RESP message payload Byte 8 is 0x10: The act mode of Bot: Add-on Mode;

<img alt="set_info_1" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/set_info_1.png"></a>
<img alt="set_info_2" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/set_info_2.png"></a>

### 0x08 Get Device Time Management Info

  - Used to get time management info, including current time, the number
    of alarms, the time of the alarms.
  - REQ message payload is 1 (3 Subcmd: get current time, get the number
    of alarms, get the alarm time).
  - If executed successfully, the Status Byte of the RESP message will
    be1，payload is up to Subcmd type.

REQ message payload:

<table>
    <thead>
        <tr>
            <th colspan=3>REQ message payload:</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Subcmd</td>
            <td rowspan=1>Indicates what to obtain from the target Device 0x01: Device current time 0x02: the number of alarm tasks the Device has 0xn3(n=0..4): what is the Nth task</td>
        </tr>
    </tbody>
</table>

If executed successfully, the Status Byte of the RESP message will be 0. For different subcmd, the Payload will be:

Subcmd=1:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP message payload:</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0-7</td>
            <td rowspan=1>Current system timestamp</td>
            <td rowspan=1>The current Unix timestamp in big endian</td>
        </tr>
    </tbody>
</table>

Subcmd=2:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP message payload:</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Num</td>
            <td rowspan=1>Total number of tasks</td>
        </tr>
    </tbody>
</table>

Subcmd=0xn3(n=0..4) what is the Nth task:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP message payload:</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Num</td>
            <td rowspan=1>Total number of tasks</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>Index</td>
            <td rowspan=1>Task index number</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=1>Repeat</td>
            <td rowspan=1>Repeating method: Bit [7]: repeating mode setting 1 - execute 1 time 0 - execute repeatedly Bit [6:0]: Sun to Mon   1 - valid for this date   0 - invalid for this date</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 3</td>
            <td rowspan=1>Time of HH</td>
            <td rowspan=1>HH</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 4</td>
            <td rowspan=1>Time of MM</td>
            <td rowspan=1>MM</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 5</td>
            <td rowspan=1>Action mode</td>
            <td rowspan=1>0 - Execute the job at HH:MM in a repeating method. 1 - Execute the job at HH:MM in a repeating method, and repeat Sum times at the interval of Interval. 2 - Execute the job at HH:MM in a repeating method, and repeat forever at the interval of Interval.</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 6</td>
            <td rowspan=1>Job</td>
            <td rowspan=1>What to do when time is up. 0 - Action 1 - On 2 - Off</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 7</td>
            <td rowspan=1>Sum</td>
            <td rowspan=1>The total times of the continuous actions executed by the timer</td>
        </tr>
        <tr>
            <td rowspan=3>Byte: 8-10</td>
            <td rowspan=3>Interval: the interval between the continuous actions for the timer</td>
            <td rowspan=1>HH (Max. 5)</td>
        </tr>
        <tr>
            <td rowspan=1>MM</td>
        </tr>
        <tr>
            <td rowspan=1>SS (in step of 10)</td>
        </tr>
    </tbody>
</table>

**Example**:

Use NRF Connect to issue command 0x570802 to the Device, get the number
of alarms.

The replied RESP message is 0x0103;

Status Byte is 0x01 OK Action executed

RESP message payload Byte 0 is 0x03: Total number of tasks;

<img alt="get_time_1" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/get_time_1.png"></a>

### 0x09 Set Device Time Management Info

  - Used to set Device time management info, including current time, the
    number of alarms, the time of the alarms.
  - Always set all 5 build-in timers simultaneously when setting alarm
    time.
  - REQ message payload depends on different Subcmd (3 types of Subcmd:
    set current time, set the number of alarms, set time of the alarms).
  - If executed successfully, the Status Byte of the RESP message will
    be 1, payload will be 0.

There are 3 formats for REQ message payload:

1\.

<table>
    <thead>
        <tr>
            <th colspan=3>REQ message payload:</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Subcmd</td>
            <td rowspan=1>0x01: device current times</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1-8</td>
            <td rowspan=1>Current system timestamp</td>
            <td rowspan=1>The current Unix timestamp in big endian</td>
        </tr>
    </tbody>
</table>

2\.

<table>
    <thead>
        <tr>
            <th colspan=3>REQ message payload:</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Subcmd</td>
            <td rowspan=1>0x02: the number of alarm tasks the Device has</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>Num</td>
            <td rowspan=1>Total number of tasks</td>
        </tr>
    </tbody>
</table>

3\.

<table>
    <thead>
        <tr>
            <th colspan=3>REQ message payload:</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Subcmd</td>Rev
            <td rowspan=1>0xn3(n=0..4): set the Nth task</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>Num</td>
            <td rowspan=1>Total number of tasks</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=1>Index</td>
            <td rowspan=1>Rev</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 3</td>
            <td rowspan=1>Repeat</td>
            <td rowspan=1>Repeating method: Bit [7]: repeating mode setting 1 - execute 1 time 0 - execute repeatedly Bit [6:0]: Sun to Mon   1 - valid for this date   0 - invalid for this date</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 4</td>
            <td rowspan=1>Time of HH</td>
            <td rowspan=1>HH</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 5</td>
            <td rowspan=1>Time of MM</td>
            <td rowspan=1>MM</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 6</td>
            <td rowspan=1>Action mode</td>
            <td rowspan=1>0 - Execute the job at HH:MM in a repeating method. 1 - Execute the job at HH:MM in a repeating method, and repeat Sum times at the interval of Interval. 2 - Execute the job at HH:MM in a repeating method, and repeat forever at the interval of Interval.</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 7</td>
            <td rowspan=1>Job</td>
            <td rowspan=1>What to do when time is up. 0 - Action 1 - On 2 - Off</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 8</td>
            <td rowspan=1>Sum</td>
            <td rowspan=1>The total times of the continuous actions executed by the timer</td>
        </tr>
        <tr>
            <td rowspan=3>Byte: 9-11</td>
            <td rowspan=3>Sum</td>
            <td rowspan=1>HH (Max. 5)</td>
        </tr>
        <tr>
            <td rowspan=1>MM</td>
        </tr>
        <tr>
            <td rowspan=1>SS (in step of 10)</td>
        </tr>
    </tbody>
</table>

If executed successfully, the Status Byte of the RESP message will be 1,
payload will be 0.

<table>
    <thead>
        <tr>
            <th colspan=3>RESP message status: 1</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Status</td>
            <td rowspan=1></td>
        </tr>
    </tbody>
</table>

<table>
    <thead>
        <tr>
            <th colspan=3>RESP message payload: 0</th>
        </tr>
    </thead>
    <tbody>
    </tbody>
</table>

**Example**:

Use NRF Connect to issue command 0x57090203 to a Device, and set 3
alarms to the Device.

Replied RESP message is 0x01;

Status Byte is 0x01 OK Action executed

<img alt="set_time_1" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/set_time_1.png"></a>

### 0x0f Extended Command

#### **0x08 Set long press duration**

  - To set the duration (in second) for SwitchBot arm to long press
  - REQ Packet payload: 1
  - If executed successfully, the Status Byte of the RESP message will
    be 1, payload will be 0.

REQ Packet payload: 1

<table>
    <thead>
        <tr>
            <th colspan=3>REQ Packet payload:</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Time</td>
            <td rowspan=1></td>
        </tr>
    </tbody>
</table>

If executed successfully, the Status Byte of the RESP message will be 1,
payload will be 0.

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

**Example**:

Use NRF Connect to send a command 0x570f0803 to a Device, and set the
long press duration to 3 seconds.

If the received RESP packet is 0x01

Replied RESP message is 0x01, meaning a successful setting.

<img alt="set_long_press_1.png" src="https://switchbot-open-api.s3.amazonaws.com/Bot+open+api/set_long_press_1.png"></a>

CopyRight@2022 Wonderlabs, Inc.

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)