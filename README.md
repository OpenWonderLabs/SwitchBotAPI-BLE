# SwitchBotAPI-BLE

- [Device Types](#device-type)
- [UUID Update Notes](#uuid-update-notes)
- [Bot BLE open API](/devicetypes/bot.md)
- [Color Bulb BLE open API](/devicetypes/colorbulb.md)
- [Contact Sensor BLE open API](/devicetypes/contactsensor.md)
- [Curtain BLE open API](/devicetypes/curtain.md)
- [Curtain 3 BLE open API](/devicetypes/curtain3.md)
- [LED Strip Light BLE open API](/devicetypes/ledstriplight.md)
- [Meter BLE open API](/devicetypes/meter.md)
- [Motion Sensor BLE open API](/devicetypes/motionsensor.md)
- [Plug Mini BLE open API](/devicetypes/plugmini.md)
- [Lock BLE open API](/devicetypes/lock.md)

## Device Types

| Product                          | Device Type          |
| -------------------------------- | -------------------- |
| Bot                              | H (0x48)             |
| Meter                            | T (0x54)             |
| Meter Plus                       | i (0x69), I (0x49)   |
| Meter Pro CO2                    | 5 (0x35), 0x15       |
| Indoor/Outdoor Thermo-Hygrometer | w (0x77), W (0x57)   |
| Humidifier                       | e (0x65)             |
| Curtain                          | c (0x63), C (0x43)   |
| Curtain 3                        | { (0x7B), [ (0x5B)   |
| Motion Sensor                    | s (0x73)             |
| Contact Sensor                   | d (0x64)             |
| Color Bulb                       | u (0x75)             |
| LED Strip Light                  | r (0x72)             |
| Ceiling Light                    | q (0x71), Q (0x51)   |
| Ceiling Light Pro                | n (0x6E), N (0x4E)   |
| Smart Lock                       | o (0x6F)             |
| Plug Mini (US)                   | g (0x67), G (0x47)   |
| Plug Mini (JP)                   | j (0x6A), J (0x4A)   |

Products that share a protocol still report their own Device Type, so
clients should identify the product from the Device Type rather than
treating, for example, Curtain 3 as Curtain or Plug Mini (JP) as
Plug Mini (US). Where two values are listed, both identify the same
product.

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
