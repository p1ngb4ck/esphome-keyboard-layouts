# `pr/keyboard-layouts`

One part of the topic repo p1ngb4ck/esphome-keyboard-layouts. The whole topic, with its description, is the branch `main`.

Change of this branch: `keyboard_layouts`: a layout per keyboard from sources in parallel - `directory` (storage), `relay: true` (Home Assistant through relay_link), `http_sources`; device select and layout sensor

Based on these branches, whose changes it holds and builds on:

- `pr/hid` of p1ngb4ck/esphome-hid: `hid`: report maps, keyboard layouts (also as files), keyboards, mice, gamepads, remotes; key events carry their device; `on_key`, `on_text`, `on_pointer`, `on_control`, API actions
- `pr/fixed-containers` of p1ngb4ck/esphome-relay-link: `fixed_containers`: `FixedString`, `TextView`, `FixedList` (fixed-size texts and lists, no heap growth)
- `pr/storage` of p1ngb4ck/esphome-storage-interface: the `storage` component: registry, path / filesystem / network / raw storages, the asynchronous worker (jobs, streams), `storage.*` actions, PSRAM transfer buffer, change feed, ISO 9660 mount
