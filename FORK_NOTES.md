# Fork notes

This fork tracks upstream [HenryCauan/rovyl](https://github.com/HenryCauan/rovyl) and
carries a few local fixes we hit while running Rovyl day to day on Windows 11. Everything
here is meant to eventually flow back upstream — this file is just so we don't forget what
diverged and why.

## What's patched

- **Middle-mouse trigger survives hook eviction.** Windows silently drops a low-level
  mouse hook (`WH_MOUSE_LL`) whenever the callback misses the `LowLevelHooksTimeout`
  window. When that happened the wheel just stopped opening until a restart. The hook host
  now re-arms itself on a short timer: it installs a fresh hook *before* releasing the old
  one, so there's never a gap where the trigger is dead.

- **Relaunch opens the hub instead of a blank overlay.** Launching a second instance used
  to half-unhide the transparent overlay window — you'd get a taskbar entry and nothing
  visible. The second-instance handler now opens the settings hub the same way the tray
  "Open Settings" entry does.

- **Auto-updater off by default for source builds.** A fork build shouldn't try to pull
  and install signed releases from upstream over the top of itself. The updater is disabled
  unless `ROVYL_ENABLE_UPDATER=1` is set, which also stops the packaged app from clobbering
  a locally built install.

## Building this fork

Same as upstream (Windows 10/11, Node 20+):

```bash
git clone https://github.com/muhammadbinriaz/rovyl
cd rovyl
npm install
npm start
```

`npm run dist` produces the installer under `build-out/`. If a previous install is running,
close it (and any lingering `mouse-blocker.ps1` process) before reinstalling, otherwise the
packaged `app.asar` stays locked and the new build won't overwrite it.

## Upstream

Fixes are sent back as pull requests against
[HenryCauan/rovyl](https://github.com/HenryCauan/rovyl). Check the PR list there for the
current status of each item above.
