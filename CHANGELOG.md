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
- Local Linux container cannot build this Windows-only Win32 target.
- GitHub Actions is the authoritative build check for Windows x64.

### Runtime
- Status: **RUNTIME UNTESTED**.
- Required test: 2+ windows reaching overlapping click sequences; verify no click interleaving while route/state on other windows continues.
