# Logic Table Adaptation — v0.1.6

Source reference: user-supplied **Thần Long Tool Logic Table v0.4**.

## Adopted
- Click Sequence Lease instead of scheduler-wide click freeze.
- Route/FSM/AutoPath of other accounts continues while physical click ownership is held.
- User physical mouse input delays automatic clicks for 5 seconds without pausing route/tool logic.
- Automatic sequence request/index/repeat is preserved while click is delayed.
- Manual TEST may bypass the user-mouse delay.

## Adapted for AUTO-PK
The table was written around a broader consolidator workflow; v0.1.6 applies the arbitration principles only to existing AUTO-PK physical-click groups:
- revive;
- Recovery;
- Stage PK/Alliance;
- Final Auto/PK/Auto/Train;
- per-account periodic Confirm as a single-click consumer.

## Explicitly rejected due to newer requirement
The table's Lâu Lan Confirm proposal used background `PostMessageW`. AUTO-PK v0.1.6 does **not** adopt that mechanism. Periodic Confirm remains a foreground physical click from v0.1.5.

## Preserved unrelated behavior
No Sell/Trade-specific logic from the table is transplanted into AUTO-PK.
