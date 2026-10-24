## Air Purifier BLE open API

This page covers the Air Purifier (US / JP, Device Type `*` / `+`) and Air
Purifier Table (US / JP, Device Type `7` / `8`). Air purifiers use an
encrypted connection; the commands below are shown before encryption, and the
status byte (Byte 0) of a response is not encrypted.

- [0x0F Expansion command](#0x0f-expansion-command)
  - [0x570f4c09 reset filter usage](#0x570f4c09-reset-filter-usage)
  - [0x570f4d09 read filter usage](#0x570f4d09-read-filter-usage)
- [Resetting the filter safely](#resetting-the-filter-safely)

### 0x0F Expansion command

### 0x570f4c09 reset filter usage

The payload length of the request packet is 0.

Resets the filter's used-hours counter to 0.

<table>
    <thead>
        <tr>
            <th colspan=3>Response</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Status</td>
            <td rowspan=1>0x01 - OK, the reset was accepted</td>
        </tr>
    </tbody>
</table>

### 0x570f4d09 read filter usage

The payload length of the request packet is 0.

<table>
    <thead>
        <tr>
            <th colspan=3>Response</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=1>Byte: 0</td>
            <td rowspan=1>Status</td>
            <td rowspan=1>0x01 - OK</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 1</td>
            <td rowspan=2>Used hours</td>
            <td rowspan=2>Filter used hours, big-endian (e.g. 0x0768 = 1896 hours)</td>
        </tr>
        <tr>
            <td rowspan=1>Byte: 2</td>
        </tr>
    </tbody>
</table>

### Resetting the filter safely

Send `0x570f4c09` once and do not retry it automatically: if the reply is
lost, the reset may still have been applied. Then read `0x570f4d09` and treat
the reset as done only when the status is `0x01` and the used hours are `0`.
This is how [pySwitchbot](https://github.com/sblibs/pySwitchbot) implements
`reset_filter_usage`.
