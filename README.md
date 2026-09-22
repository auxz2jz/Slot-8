# 3D Scan Studio

This repository is the canonical source of truth for the Android 3D Scan Studio project.

The app is intended to be a self-contained Android application written in Kotlin + Jetpack Compose. The Android device acts as the main controller and processing device. GitHub is used for development and documentation only; the finished scanner should not require GitHub or cloud processing at runtime.

## Planned scan modes

1. **Photogrammetry** — build 3D models from many overlapping photographs.
2. **Laser Line Scan** — DAVID-style structured-light/laser-line triangulation.
3. **Hybrid Scan** — combine photographic reconstruction/texture with laser-derived geometry.

## Main project documents

- `PROJECT_CHECKPOINT.md` — current recovery-safe project state, last test results, current build, and exact next step.
- `SOURCE_MANIFEST_v0.8.3.sha256` — SHA-256 integrity manifest for the recovered v0.8.3 Android source snapshot.
- `PROJECT_NOTES.md` — overall architecture and decisions.
- `ANDROID_FEATURES.md` — DONE / PARTIAL / TODO roadmap and test history.
- `HARDWARE.md` — camera, turntable, laser, ESP32/Arduino, and motion-control plan.
- `ENGINE_INTEGRATION.md` — photogrammetry and mesh-engine integration plan.

## Current state

The current Android source baseline is **v0.8.3 / versionCode 17**. A complete Android Studio project was recovered from:

`PhotogrammetryStudioAndroid-v0.8.3-dense-recovery-fullscreen-viewer.zip`

Archive SHA-256:

`0dd2ec35195a214018c3c1695dd35e7a6bad7089a4d7b3f06fe8064c9d42e47f`

The recovered snapshot contains 41 files / 393,404 bytes. See `PROJECT_CHECKPOINT.md` for the exact test state and `SOURCE_MANIFEST_v0.8.3.sha256` for per-file hashes.

## v0.8.3 dense-seed recovery

v0.8.3 is a stabilization build after a 175-photo project completed matching, sparse reconstruction, multi-view reconstruction, and bundle refinement but did not create the corrected dense-pair report. Dense stereo now prefers verified pairs from the selected connected camera component and automatically tries fallback candidates if one pair fails. Dense-attempt diagnostics persist even when no dense report is created.

The point-cloud viewer is also now full-screen with yaw/pitch/roll gestures, pinch zoom, fixed orthogonal-view buttons, and optional sliders.

**Current next step:** build/install v0.8.3, run the built-in v0.8.3 test guide, and retest `test4` through dense stereo and fusion. Surface/triangle mesh reconstruction is the next geometry milestone after this recovery build passes device testing.

## Recovery/checkpoint policy

GitHub should be updated during development, not only at the end of a long chat. Create or refresh a checkpoint after meaningful code/version changes, before and after long device tests, when a bug/root cause is identified, before packaging a new build, and whenever a chat is approaching its context limit.
