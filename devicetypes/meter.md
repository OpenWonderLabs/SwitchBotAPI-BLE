## Meter BLE open API

- [Meter Broadcast Message](#meter-broadcast-message)
- [Meter Broadcast Message Format](#meter-broadcast-message-format)
    - [(Old) Broadcast Message](#(old)-broadcast-message)
    - [(New) Broadcast Message](#(new)-broadcast-message)
- [Broadcast Mode](#broadcast-mode)
    - [SwitchBot Mode (Default)](#switchBot-mode-(default))
    - [Simple Mode](#simple-mode)
    - [iBeacon Mode (if you need welcome function)](#ibeacon-mode-(if-you-need-welcome-function))
- [BLE Communication Data Message Basic Format](#ble-communication-data-message-basic-format)
- [0x02 Get Device Basic Info](#0x02-get-device-basic-info)
- [0x0F Extend Command](#0x0F-extend-command)
    - [0x14 Read Hardware Version](#0x14-read-hardware-version)
    - [0x30 Setting Temperature Display Mode](#0x30-setting-temperature-display-mode)
    - [0x31 Read the Display Mode and Value of the Meter](#0x31-read-the-display-mode-and-value-of-the-meter)
- [Other Devices](#other)
    - [Outdoor Temperature/Humidity Sensor](#outdoor-temperaturehumidity-sensor)

### Meter Broadcast Message

- This broadcast message defines the Service data of Scan Rsp of the specific Device.
- The length of the Service data is different based on Device Type. The Service data can be 8 bytes max. The Byte: 0, Byte: 1 and Byte: 2 are for every Device Type. The bytes start from Byte: 3 depends on different Device Type. Please refer to Device Type definition in the table below.
- Broadcast modes are defined as:
    - SwitchBot Mode (Default)
    - Simple Mode
    - iBeacon Mode

<table>
    <thead>
        <tr>
            <th colspan=4>Device Type</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Product Name</td>
            <td rowspan=1>HEX</td>
            <td rowspan=1>ASCII</td>
            <td rowspan=1>Note</td>
        </tr>
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
            <td rowspan=1>Add Mode</td>
        </tr>
        <tr>
            <td rowspan=1>0x6C</td>
            <td rowspan=1>'l'</td>
            <td rowspan=1>Normal</td>
        </tr>
        <tr>
            <td rowspan=2>SwitchBot Hub Plus (WoLink Plus)</td>
            <td rowspan=1>0x50</td>
            <td rowspan=1>'P'</td>
            <td rowspan=1>Add Mode</td>
        </tr>
        <tr>
            <td rowspan=1>0x70</td>
            <td rowspan=1>'p'</td>
            <td rowspan=1>Normal Mode</td>
        </tr>
        <tr>
            <td rowspan=2>SwitchBot Fan (WoFan)</td>
            <td rowspan=1>0x46</td>
            <td rowspan=1>'F'</td>
            <td rowspan=1>Add Mode</td>
        </tr>
        <tr>
            <td rowspan=1>0x66</td>
            <td rowspan=1>'f'</td>
            <td rowspan=1>Normal Mode</td>
        </tr>
        <tr>
            <td rowspan=2>SwitchBot MeterTH (WoSensorTH)</td>
            <td rowspan=1>0x74</td>
            <td rowspan=1>'t'</td>
            <td rowspan=1>Add Mode</td>
        </tr>
        <tr>
            <td rowspan=1>0x54</td>
            <td rowspan=1>'T'</td>
            <td rowspan=1>Normal Mode</td>
        </tr>
        <tr>
            <td rowspan=2>SwitchBot Mini (HubMini)</td>
            <td rowspan=1>0x4D</td>
            <td rowspan=1>'M'</td>
            <td rowspan=1>Add Mode</td>
        </tr>
        <tr>
            <td rowspan=1>0x6D</td>
            <td rowspan=1>'m'</td>
            <td rowspan=1>Normal Mode</td>
        </tr>
    </tbody>
</table>

### Meter Broadcast Message Format

#### (Old) Broadcast Message
Use this for firmware version before Bot v30, Remote v20 and Hub v6.

**Advertise:**
- Manufacturer data: includes device's company ID and MAC address (support WeChat protocol)


**Scanrsp:**
- Device Name: "Bot", "Remote", "Hub", "WoSensorTH"
- UUID: ''cba20d00-224d-11e6-9fb8-0002a5d5c51b'' and ''fee7''

#### (New) Broadcast Message
The length of the Service data is different based on Device Type. The Service data can be 8 bytes max. 

The bit[6:0] in Byte: 0 of the broadcast message is Device Type. The Byte: 0, Byte: 1 and Byte: 2 are for every Device Type. The bytes start from Byte: 3 depends on different Device Type, serving as general registers.

The backend also needs a mapping table of the Service data, so both the backend and app can parse the Service data.

<table>
    <thead>
        <tr>
            <th colspan=3>Service data</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=2>Byte: 0</td>
            <td rowspan=2>Enc Type Dev Type</td>
            <td rowspan=1>Bit[7] - Reserved</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[6:0] - Device Type Please refer to the Device Type table above.</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 1</td>
            <td rowspan=2>Status</td>
            <td rowspan=1>Bit[7:4] - Reserved</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[3] – Group D Bit[2] – Group C Bit[1] – Group B

Bit[0] – Group A
0: the device is not in this group
1: the device is in this group</td>
        </tr>
        <tr>
            <td rowspan=2>Byte: 2</td>
            <td rowspan=2>Update UTC Flag Battery</td>
            <td rowspan=1>Bit[7] – Reserved</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[6:0] - Remaining Battery 0~100%</td>
        </tr>
        <tr>
            <td rowspan=3>Byte: 3</td>
            <td rowspan=3>alarm flag the fraction part of temperatrue value (WoSensorTH)</td>
            <td rowspan=1>Bit[7:6] – Temperature Alert Status
    
- 0: no alert
- 1: low temp alert (temperature lower than the low limit)
- 2: high temp alert (temperature higher than the high limit)
- 3: temp alert (temperature higher than than the low limit and lower than the high limit)</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[5:4] – Humidity Alert Status
    - 0: no alert
    - 1: low humidity alert (humidity lower than the low limit)
    - 2: high humidity alert (humidity higher than the high limit)
    - 3: temp humidity (humidity higher than than the low limit and lower than the high limit)</td>
        </tr>
        <tr>
        <td rowspan=1>
Bit[3:0] – Decimals of the Temperature
- 0000 – 1001:0~9</td>
        </tr>
        <tr>
        <td rowspan=2>Byte: 4</td>
        <td rowspan=2>temperature mode Positive/Negative temperature flag the integer part of temperature value (WoSensorTH)</td>
        <td rowspan=1>Bit[7] – Positive/Negative temperature flag
    - 0: subzero temperature
    - 1: temperature above zero</td>
        </tr>
        <td rowspan=1>Bit[6:0] – Integers of the Temperature 000 0000 – 111 1111: 0~127 °C Celsius to Fahrenheit: F=(C*1.8)+32</td>
        </tr>
        <td rowspan=2>Byte: 5</td>
        <td rowspan=2>humidity value (WoSensorTH)</td>
        <td rowspan=1>Bit[7] – Temperature Scale      

        - 0: Celsius scale (°C)
        - 1: Fahrenheit scale (°F)</td>
        </tr>
        <td rowspan=1>Bit[6:0] – Humidity Value 000 0000 – 110 0011: 0~99%</td>
        </tr>
    </tbody>
</table>

## Broadcast Mode

### SwitchBot Mode (Default)
**Advertise:**
- Manufacturer data: include device's company ID (0x0059 Nordic) and MAC address (support WeChat protocol)
- BLE_ADVDATA_NO_NAME

**Scanrsp:**
- UUID: ''cba20d00-224d-11e6-9fb8-0002a5d5c51b'' and ''fee7''
- BLE_ADVDATA_NO_NAME

**Example:**

<img alt="broadcast_1" src="https://switchbot-open-api.s3.amazonaws.com/Meter+open+api/broadcast_1.png"></a>
<img alt="broadcast_2" src="https://switchbot-open-api.s3.amazonaws.com/Meter+open+api/broadcast_2.png"></a>

### Simple Mode
**Advsertise:**
- BLE_ADVDATA_NO_NAME

**Scanrsp:**

- UUID (cba20d00-224d-11e6-9fb8-0002a5d5c51b)
- BLE_ADVDATA_NO_NAME


### iBeacon Mode (if you need welcome function)

**Advertise:**

Standard iBeacon package

- UUID:cba20d00-224d-11e6-9fb8-0002a5d5c51b
- Major ID:
- High Byte: bit 7:4 - beacon type
- Bit[3:0] - beacon data
- Low Byte: beacon data
- Minor ID:beacon data
- TX power: 0xc3
- BLE_ADVDATA_NO_NAME

<table>
    <thead>
        <tr>
            <th colspan=5>beacon data instruction</th>
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
- UUID(**cba20d00-224d-11e6-9fb8-0002a5d5c51b**)
- BLE_ADVDATA_NO_NAME


## BLE Communication Data Message Basic Format
- The controlling terminal (short as the Terminal below) and the configured device (short as the Device below) use BLE to communicate with each other wirelessly. During the communication, the Terminal acts as a central device, while the Device acts as peripheral device. The Terminal gets basic device info by reading the broadcast message of the Device. They exchange data by using read and write characteristic of communication service.
- All bilateral communication is after the BLE connection established. The Terminal send a REQ message to the Device, and then the Device returns a RESP message.
- All communication message data length is 1-20 bytes, i.e. MTU=20. There are 2 types of REQ messages sending from the Terminal to the Device, encrypted and unencrypted.
- **Communication service UUID:**
    - **88cba20d00-224d-11e6-9fb8-0002a5d5c51b**
    - UUID TYPE:Vendor UUID types start at this index (128-bit)
- **RX characteristic UUID of the message from the Terminal to the Device:**
    - UUID: ''cba20002-224d-11e6-9fb8-0002a5d5c51b''
    - UUID TYPE :Vendor UUID types start at this index (128-bit)
    - Char Attribute :RW
    - Char Properties: notify      

- **TX  characteristic UUID of the message from the Device to the Terminal:**
    - **cba20003-224d-11e6-9fb8-0002a5d5c51b**
    - UUID TYPE :Vendor UUID types start at this index (128-bit)
    - Char Attribute :RW

All communication message data length is 1-20 bytes, i.e. MTU=20. The REQ data from the Terminal to the Device has encrypted and unencrypted format, as below:

<table>
    <thead>
        <tr>
            <th colspan=3>REQ Packet (Unencrpted)</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Magic Number</td>
            <td rowspan=1>0x57 – (fixed value)</td>
        </tr>
        <tr>
            <td rowspan=3>Product</td>
            <td rowspan=3>Header</td>
            <td rowspan=1>Bit[7:6] – Version 0 – (fixed value)</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[5:4] – Reserved</td>
        </tr>
        <tr>
            <td rowspan=1>Bit[3:0] – command 0x02 – Get Device Basic Info 0x0F – Expand Command</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2-19</td>
            <td rowspan=1>Payload</td>
            <td rowspan=1>Depends on Command</td>
        </tr>
    </tbody>
</table>

Payload complement the data info to the Commands. The Payload format varies for different Commands.

The basic format of RESP message from the Terminal to the Device:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP Packet</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Response status</td>
            <td rowspan=1>0x01 - OK Action executed 0x02 - ERROR Error while executing an Action 0x03 - BUSY Device is busy now, please try later 0x04 - Communication protocol version incompatible 0x05 - Device does not support this Command 0x06 - Device low battery 0x07 - Device is encrypted 0x08 - Device is unencrypted 0x09 - Password error 0x0A - Device does not support this encription method 0x0B - Failed to locate a nearby mesh device 0x0C - Failed to connect to the network</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1-19</td>
            <td rowspan=1>Payload</td>
            <td rowspan=1>Depends on the Command to reply</td>
        </tr>
    </tbody>
</table>

Status returns the handling result of the Device to the earlier command, which is the 1st Byte of every RESP message, and also mandatory.

Payload complement the data info to the Commands. The data length is 0-19 bytes depends on different cases, and can also be omitted.

The format of the Payload is up to the Command type it is responding and the handling result.

## 0x02 Get Device Basic Info
- This command is used to get the basic info of a Device:
- REQ message payload is 0.
- If executed successfully, the Status Byte of the RESP message will be 1. The payload is the device basic info:
    - This command is used to get the basic info of a Meter - remaining battery, firmware version, Service data[0], Service data[1].

REQ message payload is 0.

<table>
    <thead>
        <tr>
            <th colspan=1>REQ message payload: 0</th>
        </tr>
    </thead>
</table>

If executed successfully, the Status Byte of the RESP message will be 0, the payload format is as below:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP Message payload</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Bat Per</td>
            <td rowspan=1>ASCII</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>FW Ver</td>
            <td rowspan=1>Firmware Version Initial FW Ver:  10   (0x0A)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=1>Service_data</td>
            <td rowspan=1>Service_data[0]</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 3</td>
            <td rowspan=1>Service_data</td>
            <td rowspan=1>Service_data[1]</td>
        </tr>
    </tbody>
</table>

### 0x0F Extend Command

### 0x14 Read Hardware Version
- Read the information of Device hardware version.
- REQ Packet payload is 0。
- The Status byte of the RESP data packet after execution successfully is 1 and the payload is 0.

Read the hardware version of WoSensorTH

REQ Packet payload is 0.

<table>
    <thead>
        <tr>
            <th colspan=1>REQ Packet payload:   0</th>
        </tr>
    </thead>
</table>

The Status byte of the RESP data packet after execution successfully is 1 and the payload is 0. The payload is as below:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP Packet status:    1</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Status</td>
            <td rowspan=1>Command</td>
        </tr>
    </tbody>
</table>

<table>
    <thead>
        <tr>
            <th colspan=3>RESP Packet payload:1</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Hardware version</td>
            <td rowspan=1>0x01 – WoSensorTH  V1.0</td>
        </tr>
    </tbody>
</table>

### 0x30 Setting Temperature Display Mode
- This command is for setting the display mode of temperature. You could set it to display Celsius or Fahrenheit.
- The REQ Packet payload is 1。
- The Status byte of the RESP data packet after execution successfully is 1 and the payload is 0.

REQ Packet payload is 1.

REQ Packet payload is as below:

<table>
    <thead>
        <tr>
            <th colspan=3>REQ Packet payload:   1</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Temperature Mode</td>
            <td rowspan=1>Bit[7-2]: Reserved Bit[1-0]: Temperature display settings 11:Reserved 10:F mode     Fahrenheit mode 01:C mode     Celsius mode 00:Reserved</td>
        </tr>
    </tbody>
</table>

The Status byte of the RESP data packet after execution successfully is 1 and the payload is 0, the payload is as below:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP Packet status:    1</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Status</td>
            <td rowspan=1>Command</td>
        </tr>
    </tbody>
</table>

<table>
    <thead>
        <tr>
            <th colspan=1>RESP Packet payload:   0</th>
        </tr>
    </thead>
</table>

### 0x31 Read the Display Mode and Value of the Meter
- This command is for reading the temperature and humidity from the WoSensorTH, the temperature value is one decimal, the range is (-20.0 °C ~ 60.0 °C) ；The humidity is an integer, the range is (0 ~ 99%).
- The REQ Packet payload is 0.
- The Status byte of the RESP data packet after execution successfully is 1 and the payload is 3.

The REQ Packet payload is 0.

<table>
    <thead>
        <tr>
            <th colspan=1>REQ Packet payload:   0</th>
        </tr>
    </thead>
</table>

The Status byte of the RESP data packet after execution successfully is 1 and the payload is 3.  The payload is as below:

<table>
    <thead>
        <tr>
            <th colspan=3>RESP Packet status:    1</th>
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

The temperature value is one decimal, the range is (-20.0 °C ~ 60.0 °C) ；The humidity is an integer, the range is (0 ~ 99%).

<table>
    <thead>
        <tr>
            <th colspan=3>RESP Packet payload:   3</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>The fractional part of temperature value</td>
            <td rowspan=1>bit[7-6]: Reserved bit[3-0]: The fractional part of temperature value     0000 – 1001: 0~9</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=1>Temperature Mode Positive/Negative temperature flag The integral part of temperature value</td>
            <td rowspan=1>bit[7]: Positive/Negative temperature flag     0: subzero temperature     1: positive value bit[6-0]: The integral part of temperature value     000 0000 –  111 1111: 0~127 °C Formula for Celsius to Fahrenheit F=(C*1.8)+32</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
            <td rowspan=1>Humidity value</td>
            <td rowspan=1>bit[7]: Temperature Mode     0: °C     1: °F If the temperature mode did not be changed by the physical button behind the device, the default value is 0 °C. bit[6-0]: Humidity value     000 0000 –  110 0011: 0~99 %</td>
        </tr>
    </tbody>
</table>

### Other

This section is for devices that don't quite fit the general
specification above.  Data here is unofficial and community provided.

#### Outdoor Temperature/Humidity Sensor

**WoSensorTHO**  (Model W3400010)

This device advertises 3 sections:

```
# Sample Advertisement
Length: 0x02, Type: 0x01, Data: bytes('06')
Length: 0x0F, Type: 0xFF, Data: bytes('6909XXXXXXXXXXXX360302963700')
Length: 0x06, Type: 0x16, Data: bytes('3DFD770064')
```

XXXXXXXXXXXX represents BT device MAC address.

The device type in the Service Data matches that of the WoSensorTH,
but the data has moved to different locations, so the same code cannot
be used.

```
# Data from Type: 0xFF (Manufacturer Specific Data)
temp = ((data[10] & 0x0F) * 0.1 + (data[11] & 0x7F)) * (((data[11] & 0x80) > 0 : 1 : -1);
humidity = data[12] & 0x7F;
```

```
# Data from Type: 0x16 (Service Data)
battery_pct = data[5] & 0x7F
```

CopyRight@2022 Wonderlabs, Inc.

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)
