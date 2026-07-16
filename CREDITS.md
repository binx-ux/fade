# Credits

Fade External is MIT-licensed. This file lists third-party projects and data sources.

## Offsets (Theo)

| | |
|--|--|
| **Provider** | **Theo** |
| **Site / API** | [https://offsets.imtheo.lol](https://offsets.imtheo.lol) |
| **Docs** | [C++ auto-update](https://offsets.imtheo.lol/docs/cpp-auto-update) |
| **Used by Fade** | `External/src/sdk/offset_auto.h` — HTTPS fetch of Roblox client version + offset JSON at launch; cache under `offsets_cache/` next to the executable |

Offset values are **published by Theo**, not by Fade. Fade only downloads and applies them at runtime (with a baked `offsets.h` / `offsets/` dump as fallback).

If you redistribute Fade, keep this attribution. Do not claim Theo’s dumps as your own work.

## UI

- [Dear ImGui](https://github.com/ocornut/imgui) — MIT — vendored under `External/ext/imgui`

## JSON

- [nlohmann/json](https://github.com/nlohmann/json) — MIT — vendored under `External/ext/nlohmann`

## Base / inspiration

- [metixud/RobloxExternalBase](https://github.com/metixud/RobloxExternalBase) — structural inspiration for an external Roblox overlay

## Project history

- Earlier public line shipped as **Match-Ware** / `open-source-roblox-cheat-`; this tree is branded **Fade** ([binx-ux/fade](https://github.com/binx-ux/fade)).

## Maintainer

- [binx-ux](https://github.com/binx-ux) — Fade / kynport

## Not affiliated

Roblox Corporation, Theo / offsets.imtheo.lol (beyond use of public offset endpoints), and any listed games’ developers are not affiliated with this project.
