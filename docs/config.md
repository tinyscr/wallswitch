# Configuration

`bin/.pools` and `bin/.targets` sit next to `bin/wallswitch.exe`. Both are INI files. Comments start with `#` or `;`. Section and key names are case-insensitive. Values are trimmed.

The release zip ships placeholder files. In a source checkout, the `.example` files are templates.

## Targets

`bin/.targets` selects where wallswitch writes:

```ini
[Desktop]
Enable=true

[Terminal]
Enable=false
SettingsPath=C:\Users\<you>\AppData\Local\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\LocalState\settings.json

[Registry]
Enable=true
```

Only the literal value `true` enables a target. Any other value disables it. Missing sections are disabled.

| Target | Key | Description |
|---|---|---|
| Desktop | `Enable` | Applies the image live. |
| Terminal | `Enable` | Rewrites `backgroundImage` in Windows Terminal. |
| Terminal | `SettingsPath` | Path to `settings.json`. Relative paths resolve against the exe folder. |
| Registry | `Enable` | Writes persistence values to `HKCU\Control Panel\Desktop`. Does not refresh the desktop. |

The Terminal target only edits an existing `backgroundImage` key. If the key is missing, nothing is written and the miss is logged. If `SettingsPath` is empty or the file does not exist, the target does nothing.

If no targets are enabled, pressing a hotkey does nothing.

## Pools

`bin/.pools` defines one section per pool:

```ini
[General]
PoolDir=C:\Pictures\Wallpapers
PoolKey=F7
Mode=sync
```

| Key | Required | Description |
|---|---|---|
| `PoolDir` | yes | Folder with images. Use an absolute path. A relative path is resolved against the exe folder at startup. Only top-level files are scanned. |
| `PoolKey` | yes | Global hotkey. Empty or invalid disables the pool. |
| `Mode` | no | `random` (default) or `sync`. Any value other than `sync` is random. |

Pool names must be valid file names. A pool with an invalid name, missing folder, no supported images, or bad hotkey is disabled with a startup warning.

Use an absolute `PoolDir` if you edit it while the daemon is running.

## Hotkeys

- A bare key is only accepted for `F1` to `F12`.
- With `Ctrl`, `Alt`, `Shift`, or `Win`, the key can be a letter, digit, `space`, or `F1` to `F24`.
- A hotkey already in use by another program is not registered. wallswitch logs the failure and shows a warning.
- `PoolKey` changes need a restart. `Mode` and `PoolDir` changes are read on the next press.

## State

Each pool has a state file at `bin/state/<poolName>`:

- `queue`: images scheduled, front is next.
- `shown`: images used this cycle.

When the queue empties, wallswitch scans the pool folder, drops removed files, picks up new files, reshuffles, and resets `shown`. Delete the file to force a fresh cycle.

## Image formats

wallswitch scans `jpg`, `jpeg`, `png`, `bmp`, and `webp` files. Matching is by extension only. There is no content sniffing or conversion. WebP renders natively on Windows 11; Windows 10 needs the codec pack.

## Hardcoded settings

| Setting | Location | Default |
|---|---|---|
| Supported formats | `src/core/pool.cs` | `jpg`, `jpeg`, `png`, `bmp`, `webp` |
| Wallpaper style | `src/core/targets/regKey.cs` | Fill (10) |
| Tiling | `src/core/targets/regKey.cs` | Off (0) |

## Not supported

- Per-monitor wallpapers.
- Image exclusions.
- Format conversion.
- A startup entry.
- Success logging or success alerts.
- Configurable image extensions.
- Changing hotkeys without a restart.
