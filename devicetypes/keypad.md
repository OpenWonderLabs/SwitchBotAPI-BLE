# Keypad BLE open API

Keypad (Device Type `y` / `Y`). [pySwitchbot](https://github.com/sblibs/pySwitchbot) reads the
Keypad's broadcast only (`switchbot/adv_parsers/keypad.py`); it implements no Keypad commands, so
managing passcodes over BLE is not documented here.

## Broadcast Message

### Service Data

<table>
    <thead>
        <tr><th>Byte</th><th>Field</th><th>Value</th></tr>
    </thead>
    <tbody>
        <tr><td>0</td><td>Device Type</td><td>bit6-0: <code>y</code> (0x79)</td></tr>
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
        <tr><td>6</td><td>Attempt state</td><td>Keypad attempt state (reported as <code>attempt_state</code> by pySwitchbot; its values are not documented)</td></tr>
    </tbody>
</table>

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)
