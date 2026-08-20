# AUTO-PK PROJECT KNOWLEDGE

## Project Identity
- Current version: **v0.1.6**
- Based on: **user-supplied v0.1.5 source**
- Base controller SHA256: `7ab8407c714e31f459cc5ddc77ae3ba249e9cb277c0301565a0d21a5e549c1cd`
- v0.1.6 controller SHA256: `8ec45d5762d5731cbe6d043aa8e28859a80dbca9bc10deac91458b9e911f919f`
- Platform: Windows x64 / C++17 / Win32

## Current State

### Preserved behavior from v0.1.5
`DEAD? -> revive if needed -> healer -> RecoveryClicks -> RouteStaging -> StageClicks(PK,Alliance) -> WaitStaging barrier -> RouteMain -> FinalClicks(Auto,PK,Auto,Train) -> VerifyFinalAuto -> Monitor`

Per-account periodic map Confirm remains timer + saved coordinate, as in v0.1.5.

### v0.1.6 requested behavior
- UI/screen auto clicks remain physical foreground mouse clicks.
- At most **one game window owns physical auto-click execution at a time**.
- An active macro owns a **CLICK SEQUENCE LEASE**, not just one down/up pair.
- Other accounts do not receive physical auto clicks until the owner sequence ends/fails/yields.
- Other accounts' read-only state/FSM/AutoPath/route/bridge work continues; mouse ownership is not a scheduler-wide freeze.
- User physical mouse input defers automatic physical clicking for 5 seconds after the last move/click/wheel event.

## Hard Invariants

### INV-CLICK-001 — one physical click owner
`mouseLeasePid_ == 0` or exactly one PID. A second PID cannot call the physical click path until lease release.

### INV-CLICK-002 — sequence atomicity
Revive repeat group, Recovery, Stage PK/Alliance, and Final Auto/PK/Auto/Train retain the lease across row delay/repeat boundaries.

### INV-CLICK-003 — no scheduler-wide freeze
A lease may prevent another PID from acquiring a **physical click**, but it must not stop that PID from snapshot refresh, FSM evaluation, AutoPath or non-mouse bridge commands.

### INV-CLICK-004 — physical UI click only
`ClickSlot` and per-account periodic Confirm use foreground + cursor + `SendInput`. v0.1.6 does not introduce `PostMessageW` background click.

### INV-CLICK-005 — manual mouse guard
A non-injected physical move/click/wheel updates `lastPhysicalMouseTick_`; automatic physical click is deferred until 5000 ms have elapsed. Tool-injected events/own cursor warp are filtered.

### INV-CLICK-006 — lease cannot starve forever on local failure
Click failure, missing coordinate, client freeze, STOP/F4, rescan, or sequence completion releases an owner lease as appropriate.

## Important distinction
Existing v0.1.5 bridge commands (`StartPath`, `StopPath`, ride toggle, `ClickNpc`) remain unchanged. They are not foreground cursor click macros and therefore do not participate in the physical mouse lease.

## Source-derived facts preserved
- Lâu Lan = MapID 5.
- Đỗ Thanh Đằng = NPC ID 339 (`LangZhong1`).
- `ValidLifeState/dead` remains authoritative for revive path.
- `ValidAutoFight/autoFight` remains final AutoFight observer.
- Stage and Main keep independent MapID values.

## Runtime Status
- SOURCE REVIEW: **PASS**
- BUILD: **PASS** — GitHub Actions Windows x64 validation run `32376966981`.
- BUILD ARTIFACT: `ThanLongAutoPK-v0.1.6`, artifact ID `9409430656`.
- RUNTIME ZIP SHA256: `3081db1ccfa35fe699e2b935a108ce6f793d748f9a7d1b635056f2299d56ec76`.
- SOURCE ZIP SHA256: `27b98230f115b1f352c9bbcee5e6eef56a92585656e98ac2cd256d6a3ace0e60`.
- RUNTIME: **UNTESTED**
- KNOWN-GOOD: v0.1.5 business flow is inherited as the base; v0.1.6 click arbitration is source/build-verified but not game-runtime-confirmed yet.

## Do-not-break notes
- Do not turn `mouseLeasePid_` back into a blanket `TickAccount()` early-return for other PIDs.
- Do not release the lease after every row click inside Recovery/Stage/Final.
- Do not add background `PostMessageW` UI click while the physical-click requirement is active.
- Do not let periodic Confirm or TEST interleave with a retained auto sequence lease.
- Do not mark BUILD PASS as RUNTIME PASS.

## Knowledge Index
- `CHANGELOG.md`
- `docs/history/VERSION_v0.1.6.md`
- `docs/features/CLICK_ENGINE.md`
- `docs/decisions/DECISIONS.md`
- `docs/reference/LOGIC_TABLE_ADAPTATION_v0.1.6.md`
- `docs/CI_VALIDATION_v0.1.6.md`
