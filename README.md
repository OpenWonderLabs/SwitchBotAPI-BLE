# SwitchBotAPI-BLE

- [Device Types](#device-type)
- [UUID Update Notes](#uuid-update-notes)
- [Air Purifier BLE open API](/devicetypes/airpurifier.md)
- [Blind Tilt BLE open API](/devicetypes/blindtilt.md)
- [Bot BLE open API](/devicetypes/bot.md)
- [Color Bulb BLE open API](/devicetypes/colorbulb.md)
- [Contact Sensor BLE open API](/devicetypes/contactsensor.md)
- [Curtain BLE open API](/devicetypes/curtain.md)
- [Curtain 3 BLE open API](/devicetypes/curtain3.md)
- [Humidifier BLE open API](/devicetypes/humidifier.md)
- [Keypad BLE open API](/devicetypes/keypad.md)
- [LED Strip Light BLE open API](/devicetypes/ledstriplight.md)
- [Meter BLE open API](/devicetypes/meter.md)
- [Motion Sensor BLE open API](/devicetypes/motionsensor.md)
- [Plug Mini BLE open API](/devicetypes/plugmini.md)
- [Lock BLE open API](/devicetypes/lock.md)

## Device Types

| Product                          | Device Type        |
| -------------------------------- | ------------------ |
| Bot                              | H (0x48)           |
| Meter                            | T (0x54), t (0x74) |
| Meter Plus                       | i (0x69), I (0x49) |
| Meter Pro                        | 4 (0x34), 0x14     |
| Meter Pro CO2                    | 5 (0x35), 0x15     |
| Indoor/Outdoor Thermo-Hygrometer | w (0x77), W (0x57) |
| Humidifier                       | e (0x65), E (0x45) |
| Curtain                          | c (0x63), C (0x43) |
| Curtain 3                        | { (0x7B), [ (0x5B) |
| Motion Sensor                    | s (0x73), S (0x53) |
| Contact Sensor                   | d (0x64), D (0x44) |
| Color Bulb                       | u (0x75), U (0x55) |
| LED Strip Light                  | r (0x72), R (0x52) |
| Ceiling Light                    | q (0x71), Q (0x51) |
| Ceiling Light Pro                | n (0x6E), N (0x4E) |
| Smart Lock                       | o (0x6F), O (0x4F) |
| Plug Mini (US)                   | g (0x67), G (0x47) |
| Plug Mini (JP)                   | j (0x6A), J (0x4A) |
| Hub 2                            | v (0x76), V (0x56) |
| Evaporative Humidifier           | # (0x23), 0x03     |
| Smart Lock Pro                   | $ (0x24), 0x04     |
| Blind Tilt                       | x (0x78), X (0x58) |
| Water Leak Detector              | & (0x26), 0x06     |
| Keypad                           | y (0x79), Y (0x59) |
| Relay Switch 1PM                 | < (0x3C), 0x1C     |
| Relay Switch 1                   | ; (0x3B), 0x1B     |
| Remote                           | b (0x62), B (0x42) |
| Universal Remote                 | 0x07, ' (0x27)     |
| Roller Shade                     | , (0x2C), 0x0C     |
| HubMini Matter                   | % (0x25), 0x05     |
| Circulator Fan                   | ~ (0x7E), ^ (0x5E) |
| Robot Vacuum K20+ Pro            | . (0x2E), 0x0F     |
| Floor Cleaning Robot S10         | z (0x7A), Z (0x5A) |
| Robot Vacuum K10+ Pro Combo      | 3 (0x33), 0x13     |
| Mini Robot Vacuum K10+           | } (0x7D), ] (0x5D) |
| Mini Robot Vacuum K10+ Pro       | ( (0x28), 0x08     |
| Air Purifier (US)                | * (0x2A), 0x0A     |
| Air Purifier (JP)                | + (0x2B), 0x0B     |
| Air Purifier Table (US)          | 7 (0x37), 0x17     |
| Air Purifier Table (JP)          | 8 (0x38), 0x18     |
| Smart Lock Lite                  | - (0x2D), 0x0D     |
| Garage Door Opener               | > (0x3E), 0x1E     |
| Relay Switch 2PM                 | = (0x3D), 0x1D     |
| Plug Mini (EU)                   | ? (0x3F), 0x1F     |

Products that share a protocol still report their own Device Type, so
clients should identify the product from the Device Type rather than
treating, for example, Curtain 3 as Curtain or Plug Mini (JP) as
Plug Mini (US). Where several values are listed, each identifies the same
product. Values are Byte 0 of the service data with Bit 7 cleared, as
listed in pySwitchbot's `SUPPORTED_TYPES`.

The device type is in the service data of SCAN_RSP.

| Service data |          |                         |
|--------------|----------|-------------------------|
| Byte: 0      | Enc type | Bit[7] NC               |
| Byte: 0      | Dev Type | Bit [6:0] – Device Type |


## UUID Update Notes

From Bot V6.4, Curtain V4.6, Meter V2.7,
- `Company ID` ( `ADV_IND` - `Manufacture Data` ) modified from `0x0059` to `0x0969`.
- `Service UUID` ( `SCAN_RSP` - `Service Data`) modified from `0x000d` to `0xfd3d`.
- Complete list of 128-bit UUIDs (`SCAN_RSP`) has been removed.
