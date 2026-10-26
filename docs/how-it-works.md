# How it works

## Startup

1. A named mutex prevents a second daemon.
2. wallswitch reads `bin/.targets` and enables targets in this order: Desktop, Terminal, Registry.
3. It reads `bin/.pools` and validates each pool: name, hotkey, folder, and at least one supported image.
4. Invalid pools are disabled with warnings. Valid pools get a hotkey id.
5. A hidden WinForms form registers the hotkeys and runs the message loop.

If no pools are valid, wallswitch shows a warning dialog and exits. Other config warnings are shown once at startup.

## Hotkey dispatch

`RegisterHotKey` is called once per pool with `MOD_NOREPEAT`. `WM_HOTKEY` carries the pool id, so one hidden window can serve every pool.

On each press, wallswitch re-reads `bin/.pools` and updates the pool's `Mode` and `PoolDir` if the section still exists and the new folder exists. Use an absolute `PoolDir` for live reloads. `PoolKey` changes need a restart.

## Targets

| Target | Implementation |
|---|---|
| Desktop | `SystemParametersInfo(SPI_SETDESKWALLPAPER, ...)` |
| Terminal | Rewrites `backgroundImage` in `settings.json` with a regex. |
| Registry | Writes `HKCU\Control Panel\Desktop\Wallpaper`, `WallpaperStyle=10`, `TileWallpaper=0`. |

Apply order is Desktop, Terminal, Registry. The Terminal target writes a temp file, copies the current file to `settings.json.bak`, then uses `File.Replace`.

## Shuffle queue

Each pool has a state file at `bin/state/<poolName>` with `queue` and `shown`.

- `Next` pops the front of `queue` and moves it to `shown`.
- If the queue is empty, `NextFromPool` scans the pool folder, reshuffles, and resets `shown`.
- Entries are relative to `PoolDir`, so moving the folder does not corrupt the cycle.
- `random` advances the queue once per target. `sync` advances once and applies the same image to every target.

If random mode has fewer images than enabled targets, some targets are skipped. The queue cannot provide a different image for each target when the pool is too small.

## State writes

`bin/state/<poolName>` is written to a temp file, then moved into place. A failure is logged and the daemon keeps running. A read-only folder can cause repeats or skips.

## Source tree

```
wallswitch/
├── src/
│   ├── program.cs
│   ├── core/
│   │   ├── daemon.cs
│   │   ├── pool.cs
│   │   ├── state.cs
│   │   ├── targetRegistry.cs
│   │   └── targets/
│   │       ├── desktop.cs
│   │       ├── winTerminal.cs
│   │       └── regKey.cs
│   └── platform/
│       ├── config.cs
│       ├── hotkey.cs
│       └── faillog.cs
├── bin/
│   ├── wallswitch.exe
│   ├── .pools
│   ├── .targets
│   ├── state/
│   └── logs/
├── docs/
├── .github/
├── LICENSE
├── .gitignore
├── build.ps1
└── README.md
```
