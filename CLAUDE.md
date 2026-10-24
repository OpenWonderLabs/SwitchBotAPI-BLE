# SwitchBotAPI-BLE

Markdown documentation of the SwitchBot BLE protocol. There is no code, build or test here.

## Layout

- `README.md`: overview, the **Device Types** table (advertisement byte for each product) and links
  to each device page.
- `devicetypes/<device>.md`: broadcast format and commands for one product family.

## When to update

Update these docs when a protocol fact changes or is missing, usually as a follow-up to a
[pySwitchbot](https://github.com/sblibs/pySwitchbot) change that was ported to
[node-switchbot](https://github.com/OpenWonderLabs/node-switchbot):

- New or changed device-type byte: update the README table. The source of truth is
  `SUPPORTED_TYPES` in pySwitchbot's `switchbot/adv_parser.py`. List every byte that identifies
  the product (e.g. `g (0x67), G (0x47)`).
- New fields or commands: update or add the matching `devicetypes/*.md` page and link new pages
  from the README.
- Only document behaviour that pySwitchbot or node-switchbot actually implements. Do not invent
  byte layouts.

## Style

- Keep the existing table formats (pipe tables in the README, HTML tables in device pages).
- Hex values as `0x7B`; device-type characters in the README as `{ (0x7B)`.
- Conventional Commit titles with `docs:`. Default branch is `latest`.
