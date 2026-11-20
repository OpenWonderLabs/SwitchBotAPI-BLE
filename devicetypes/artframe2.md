# Art Frame 2 and E Ink Photo Magnet BLE open API

Art Frame 2 (Product ID `0x0011f1c0` / `0x0111f1c0`) and E Ink Photo Magnet (Product ID
`0x001295d0` / `0x011295d0`) share one protocol. They have no single-byte Device Type; see
[Device Types](../README.md#device-types) for how Product IDs are read. This is what
[pySwitchbot](https://github.com/sblibs/pySwitchbot) implements (`switchbot/adv_parsers/image_frame.py`,
`switchbot/devices/art_frame2.py`). It does not cover the original Art Frame.

## Broadcast Message

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
        <tr><td>7</td><td>Battery</td><td>bit7: charging<br>bit6-0: battery 0~100%</td></tr>
        <tr><td>8~9</td><td>Image ID</td><td>ID of the image on screen, little-endian</td></tr>
        <tr><td>10</td><td>Display</td><td>bit7-4: display size<br>bit3: display mode<br>bit2: last network status<br>bit1: orientation</td></tr>
    </tbody>
</table>

## Commands

Both products use an encrypted connection: commands are sent with the account's key ID and
encryption key, as implemented by `SwitchbotEncryptedDevice` in pySwitchbot. The commands below are
shown before encryption. A response status (Byte 0) of `0x01` means OK.

<table>
    <thead>
        <tr><th>Command</th><th>Bytes</th></tr>
    </thead>
    <tbody>
        <tr><td>Get basic info</td><td><code>0x5702</code></td></tr>
        <tr><td>Next image</td><td><code>0x570f7a0701</code></td></tr>
        <tr><td>Previous image</td><td><code>0x570f7a0700</code></td></tr>
        <tr><td>Show image</td><td><code>0x570f7a02</code> + image ID (2 bytes, little-endian)</td></tr>
        <tr><td>Read image list</td><td><code>0x570f7b07</code> + packet number (1 byte, from <code>0x00</code>)</td></tr>
    </tbody>
</table>

### Basic info

<table>
    <thead>
        <tr><th>Byte</th><th>Field</th><th>Value</th></tr>
    </thead>
    <tbody>
        <tr><td>0</td><td>Status</td><td><code>0x01</code> OK</td></tr>
        <tr><td>1</td><td>Battery</td><td>bit7: charging<br>bit6-0: battery 0~100%</td></tr>
        <tr><td>2</td><td>Firmware</td><td>Firmware version × 10</td></tr>
        <tr><td>3</td><td>Hardware</td><td>Hardware version</td></tr>
        <tr><td>4</td><td>Display</td><td>bit7-4: display size<br>bit3: display mode<br>bit2: last network status</td></tr>
        <tr><td>5~6</td><td>Current image ID</td><td>Little-endian</td></tr>
        <tr><td>7~8</td><td>Number of images</td><td>Little-endian</td></tr>
        <tr><td>9</td><td>Orientation</td><td>Orientation</td></tr>
    </tbody>
</table>

### Image list

The stored images are a bitmap sent over one or more packets. Read packet `0x00` first, then each
following packet until the total reported in Byte 1. Each response is:

<table>
    <thead>
        <tr><th>Byte</th><th>Field</th><th>Value</th></tr>
    </thead>
    <tbody>
        <tr><td>0</td><td>Status</td><td><code>0x01</code> OK</td></tr>
        <tr><td>1</td><td>Total packets</td><td>Number of packets in the bitmap (at least 1)</td></tr>
        <tr><td>2</td><td>Packet number</td><td>Same as the requested packet number</td></tr>
        <tr><td>3~</td><td>Bitmap</td><td>Next bytes of the image bitmap</td></tr>
    </tbody>
</table>

Join the bitmap bytes of all packets in order. Bit `b` (bit0 first) of bitmap byte `i` set means
image ID `i × 8 + b + 1` is stored. pySwitchbot rejects a bitmap longer than 2560 bytes. The
image IDs in this list are the values the Show image command takes.

### Back to [Home](https://github.com/OpenWonderLabs/SwitchBotAPI-BLE/)
### Back to [Device Types](../README.md)
