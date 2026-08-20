# FEATURE: Physical Click Engine / Multi-window Arbitration

## Purpose
Guarantee that physical foreground UI automation never lets two game windows fight for the Windows cursor.

## Current Implementation

### Global owner
`mouseLeasePid_` is the only physical-click owner token.

### Atomic groups
The following retain ownership across Delay/Repeat:
- HỒI SINH/ĐẦU THAI repeat group;
- Recovery sequence;
- Stage PK + Alliance sequence;
- Final Auto + PK + Auto + Train sequence.

### Single-click users
Per-account periodic map Confirm acquires/releases one single-click lease, but only when no sequence lease is active.

Manual TEST is allowed during the 5-second user-mouse pause, but not during an active auto sequence lease.

## User Mouse Guard
`WH_MOUSE_LL` records non-injected move/click/wheel. Automatic physical clicks wait until 5 seconds after the latest physical user input.

Important: this guard pauses **automatic physical clicks only**. It does not pause route/FSM/snapshot/bridge progress.

## Failure Rules
- wrong/missing click coordinate: do not advance index; release owned lease;
- foreground/cursor/SendInput failure: release lease;
- client loading/freeze: release owner lease;
- F4/STOP/rescan: release owner lease;
- sequence complete: release lease.

## Do-Not-Break Rules
1. Do not use `mouseLeasePid_` as a reason to skip the entire `TickAccount()` of another PID.
2. Do not release the owner after every row inside an atomic sequence.
3. Do not let periodic Confirm or manual TEST cut into a retained auto sequence.
4. Do not replace physical screen/UI clicks with `PostMessageW` while current requirement remains mouse-taking click.
5. Do not mistake existing non-mouse bridge commands for cursor contention.

## Runtime Test Matrix
- A Recovery vs B Stage due simultaneously -> exactly one owner; second sequence index stays unchanged.
- A sequence delay active -> B route continues, B physical click waits.
- periodic Confirm B becomes due during A sequence -> B waits; after lease free it clicks once.
- move/click/wheel by user -> owner sequence preserves index and no automatic physical click until 5s quiet.
- F4 during sequence -> lease released; resume continues from preserved state according to existing FSM behavior.
- missing coordinate mid-sequence -> lease released and row remains pending.

## Current Runtime Status
**UNTESTED** for v0.1.6.
