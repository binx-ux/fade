# Contributing

Thanks for interest in Fade. Keep PRs focused and safe for an open educational repo.

## Before you open a PR

1. Read [WARNING.md](WARNING.md) and the disclaimer in [README.md](README.md).
2. Do **not** commit secrets: Discord webhooks, API keys, mint secrets, `.env`, license servers.
3. Do **not** commit binaries: `*.exe`, `*.dll`, `*.zip`, `x64/`, `.vs/`, packaged `FADE/` folders.
4. Document high ban-risk features clearly in the PR description.

## Local build

1. Open `External.sln` → **Release | x64** → Build  
2. Output: `x64\Release\FADE.exe`

## Adding a supported game

Follow **Adding or changing games** in [README.md](README.md).

Summary:

1. Edit `External/src/core/games/arsenal.h` — PlaceId / GameId, `Kind`, `Detect()`, `Name()`, `SupportedListShort()`.
2. Adjust cache / team / aim if the game is not Arsenal-shaped (`cache.h`, aimbot, etc.).
3. Rebuild and test attach + ESP on that place.

## Offsets

- Prefer Theo auto-update (`offset_auto.h`). See [CREDITS.md](CREDITS.md).
- If you update static dumps, put them under `offsets/` and sync `External/src/sdk/offsets.h` when needed.
- Do not vendor stolen private dump DBs; use public Theo / your own dumps you have rights to use.

## Code style

- Match existing C++ style in the file you touch.
- Prefer small, reviewable diffs over drive-by renames.

## Security reports

See [SECURITY.md](SECURITY.md).
