# Usage

wallswitch runs hidden in the background. It registers one global hotkey per valid pool and waits. There is no window, tray icon, or service.

## Launch

Run `bin/wallswitch.exe` once. A second launch exits because a named mutex is already held.

To start it at logon, add a Startup shortcut or a Task Scheduler task. wallswitch does not install one.

## Pools and hotkeys

Each section in `bin/.pools` is a pool. Pressing the pool's hotkey applies the next image to each enabled target.

- `random`: each enabled target gets a different image.
- `sync`: all enabled targets get the same image.
- `Mode` and `PoolDir` changes are read on the next press. Use an absolute `PoolDir` if you change it while running.
- `PoolKey` changes need a restart.
- A bare key is only accepted for `F1` to `F12`.
- With `Ctrl`, `Alt`, `Shift`, or `Win`, the key can be a letter, digit, `space`, or `F1` to `F24`.
- If a hotkey is already in use, registration fails and a warning is shown at startup.

## Targets

| Target | What it does |
|---|---|
| Desktop | Applies the image live with `SystemParametersInfo`. |
| Terminal | Rewrites `backgroundImage` in Windows Terminal `settings.json`. |
| Registry | Writes `HKCU\Control Panel\Desktop\Wallpaper` and style values for reboot persistence. |

Apply order is Desktop, Terminal, Registry.

If no targets are enabled, pressing a hotkey does nothing. The Registry target writes persistence values but does not refresh the desktop; enable Desktop for an immediate change.

The Terminal target edits an existing `backgroundImage` value only. If the key is not present, nothing is written and the miss is logged. Each successful edit saves the previous file to `settings.json.bak`. If `SettingsPath` is empty or the file does not exist, the target does nothing.

## Shuffle state

Each pool has a state file at `bin/state/<poolName>`. It holds `queue` and `shown`.

- `queue`: images scheduled, front is next.
- `shown`: images used this cycle.

When the queue empties, wallswitch scans the pool folder, drops removed files, picks up new files, reshuffles, and resets `shown`. Delete the state file to force a fresh cycle.

## Logs

There is no success log. Runtime failures append to `bin/logs/fail.log`. Startup validation warnings and hotkey registration failures also show a message box.

## Exit behavior

- No usable pools: warning dialog, then exit.
- Second instance: exits immediately.
- Runtime failure: logged, daemon keeps running.
