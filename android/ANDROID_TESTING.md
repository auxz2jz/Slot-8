# Android Testing Ownership

Android testing remains independent from Windows testing.

The existing Android built-in "Test This Version" workflow, Android test reports, test-output manifests, and device-test history remain Android-owned.

Authoritative Android recovery/testing history:
- `../PROJECT_CHECKPOINT.md`
- root `TEST_RESULTS_*.md` files
- root `TEST_OUTPUT_MANIFEST_*.sha256` files
- Android version/build notes.

A Windows test must never change Android verification status, and an Android test must never verify Windows.
