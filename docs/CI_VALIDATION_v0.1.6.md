# CI Validation v0.1.6

This file exists to trigger and preserve a pull-request Windows x64 validation run for AUTO-PK v0.1.6.

Validation gates are implemented in `.github/workflows/build-autopk.yml`:
- reconstruct and SHA-verify exact v0.1.5 controller base;
- normalize and SHA-verify the v0.1.6 patch;
- SHA-verify the final v0.1.6 controller;
- configure/build Windows x64;
- package runtime and reconstructed source artifacts.

A successful CI run proves build/package integrity only. It does not replace live multi-window game runtime testing.
