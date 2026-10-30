# Lock BLE Open API

## Lock Broadcast Message
The following table is the Service Data of SCAN_RSP.

### ADV AD Structure

| ADV AD Structure |      |           |                            |                                                                                                                                                                                                                         |                                                                                                              |
| ---------------- | ---- | --------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| AD Structure 1   | len  | Byte 0    |                            | 0x02                                                                                                                                                                                                                    |                                                                                                              |
|                  | type | Byte 1    | Flags                      | 0x01                                                                                                                                                                                                                    |                                                                                                              |
|                  | data | Byte 2    |                            | 0x06                                                                                                                                                                                                                    |                                                                                                              |
| AD Structure 2   | len  | Byte 3    |                            |                                                                                                                                                                                                                         |                                                                                                              |
|                  | type | Byte 4    | Company ID                 | 0xff                                                                                                                                                                                                                    |                                                                                                              |
|                  | data | Byte 5~6  |                            | 0x0969                                                                                                                                                                                                                  |                                                                                                              |
|                  |      | Byte 7~13 | MAC Address                | Device MAC Address                                                                                                                                                                                                      |                                                                                                              |
|                  |      | Byte 14   | Sequence Number            | [1,255]                                                                                                                                                                                                                 | 1~255 increases, 256 overflow is 1, and the broadcast packet data automatically increases when it is updated |
|                  |      | Byte 15   | Lock Info                  | Bit7 Calibration status： 0-Not calibrated 1-Calibrated                                                                                                                                                                 |                                                                                                              |
|                  |      |           |                            | Bit6-4 Lock Status:<br/>000b - Locked<br/>001b - Unlocked<br/>010b - Locking<br/>011b - Unlocking<br/>100b - LockingStop（Stuck）<br/>101b - UnlockingStop（Stuck）<br/>110b - Not fully locked（only for EU Lock Type) | This status is invalid when not calibrated                                                                   |
|                  |      |           |                            | Bit3 Door status update source:<br/>0-Update from Primary lock<br/>1-Update from Secondary lock<br/>Bit2 Door status:<br/>0-Closed<br/>1-Opened                                                                         | Only for Double Lock Mode                                                                                    |
|                  |      |           |                            | Bit1-0 Reserve                                                                                                                                                                                                          |                                                                                                              |
|                  |      | Byte 16   | Double lock control source | Bit7 Double Lock Mode:  0-Off 1-On                                                                                                                                                                                      | Only for Double Lock Mode                                                                                    |
|                  |      |           |                            | Bit6 Reserve                                                                                                                                                                                                            |                                                                                                              |
|                  |      |           |                            | Bit5 Unclosed door timeout alarm: 0-Off 1-On                                                                                                                                                                            |                                                                                                              |
|                  |      |           |                            | Bit4 Unlocked timeout alarm: 0-Off 1-On                                                                                                                                                                                 |                                                                                                              |
|                  |      |           |                            | Bit3-2 Reserve                                                                                                                                                                                                          |                                                                                                              |
|                  |      |           |                            | Bit1 Pause Automatic Locking Status:  0 inactived; 1: activated                                                                                                                                                         |                                                                                                              |
|                  |      |           |                            | Bit0 Reserve                                                                                                                                                                                                            |                                                                                                              |
|                  |      | Byte 17   | Reserve                    | Reserve                                                                                                                                                                                                                 |

### SCAN RSP AD Structure

| SCAN RSP AD Structure |      |        |              |                                                 |     |
| --------------------- | ---- | ------ | ------------ | ----------------------------------------------- | --- |
| AD Structure 1        | len  |        |              |                                                 |     |
|                       | type |        | Service Data | 0x16                                            |     |
|                       | data | Byte 0 | Device Type  | Bit7 Reserve                                    |     |
|                       |      |        |              | Bit6-0 Device Type<br/>'o' (0x6F)： Normal Mode |     |
|                       |      | Byte 1 | Reserve      | Reserve                                         |     |
|                       |      | Byte 2 | Battery      | Bit7 Reserve                                    |     |
|                       |      |        |              | Bit6-0 battery voltage 0~100%                   |     |

## Lock Pro, Lock Ultra and Lock Vision Pro Broadcast Message

