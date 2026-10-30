# Humidifier BLE open API

Humidifier (Device Type `e` / `E`). This page documents what
[pySwitchbot](https://github.com/sblibs/pySwitchbot) implements
(`switchbot/adv_parsers/humidifier.py`, `switchbot/devices/humidifier.py`). The Evaporative
Humidifier (`#`) uses a different, encrypted protocol and is not covered here.

## Broadcast Message

### Service Data

<table>
    <thead>
        <tr><th>Byte</th><th>Field</th><th>Value</th></tr>
    </thead>
    <tbody>
        <tr><td>0</td><td>Device Type</td><td>bit6-0: <code>e</code> (0x65)</td></tr>
        <tr><td>1</td><td>Power</td><td>0x00 off, otherwise on</td></tr>
        <tr><td>4</td><td>Level</td><td>Humidity level (see below)</td></tr>
    </tbody>
</table>

Level values: `128` (0x80) or `228` (0xE4) - auto mode; `101`, `102`, `103` - manual presets
(about 33%, 66% and 100%); `1`~`100` - target humidity in %.

## Command

All settings use one command:

`0x570f4381 01 PP LL ffffffff`

- `PP`: `0x01` on, `0x00` off
- `LL`: level, `0x80` for auto or `0x01`~`0x64` for a target humidity

| Example | Command |
| ------- | ------- |
| Off | `0x570f4381010080ffffffff` |
| On, auto | `0x570f4381010180ffffffff` |
| On, 34% | `0x570f4381010122ffffffff` |
| On, 67% | `0x570f4381010143ffffffff` |
| On, 100% | `0x570f4381010164ffffffff` |

Response Byte 0 is `0x01` when the command was executed. The humidifier has no battery, and
pySwitchbot reads its state from the broadcast only.

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)
