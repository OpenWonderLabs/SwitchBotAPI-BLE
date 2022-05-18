# SwitchBotAPI-BLE

- [Device Types](#device-type)
- [Bot BLE open API](/devicetypes/bot.md)
- [Color Bulb BLE open API](/devicetypes/colorbulb.md)
- [Contact Sensor BLE open API](/devicetypes/contactsensor.md)
- [Curtain BLE open API](/devicetypes/curtain.md)
- [LED Strip Light BLE open API](/devicetypes/ledstriplight.md)
- [Meter BLE open API](/devicetypes/meter.md)
- [Motion Sensor BLE open API](/devicetypes/motionsensor.md)
- [Plug Mini BLE open API](/devicetypes/plugmini.md)

## Device Types

| Product         | Device Type |
|-----------------|-------------|
| Bot             | H (0x48)    |
| Meter           | T (0x54)    |
| Humidifier      | e (0x65)    |
| Curtain         | c (0x63)    |
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
