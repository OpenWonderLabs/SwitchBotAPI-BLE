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

| Product         | Device Type |
| --------------- | ----------- |
| Bot             | H (0x48)    |
| Meter           | T (0x54)    |
| Humidifier      | e (0x65)    |
| Curtain         | c (0x63)    |
| Curtain 3       | {(0x7B)     |
| Motion Sensor   | s (0x73)    |
| Contact Sensor  | d (0x64)    |
| Color Bulb      | u (0x75)    |
| LED Strip Light | r (0x72)    |
| Smart Lock      | o (0x6F)    |
| Plug Mini       | g (0x67)    |
| Meter Plus      | i (0x69)    |

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