Lock Pro (`$` / `0x04`), Lock Pro WiFi, Lock Ultra, Lock Ultra Max and Lock Vision Pro use a
different manufacturer data layout from the Lock and Lock Lite. Byte offsets below count from the
start of the manufacturer data payload, after the 2-byte company ID `0x0969`. This is the layout
[pySwitchbot](https://github.com/sblibs/pySwitchbot) parses (`switchbot/adv_parsers/lock.py`).

<table>
    <thead>
        <tr><th>Byte</th><th>Field</th><th>Value</th></tr>
    </thead>
    <tbody>
        <tr><td>0~5</td><td>MAC Address</td><td>Device MAC address</td></tr>
        <tr><td>6</td><td>Sequence Number</td><td>Increases when the broadcast data is updated</td></tr>
        <tr><td>7</td><td>Lock Info</td><td>bit7: calibrated (0 no, 1 yes)<br>bit6-3: lock status (see <a href="#lock-status">Lock status</a>)<br>bit2-0: reserved</td></tr>
        <tr><td>8</td><td>Door Info</td><td>bit7-6: update from secondary lock<br>bit5: door open, reported by the secondary lock<br>bit4: door open (0 closed, 1 open)<br>bit3: automatic locking paused<br>bit2-0: reserved</td></tr>
        <tr><td>9</td><td>Battery</td><td>bit7: reserved<br>bit6-0: battery 0~100%</td></tr>
        <tr><td>10</td><td>Double Lock</td><td>bit7: double lock mode<br>bit6: this is the secondary lock<br>bit5: manual unlock linkage<br>bit4-0: reserved</td></tr>
        <tr><td>11</td><td>Alarms</td><td>bit7: unclosed door alarm<br>bit6: unlocked alarm<br><b>Lock Pro / Lock Pro WiFi:</b> bit5: low temperature alarm, bit2: left battery compartment alarm, bit1: right battery compartment alarm<br><b>Lock Ultra / Ultra Max / Vision Pro:</b> bit4: power alarm, bit2-0: battery status</td></tr>
    </tbody>
</table>

### Lock status

| Value | Status |
| ----- | ------ |
| 0 | Locked |
| 1 | Unlocked |
| 2 | Locking |
| 3 | Unlocking |
| 4 | Locking stopped (stuck) |
| 5 | Unlocking stopped (stuck) |
| 6 | Not fully locked (EU lock type only) |
| 7 | Half locked (Lock Ultra EU lock type only) |

## Lock Commands

All lock models use an encrypted connection: commands are sent with the account's key ID and
encryption key, as implemented by `SwitchbotEncryptedDevice` in pySwitchbot. The commands below are
shown before encryption. A response status (Byte 0) of `0x01` means OK, and `0x06` means OK with a
low battery.

<table>
    <thead>
        <tr><th>Command</th><th>Lock / Lock Lite</th><th>Lock Pro, Ultra, Ultra Max, Vision, Vision Pro, Pro WiFi</th></tr>
    </thead>
    <tbody>
        <tr><td>Lock</td><td><code>0x570f4e01011000</code></td><td><code>0x570f4e0101000000</code></td></tr>
        <tr><td>Unlock (also unlatches when the EU unlatch feature is on)</td><td><code>0x570f4e01011080</code></td><td><code>0x570f4e0101000080</code></td></tr>
        <tr><td>Unlock without unlatch</td><td><code>0x570f4e010110a0</code></td><td><code>0x570f4e01010000a0</code></td></tr>
        <tr><td>Half lock (Lock Ultra only)</td><td>-</td><td><code>0x570f4e0101000008</code></td></tr>
        <tr><td>Disable status notifications</td><td colspan=2><code>0x570e00</code></td></tr>
    </tbody>
</table>

The lock info and enable-notification commands end with a model byte:

| Model | Lock info | Enable status notifications |
| ----- | --------- | --------------------------- |
| Lock, Lock Lite | `0x570f4f8101` | `0x570e01001e00008101` |
| Lock Pro | `0x570f4f8104` | `0x570e01001e00008104` |
| Lock Ultra, Lock Ultra Max | `0x570f4f8107` | `0x570e01001e00008107` |
| Lock Vision, Lock Vision Pro | `0x570f4f8102` | `0x570e01001e00008102` |
| Lock Pro WiFi | `0x570f4f810a` | `0x570e01001e0000810a` |

### Lock info data

The lock info response (after the status byte) and status notifications carry the same lock info
data. While notifications are enabled, the lock sends notifications whose Byte 0 is `0x0F`; the
lock info data is the decrypted payload from Byte 4.

<table>
    <thead>
        <tr><th>Byte</th><th>Lock, Lock Lite, Lock Vision, Lock Vision Pro</th><th>Lock Pro, Ultra, Ultra Max, Pro WiFi</th></tr>
    </thead>
    <tbody>
        <tr><td>0</td><td>bit7: calibrated<br>bit6-4: lock status<br>bit2: door open (Lock and Lock Vision Pro only)</td><td>bit7: calibrated<br>bit6-3: lock status</td></tr>
        <tr><td>1</td><td>bit5: unclosed door alarm (Lock and Lock Vision Pro only)<br>bit4: unlocked alarm</td><td>bit4: door open<br>bit0: half lock calibrated (Lock Ultra only)</td></tr>
        <tr><td>5</td><td>-</td><td>bit7: unclosed door alarm<br>bit6: unlocked alarm</td></tr>
    </tbody>
</table>

Lock Vision Pro uses the Lock layout here, although its broadcast uses the Lock Pro layout.
