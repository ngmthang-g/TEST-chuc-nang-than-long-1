# VERSION v0.1.6

## A. Identity / Lineage
- Version: v0.1.6
- Date: 2026-08-20
- Based On: user-supplied AUTO-PK v0.1.5
- Base controller SHA256: `7ab8407c714e31f459cc5ddc77ae3ba249e9cb277c0301565a0d21a5e549c1cd`
- Reason Created: make multi-window physical clicking mutually exclusive at sequence level without freezing route/state of other windows.
- Last Known-Good: v0.1.5 business flow inherited; v0.1.6 arbitration runtime not yet proven.

## B. User Requests
- Keep v0.1.5 as source foundation.
- All screen/UI clicks remain mouse-taking foreground clicks.
- Only one window can auto-click at a time; windows must not fight for cursor ownership.
- Use supplied logic table when applicable.

## C. State Before Modification
v0.1.5 had a global `mouseLeasePid_`, but `ClickSlot` released it immediately after each physical mouse-up. A sequence with Delay/Repeat therefore did not remain atomic. `TickAccount()` also returned early for other PIDs while a mouse owner existed, coupling mouse arbitration to scheduler-wide state progression.

## D. Root Cause
**CONFIRMED from source:** lease scope was one physical click rather than one macro/sequence, while a separate scheduler-wide early return blocked other account state machines.

This creates two undesirable properties at once:
1. another account can acquire the cursor between rows/repeats of the first account's macro;
2. non-mouse work of other accounts is unnecessarily frozen while any click is in progress.

## E. Changes Made

### Click engine
- `ClickSlot(... retainLease, manual)` now supports retaining ownership.
- Recovery/Stage/Final call it with `retainLease=true` and release only on sequence completion/failure condition.
- Revive repeat group also retains the lease until the configured group is sent.
- Periodic Confirm is still a physical single click and never cuts into any retained lease.

### Scheduler
- Removed `if (mouseLeasePid_ && mouseLeasePid_ != pid) return` from `TickAccount`.
- Snapshot/FSM/route/bridge can progress on other accounts while one PID owns physical clicks.

### User Mouse Guard
- Added `WH_MOUSE_LL` hook.
- Non-injected move/click/wheel defers automatic physical clicks 5000 ms.
- Tool-injected events and own cursor movement are filtered.
- Existing sequence position/repeat counters are preserved while click is deferred.

### Fail-safe
- Missing coordinate inside an already-owned sequence releases lease but retains sequence index.
- Client freeze releases an owner lease.
- Existing F4/STOP/rescan release behavior is preserved.

## F. Important Implementation Details
- User Mouse Guard: 5000 ms.
- Own cursor ignore window: 1500 ms.
- Physical click path remains foreground + `SetCursorPos` + `SendInput`.
- No background `PostMessageW` UI click was introduced.
- Existing bridge `ClickNpc`/route/ride commands are preserved and are outside mouse-lease scope.

## G. Files Changed / Added
- Modified: `src/controller.cpp`, `CMakeLists.txt`, `README.md`, `PROJECT_KNOWLEDGE.md`.
- Added: `CHANGELOG.md`, click-engine/history/decision/reference docs, GitHub Actions build workflow.

## H. Build / CI
- Local: NOT BUILT (Windows-only target; current authoring environment is Linux).
- CI: use repository GitHub Actions Windows x64 workflow.

## I. Runtime Result
- RUNTIME: **UNTESTED**.
- Awaiting test: overlapping sequences on 2+ clients, manual mouse interruption, periodic Confirm due during another sequence, F4/STOP mid-sequence.

## M. Handoff
If runtime reports cursor conflict, inspect lease acquisition/release logs first. Do not reintroduce scheduler-wide freeze. If a sequence stalls, inspect the row coordinate/guard and ensure failure releases lease without advancing the sequence index.
