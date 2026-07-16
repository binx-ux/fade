# Fade External (Open Source)

Windows x64 Roblox **external overlay** (not an injector) for educational / research use.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Windows%20x64-lightgrey.svg)](#requirements)
[![Offsets](https://img.shields.io/badge/offsets-Theo%20auto--update-success.svg)](#offsets)

**Repository:** https://github.com/binx-ux/fade  
**Website:** https://kynport.vercel.app/  
**Discord:** https://discord.gg/3WsFqS9hTF  
**Download (built):** https://kynport.vercel.app/downloads/FADE.exe

---

## WARNING — READ BEFORE BUILDING OR RUNNING

- This software interacts with **Roblox** memory and UI overlays. Using it in live games can violate Roblox's Terms of Use and may result in **account bans**, hardware bans, or other penalties.
- You are solely responsible for how you use this code. The authors and contributors provide it **as-is**, with **no warranty**, and accept **no liability** for bans, data loss, legal issues, or damages.
- Do **not** use this to harass players, steal accounts, distribute malware, or bypass paid anti-cheat in ways that harm others.
- Offsets can still break between Roblox updates. Prefer the Theo auto-update path below; fall back to static dumps in `offsets/` if needed.
- This project is **open source and free**. There is **no license key**, no paywall, and no unlock gate.

If you do not agree, do not build or run this software. See also [WARNING.md](WARNING.md).

---

## Architecture (short)

Fade attaches to the Roblox client from **outside** the process UI stack: DXGI / Dear ImGui overlay, external memory reads/writes, place detection via `PlaceId` / `GameId`. It is **not** a script executor and **not** an injector.

Config lives at `%USERPROFILE%\Documents\Fade\config.ini` (migrates once from an older Match-Ware folder if present).

---

## Supported games

| Game | Notes |
|------|--------|
| **Arsenal** | Primary FPS support |
| **BloxStrike** | Mesh FPS; team via attributes |
| **Counter Blox** | Mesh FPS |
| **MiscGunTest:X** | Hitbox expander forced off (ban risk) |
| **Baseplate** | Sandbox / Studio template |

Allow-list and detection live in [`External/src/core/games/arsenal.h`](External/src/core/games/arsenal.h) (`Games::` namespace). See [Adding or changing games](#adding-or-changing-games).

---

## Features (high level)

- DXGI / ImGui overlay menu
- Aimbot, triggerbot, ESP, gun mods (game-dependent)
- Local movement helpers, world lighting tweaks, Spotify mini controls
- Server browser helpers for the current place
- Theo offset auto-update on launch

Feature availability depends on offsets and the game you join.

---

## Requirements

- Windows 10/11 x64
- Visual Studio with **Desktop development with C++**
- Windows SDK + MASM
- Network access on first launch for Theo offset auto-update (or use static dumps)

---

## Build

1. Open `External.sln`
2. Configuration: **Release | x64**
3. Build Solution

Output: `x64\Release\FADE.exe` (`TargetName` is `FADE` in the vcxproj).

---

## Use

1. Join a [supported game](#supported-games) on Roblox
2. Run `FADE.exe` (run as admin only if attach fails)
3. Use the keybinds below

No key entry. Optional telemetry / webhooks are **off** in this public build unless you configure them yourself.

### Keybinds

| Key | Action |
|-----|--------|
| **Insert** or **Right Ctrl** | Toggle menu |
| **Delete** | Panic — disables features (if enabled in Options) |

---

## Offsets

Fade refreshes offsets at launch via **Theo** ([offsets.imtheo.lol](https://offsets.imtheo.lol)), implemented in `External/src/sdk/offset_auto.h`.

| Path | Purpose |
|------|---------|
| `External/src/sdk/offset_auto.h` | Theo auto-update (`/roblox/version`, `/offsets.json`) |
| `External/src/sdk/offsets.h` | Baked / compiled-in C++ header (fallback) |
| `offsets/` | Static dump folder (`version-*.json`, `active.json`, …) |
| `offsets_cache/` (next to exe) | Runtime cache written by auto-update |

Offsets are **third-party data** from Theo — not authored or owned by Fade. The API can change or go offline; see troubleshooting below.

Full attribution: [CREDITS.md](CREDITS.md).

### Offset troubleshooting

| Symptom | What to try |
|---------|-------------|
| Status stays on baked dump / offline | Check firewall / TLS; confirm `offsets.imtheo.lol` is reachable |
| Attached but features wrong after Roblox update | Wait for Theo to publish new offsets, or ForceRefresh if exposed in UI; restart Fade |
| Auto-update fails completely | Copy a fresh dump into `offsets/` and update `External/src/sdk/offsets.h` manually, then rebuild |
| Version mismatch | Delete `offsets_cache` next to the exe and relaunch |

---

## Adding or changing games

Supported experiences are **hard-coded** in:

[`External/src/core/games/arsenal.h`](External/src/core/games/arsenal.h)

Attach and mid-session gating call `Arsenal::IsSupportedPlace()` → `Games::IsSupported()` → `Games::Detect()`. If `Detect()` returns `Kind::None`, Fade refuses to attach (or pauses cheats).

### 1. Get PlaceId and GameId (UniverseId)

From a Roblox place URL, the number is usually the **PlaceId**.  
**GameId** is the universe id (`game.GameId` in Luau) — often different from PlaceId.

You can also read them once Fade is attached to a known game via DataModel offsets (`Offsets::DataModel::PlaceId` / `GameId`), or from Roblox creator dashboards / APIs.

### 2. Add constants

Near the other `k…PlaceId` / `k…UniverseId` constants:

```cpp
// https://www.roblox.com/games/<PLACE_ID>/<slug>
constexpr int64_t kMyGamePlaceId = /* PlaceId */;
constexpr int64_t kMyGameUniverseId = /* GameId / universe */;
```

For multi-place universes (like MiscGunTest:X), prefer matching **GameId**, and optionally list extra PlaceIds in an array.

### 3. Extend `Games::Kind`

```cpp
enum class Kind : int {
    None = 0,
    Arsenal,
    // …
    MyGame,
};
```

### 4. Add `IsMyGamePlace` + wire `Detect()`

```cpp
inline bool IsMyGamePlace(int64_t place, int64_t gameId)
{
    if (place == kMyGamePlaceId) return true;
    if (gameId == kMyGameUniverseId) return true;
    return false;
}

inline Kind Detect()
{
    // …
    if (IsMyGamePlace(place, gameId)) return Kind::MyGame;
    return Kind::None;
}
```

### 5. Update display strings

- `Games::Name()`
- `Games::SupportedListShort()` (error messages in `main.cpp` use this list)

### 6. Game-specific behavior (important)

Allow-listing only unlocks **attach**. Many features assume Arsenal / mesh-FPS layouts:

| Area | Files / notes |
|------|----------------|
| Player / character cache | `External/src/core/cache/cache.h` (e.g. BloxStrike `Workspace.Characters`) |
| Teams | Attribute vs `Player.Team` logic in cache |
| Aimbot / visibility | `aimbot.h`, `visibility.h` (`IsMeshFps()` for CB / BloxStrike) |
| Gun mods | `gun_mods.h` / exploits — may be Arsenal-shaped |
| Soft gates | `main.cpp` (e.g. MiscGunTest hitbox forced off) |

For a new game you will often need cache + team + aim adjustments, not only a PlaceId.

### 7. Rebuild and test

Release | x64 → join the new place → confirm attach toast shows your `Games::Name()` and ESP/aim see players.

---

## Links

| | |
|--|--|
| Site | https://kynport.vercel.app/ |
| Discord | https://discord.gg/3WsFqS9hTF |
| Changelog (hosted) | https://kynport.vercel.app/releases/external-changelog.json |
| WEAO notes | [External/WEAO.md](External/WEAO.md) |
| Theo offsets | https://offsets.imtheo.lol |
| Theo C++ auto-update docs | https://offsets.imtheo.lol/docs/cpp-auto-update |

---

## License

See [LICENSE](LICENSE) (MIT). Third-party notices: [NOTICE](NOTICE) and [CREDITS.md](CREDITS.md).

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

**Security:** [SECURITY.md](SECURITY.md) — do not commit webhooks, API keys, or license servers.

---

## Credits

- **Offsets:** Theo — [offsets.imtheo.lol](https://offsets.imtheo.lol) (see [CREDITS.md](CREDITS.md))
- Base inspiration: [metixud/RobloxExternalBase](https://github.com/metixud/RobloxExternalBase)
- UI: Dear ImGui
- JSON: nlohmann/json
- Earlier Match-Ware open-source line: [binx-ux](https://github.com/binx-ux)

---

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

---

## Disclaimer

Educational / research software. Use at your own risk. **Not affiliated with Roblox Corporation** or with Theo / offsets.imtheo.lol beyond consuming their public offset API.
