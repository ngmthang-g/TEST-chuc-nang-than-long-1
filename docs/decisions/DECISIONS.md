# DECISIONS

## DEC-001 — Sequence-scoped physical mouse lease
- Date / Version: 2026-08-20 / v0.1.6
- Status: ACTIVE
- Decision: one PID owns physical UI clicking for an entire logical macro sequence, not only one down/up pair.
- Context: multi-window automation must not interleave cursor clicks.
- Rejected: scheduler-wide freeze of every other PID.
- Reason: cursor serialization and state/route concurrency are separate concerns.

## DEC-002 — Preserve physical UI click requirement
- Date / Version: 2026-08-20 / v0.1.6
- Status: ACTIVE
- Decision: keep UI/screen rows and periodic Confirm on foreground `SetCursorPos` + `SendInput`.
- Rejected: background `PostMessageW` Confirm from the external logic-table reference.
- Reason: current user requirement explicitly retains mouse-taking clicks.

## DEC-003 — User mouse pauses auto-click only
- Date / Version: 2026-08-20 / v0.1.6
- Status: ACTIVE
- Decision: 5s physical-mouse guard delays automatic cursor actions but does not pause snapshot/FSM/route/bridge execution.
