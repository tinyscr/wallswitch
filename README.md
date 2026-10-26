# wallswitch

Cycle wallpapers across the desktop, Windows Terminal, and registry persistence with one global hotkey per pool.

Background daemon, not a live wallpaper engine or a startup manager.

## Quick start

1. Download a release zip, or build `bin/wallswitch.exe` with `build.ps1`.
2. Edit `bin/.pools` and `bin/.targets`. The release zip ships placeholder files. In a source checkout, start from the `.example` files.
3. Run `bin/wallswitch.exe`. It stays in the background.
4. Press a pool hotkey to apply the next image.
5. Optional: add a Startup shortcut or Task Scheduler task.

## What it does

- One global hotkey per pool.
- `random` mode gives each enabled target a different image. `sync` gives every target the same image.
- Each pool keeps a shuffle queue so images do not repeat until the cycle drains.
- Targets: live desktop, Windows Terminal `backgroundImage`, and registry persistence.

## Requirements

- Windows 7 or later with .NET Framework 4.0 or later.
- Windows Terminal only if the Terminal target is enabled.
- Images in `jpg`, `jpeg`, `png`, `bmp`, or `webp`. WebP on Windows 10 needs the codec pack.

## Limitations

- Hotkeys are registered at startup. `PoolKey` changes need a restart. `Mode` and `PoolDir` changes reload on the next press; use an absolute `PoolDir` for live reloads.
- Random mode needs at least as many images as enabled targets. Otherwise some targets are skipped.
- The Terminal target only rewrites an existing `backgroundImage` key.
- Single wallpaper across the virtual desktop. No per-monitor control.
- No installer, auto-start, success log, or format conversion.
- State lives in `bin/state`. A read-only folder can cause repeats or skips.

## Docs

- [Usage](docs/usage.md)
- [Configuration](docs/config.md)
- [Troubleshooting](docs/troubleshooting.md)
- [How it works](docs/how-it-works.md)
- [Project history](docs/STORY.md)
