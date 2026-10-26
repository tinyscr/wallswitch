# Troubleshooting

## Nothing happens when I press a hotkey

- Check that `bin/wallswitch.exe` is running.
- Check that at least one target is enabled in `bin/.targets`.
- Check `bin/logs/fail.log`.
- Check the startup warning dialog for invalid pools or hotkey conflicts.
- A bare key must be `F1` to `F12`.
- A hotkey already used by another program is not registered. Choose another.
- `PoolKey` changes need a restart.

## No usable pools

wallswitch shows a warning and exits when every pool is invalid. Check:

- `bin/.pools` exists and has at least one section.
- `PoolDir` exists and contains a supported image at the top level.
- `PoolKey` is not empty and parses.
- The pool name is a valid file name.

## Terminal target does not change

- `[Terminal] Enable=true`.
- `SettingsPath` points to the existing `settings.json`.
- The file already contains a `backgroundImage` key.
- Check `bin/logs/fail.log` for `no "backgroundImage" match`.
- If `SettingsPath` is empty or the file does not exist, the target does nothing and does not log.
- A successful edit writes `settings.json.bak` next to the file.

## Images repeat or targets are skipped

- In `random` mode, the pool needs at least as many images as enabled targets. Otherwise some targets are skipped.
- Delete `bin/state/<poolName>` to force a fresh cycle.
- A read-only `bin/state` folder prevents state writes. The failure is logged and the cycle may repeat or skip.

## WebP images do not show

WebP renders natively on Windows 11. On Windows 10, install the WebP codec pack.

## The second launch exits

That is expected. A named mutex allows only one daemon. Check Task Manager or the Startup shortcut if you expected the first one to be running.

## No window or tray icon

wallswitch is a hidden Windows GUI app. It has no window, tray icon, or success log. Check `bin/logs/fail.log` for runtime failures.

## Startup

wallswitch does not install a startup entry. Add a Startup shortcut or a Task Scheduler task yourself.
