# esphome-keyboard-layouts

## Purpose
`keyboard_layouts`: a keyboard layout per connected keyboard, chosen at runtime. The layouts are files (on a storage,
from Home Assistant or a web server), so a firmware does not carry all hundred layouts; the user picks one from a
select in Home Assistant or on the device, and the choice is remembered per keyboard.

## How it works
- The keyboards are `hid` inputs ([esphome-hid](https://github.com/p1ngb4ck/esphome-hid)) from any transport:
  `usb_hid`, `bt_hid`, `ble_client_hid`.
- Sources run in parallel: a storage folder of `<name>.kbl` files (storage interface), Home Assistant through
  `relay_link` (`relay: true`), up to 4 web servers (`http_sources`); the offered list is their union.
- A layout is loaded from the storage first, then the relay, then HTTP, and handed to the selected keyboard's `hid`
  input; each keyboard's choice is saved in the preferences and restored on reconnect.

## Quick start
Chips: ESP32-S2, ESP32-S3, ESP32-P4, ESP32-S31
Needs: [esphome-hid](https://github.com/p1ngb4ck/esphome-hid),
[esphome-usb-drivers](https://github.com/p1ngb4ck/esphome-usb-drivers),
[esphome-relay-link](https://github.com/p1ngb4ck/esphome-relay-link),
[esphome-usb-host](https://github.com/p1ngb4ck/esphome-usb-host),
[esphome-storage-interface](https://github.com/p1ngb4ck/esphome-storage-interface),
[esphome-esp32-platform](https://github.com/p1ngb4ck/esphome-esp32-platform)

```yaml
external_components:
  - source: github://p1ngb4ck/esphome-keyboard-layouts@main
    components: [keyboard_layouts]
  - {source: github://p1ngb4ck/esphome-hid@main, components: [hid]}
  - {source: github://p1ngb4ck/esphome-storage-interface@main, components: [storage]}
  - {source: github://p1ngb4ck/esphome-relay-link@main, components: [fixed_containers]}
  - {source: github://p1ngb4ck/esphome-usb-drivers@main, components: [usb_hid]}
  - {source: github://p1ngb4ck/esphome-usb-host@main, components: [usb_host]}
  - {source: github://p1ngb4ck/esphome-esp32-platform@main, components: [esp32]}
storage:
usb_host:
usb_hid:
  - id: keyboard
keyboard_layouts:
  directory: /sdcard/keyboard
  keyboards: [keyboard]
```

Layout files: `script/export_hid_layouts.py <folder>` (esphome-hid). `relay` / `http_sources` add `relay_link`.

## Main options

| Option | What it does | Default |
|---|---|---|
| [`keyboards`](CONFIGURATION.md#keyboard_layouts) | HID inputs: an ID, or `id`, `name`, start `layout` | required |
| [`directory`](CONFIGURATION.md#keyboard_layouts) | storage folder with `<name>.kbl` files | - |
| [`relay`](CONFIGURATION.md#keyboard_layouts) | layouts from Home Assistant through `relay_link` | `false` |
| [`http_sources`](CONFIGURATION.md#keyboard_layouts) | up to 4 web server base URLs | `[]` |
| [`device_select`](CONFIGURATION.md#keyboard_layouts) | select of the connected keyboards | - |
| [`layout_sensor`](CONFIGURATION.md#keyboard_layouts) | text sensor with the selected keyboard's layout | - |

At least one source (`directory`, `relay`, `http_sources`) is required. File format:
[Layout files](CONFIGURATION.md#layout-files).

## Catalog
- Component `keyboard_layouts`:
  [folder](https://github.com/p1ngb4ck/esphome-keyboard-layouts/tree/main/esphome/components/keyboard_layouts),
  [options](CONFIGURATION.md#keyboard_layouts), [examples](CONFIGURATION.md#examples).
- Branches: [main](https://github.com/p1ngb4ck/esphome-keyboard-layouts/tree/main),
  [pr/keyboard-layouts](https://github.com/p1ngb4ck/esphome-keyboard-layouts/tree/pr/keyboard-layouts).
- Needed repos: [esphome-hid](https://github.com/p1ngb4ck/esphome-hid) (`hid`),
  [esphome-storage-interface](https://github.com/p1ngb4ck/esphome-storage-interface) (`storage`),
  [esphome-relay-link](https://github.com/p1ngb4ck/esphome-relay-link) (`fixed_containers`, `relay_link`),
  [esphome-usb-drivers](https://github.com/p1ngb4ck/esphome-usb-drivers) (`usb_hid`),
  [esphome-usb-host](https://github.com/p1ngb4ck/esphome-usb-host) (`usb_host`),
  [esphome-esp32-platform](https://github.com/p1ngb4ck/esphome-esp32-platform) (`esp32`); for an SD card
  [esphome-storage-drivers](https://github.com/p1ngb4ck/esphome-storage-drivers) (`sd_storage`); Bluetooth keyboards
  [esphome-bluetooth](https://github.com/p1ngb4ck/esphome-bluetooth).
- All topics together: `github://p1ngb4ck/esphome@dev`.

---

# Detailed version

`keyboard_layouts`: a keyboard layout per connected keyboard, chosen at runtime. The layouts are files - on a storage,
from Home Assistant or from a web server - so a firmware does not carry all hundred layouts, and a user picks theirs
from a select in Home Assistant or on the device. The choice is remembered per keyboard.

Keyboards come from the `hid` inputs of [esphome-hid](https://github.com/p1ngb4ck/esphome-hid): `usb_hid`
([esphome-usb-drivers](https://github.com/p1ngb4ck/esphome-usb-drivers), with `usb_host` from
[esphome-usb-host](https://github.com/p1ngb4ck/esphome-usb-host)), `bt_hid` and `ble_client_hid`
([esphome-bluetooth](https://github.com/p1ngb4ck/esphome-bluetooth)).

## Examples

Write the layout files once: `script/export_hid_layouts.py <folder>` (in esphome-hid and `dev`) creates one
`<name>.kbl` per layout and an `index.tsv`. Copy the folder to the SD card, then:

```yaml
external_components:
  - source: github://p1ngb4ck/esphome-keyboard-layouts@main
    components: [keyboard_layouts]
  - source: github://p1ngb4ck/esphome-hid@main
    components: [hid]
  - source: github://p1ngb4ck/esphome-storage-interface@main
    components: [storage]
  - source: github://p1ngb4ck/esphome-relay-link@main
    components: [fixed_containers]
  - source: github://p1ngb4ck/esphome-usb-drivers@main
    components: [usb_hid]
  - source: github://p1ngb4ck/esphome-usb-host@main
    components: [usb_host]
  - source: github://p1ngb4ck/esphome-esp32-platform@main
    components: [esp32]

storage:

usb_host:

usb_hid:
  - id: keyboard

keyboard_layouts:
  directory: /sdcard/keyboard
  keyboards:
    - keyboard
  device_select:
    name: Keyboard
  layout_sensor:
    name: Keyboard layout
```

`keyboard` is a HID input, here a `usb_hid` entry; USB host needs ESP32-S2, ESP32-S3, ESP32-P4 or ESP32-S31.
`directory: /sdcard/keyboard` needs a mounted storage at `/sdcard`, for example `sd_storage` from
[esphome-storage-drivers](https://github.com/p1ngb4ck/esphome-storage-drivers). `relay: true` and `http_sources`
add `relay_link` ([esphome-relay-link](https://github.com/p1ngb4ck/esphome-relay-link)).

## More

**[CONFIGURATION.md](CONFIGURATION.md)**: every option, layouts from Home Assistant and HTTP servers, the file
format.

## Branches

One branch per change; `main` contains every branch. Base: ESPHome `dev` at `a5a75738ad`.

- `pr/keyboard-layouts` (chips: all; needs: `pr/relay-link`
  ([esphome-relay-link](https://github.com/p1ngb4ck/esphome-relay-link)), `pr/hid`
  ([esphome-hid](https://github.com/p1ngb4ck/esphome-hid)), `pr/storage`
  ([esphome-storage-interface](https://github.com/p1ngb4ck/esphome-storage-interface))): `keyboard_layouts`: a layout
  per keyboard from sources in parallel - `directory` (storage), `relay: true` (Home Assistant through relay_link),
  `http_sources`; device select and layout sensor.

The chips are those of `keyboard_layouts` itself; the keyboard's transport adds its own (USB host: ESP32-S2,
ESP32-S3, ESP32-P4, ESP32-S31).

All topics together: `github://p1ngb4ck/esphome@dev`. `external_components` replaces components only; the ESPHome
core of the build host stays as installed.

## License

- ESPHome's own files, also where this repository changes them: the [ESPHome License](LICENSE) (MIT for the Python
  code and everything else, GPLv3 for the C++ runtime files).
- New files written for this repository (new components, their code, tests and documentation): MIT,
  [LICENSE-MIT](LICENSE-MIT).
- Third-party code keeps its own licence:
  - Layout files exported from `hid/layouts/`: generated from xkeyboard-config (freedesktop.org), MIT-style licence.

## Thanks

The layouts are generated from freedesktop.org's
[xkeyboard-config](https://gitlab.freedesktop.org/xkeyboard-config/xkeyboard-config). Everyone this project builds
on: [ATTRIBUTIONS.md](https://github.com/p1ngb4ck/esphome/blob/dev/ATTRIBUTIONS.md).

## Branch structure

Each `pr/` branch holds one part of this topic on top of upstream and says in its `BRANCH.md` what it is based on.
`main` holds all of them.

- `pr/keyboard-layouts` is based on `pr/hid` of p1ngb4ck/esphome-hid, `pr/relay-link` of
  p1ngb4ck/esphome-relay-link, `pr/storage` of p1ngb4ck/esphome-storage-interface.
