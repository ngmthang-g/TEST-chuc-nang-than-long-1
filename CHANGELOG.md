# CHANGELOG

## [v0.1.6] - 2026-08-20

### Requested
- Use user-supplied v0.1.5 as the direct source base.
- Keep screen/UI automation as foreground physical mouse click.
- Prevent multiple game windows from auto-clicking/interleaving at the same time.
- Reuse the supplied logic table where it improves arbitration without changing unrelated Auto-PK behavior.

### Changed
- Replaced per-click mouse ownership with **global CLICK SEQUENCE LEASE** for Revive repeat, Recovery, Stage and Final sequences.
- Removed scheduler-wide `TickAccount()` freeze caused only by another PID owning the mouse.
- Periodic Confirm waits for the sequence lease instead of interleaving.
- Barrier release no longer depends on mouse lease; state/route orchestration stays independent of cursor ownership.
- Added 5-second User Mouse Guard for physical move/click/wheel.
- Added injected/own-cursor filtering so tool clicks do not trigger its own guard.
- Manual TEST bypasses User Mouse Guard but refuses to cut into an active auto sequence.
- Added fail-safe release on missing sequence coordinate/client freeze and preserved existing STOP/F4/rescan release paths.

### Explicitly Not Adopted
- Background `PostMessageW` Confirm from the supplied v0.4 logic table, because the current requirement is physical mouse click.

### Build
- Local Linux container: NOT BUILT because this is a Windows-only Win32 target.
- GitHub Actions Windows x64 validation run `32376966981`: **BUILD PASS**.
- Exact v0.1.5 controller base SHA gate: PASS (`7ab8407c714e31f459cc5ddc77ae3ba249e9cb277c0301565a0d21a5e549c1cd`).
- v0.1.6 patched controller SHA gate: PASS (`8ec45d5762d5731cbe6d043aa8e28859a80dbca9bc10deac91458b9e911f919f`).
- Configure x64: PASS.
- Build Release: PASS.
- Package + artifact upload: PASS.
- Runtime ZIP SHA256: `3081db1ccfa35fe699e2b935a108ce6f793d748f9a7d1b635056f2299d56ec76`.
- Source ZIP SHA256: `27b98230f115b1f352c9bbcee5e6eef56a92585656e98ac2cd256d6a3ace0e60`.

### Runtime
- Status: **RUNTIME UNTESTED**.
- Build success does not establish game-runtime success.
- Required test: 2+ windows reaching overlapping click sequences; verify no click interleaving while route/state on other windows continues.
