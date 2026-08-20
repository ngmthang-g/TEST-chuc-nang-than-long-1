# SOURCE PROVENANCE — AUTO-PK v0.1.6

The user supplied `AutoPK_Source_v0.1.5` as the mandatory direct base.

## Verified local identities
- User-supplied ZIP SHA256: `c71ccb9b5c5bab6ea3d010467d1b969744471a49efdee0f3c56df563869065d2`
- Extracted v0.1.5 `src/controller.cpp` SHA256: `7ab8407c714e31f459cc5ddc77ae3ba249e9cb277c0301565a0d21a5e549c1cd`
- v0.1.6 `src/controller.cpp` SHA256 after this patch: `8ec45d5762d5731cbe6d043aa8e28859a80dbca9bc10deac91458b9e911f919f`
- v0.1.6 patch SHA256: `63a6d5602b98760f8c2ebc57cf8316cc63a11e521ab086821d65ace8e7460a50`

## Why this repository stores a patch
The connected GitHub write interface is text-file oriented, while the full controller is large. This repo therefore stores the exact v0.1.5→v0.1.6 plaintext patch plus hashes and a reproducible CI reconstruction path.

The pinned `ngmthang-g/AUTO-PK` commit `afbc8305f185e80d025b34352781a9120bc2ba10` is used only as a transport source for the already-verified v0.1.5 lineage chunks and `vendor/r3` build dependency. CI reconstructs the controller and refuses to proceed unless its SHA256 equals the user-supplied base controller hash above.

Therefore the build cannot silently switch to another controller source.

## Output
GitHub Actions produces both:
- `ThanLongAutoPK-v0.1.6-win-x64.zip`
- `AutoPK_Source_v0.1.6.zip` (fully reconstructed source tree)
