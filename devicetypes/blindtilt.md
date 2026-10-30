# Blind Tilt BLE open API

Blind Tilt (Device Type `x` / `X`). This page documents what
[pySwitchbot](https://github.com/sblibs/pySwitchbot) implements
(`switchbot/adv_parsers/blind_tilt.py`, `switchbot/devices/blind_tilt.py`).

- [Broadcast Message](#broadcast-message)
- [Commands](#commands)
- [Basic info](#basic-info)

## Broadcast Message

The tilt position is broadcast in the manufacturer data, so it can be read without connecting.

### Service Data

<table>
    <thead>
        <tr><th>Byte</th><th>Field</th><th>Value</th></tr>
    </thead>
    <tbody>
        <tr><td>0</td><td>Device Type</td><td>bit6-0: <code>x</code> (0x78)</td></tr>
        <tr><td>2</td><td>Battery</td><td>bit6-0: battery 0~100%</td></tr>
    </tbody>
</table>

### Manufacturer Data

Byte offsets count from the start of the manufacturer data payload, after the 2-byte company ID
`0x0969`.

<table>
    <thead>
        <tr><th>Byte</th><th>Field</th><th>Value</th></tr>
    </thead>
    <tbody>
        <tr><td>0~5</td><td>MAC Address</td><td>Device MAC address</td></tr>
        <tr><td>6</td><td>Sequence Number</td><td>Increases when the broadcast data is updated</td></tr>
        <tr><td>7</td><td>Calibration</td><td>bit0: calibrated (0 no, 1 yes)</td></tr>
        <tr><td>8</td><td>Tilt</td><td>bit7: in motion<br>bit6-0: tilt position 0~100 (0 closed down, 50 open, 100 closed up)</td></tr>
        <tr><td>9</td><td>Light</td><td>bit7-4: light level</td></tr>
    </tbody>
</table>

## Commands

Commands use the cover command `0x570f4501`. pySwitchbot sends both commands listed for an action,
because different firmware versions accept different forms, and treats the action as done when
either response has Byte 0 `0x01`.

| Action | Commands |
| ------ | -------- |
| Open (tilt 50) | `0x570f4501010132`, `0x570f450105ff32` |
| Close down (tilt 0) | `0x570f4501010100`, `0x570f450105ff00` |
| Close up (tilt 100) | `0x570f4501010164`, `0x570f450105ff64` |
| Set tilt to `NN` (0x00~0x64) | `0x570f45010101NN`, `0x570f450105SSNN` (`SS` speed: `0xff` normal, `0x01` slow) |
| Stop | `0x570f45010001`, `0x570f450100ff` |

## Basic info

The basic info command `0x5702` returns:

<table>
    <thead>
        <tr><th>Byte</th><th>Field</th><th>Value</th></tr>
    </thead>
    <tbody>
        <tr><td>0</td><td>Status</td><td>0x01 - OK</td></tr>
        <tr><td>1</td><td>Battery</td><td>0~100%</td></tr>
        <tr><td>2</td><td>Firmware</td><td>Firmware version × 10</td></tr>
        <tr><td>4</td><td>Flags</td><td>bit5: light sensor on<br>bit3: fault</td></tr>
        <tr><td>5</td><td>Motion</td><td>bit3: solar panel connected<br>bit2: calibrated<br>bit1: opening<br>bit0: closing (or, while opening, the direction flag)</td></tr>
        <tr><td>6</td><td>Tilt</td><td>Tilt position 0~100</td></tr>
    </tbody>
</table>

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)
