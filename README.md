# 3D Scan Studio

This repository is the canonical source of truth for the Android 3D Scan Studio project.

The app is intended to be a self-contained Android application written in Kotlin + Jetpack Compose. The Android device acts as the main controller and processing device. GitHub is used for development and documentation only; the finished scanner should not require GitHub or cloud processing at runtime.

## Planned scan modes

1. **Photogrammetry** — build 3D models from many overlapping photographs.
2. **Laser Line Scan** — DAVID-style structured-light/laser-line triangulation.
3. **Hybrid Scan** — combine photographic reconstruction/texture with laser-derived geometry.

## Main project documents

- `PROJECT_NOTES.md` — overall architecture and decisions.
- `ANDROID_FEATURES.md` — DONE / PARTIAL / TODO roadmap.
- `HARDWARE.md` — camera, turntable, laser, ESP32/Arduino, and motion-control plan.
- `ENGINE_INTEGRATION.md` — photogrammetry and mesh-engine integration plan.

## Current state

A first Android Studio-ready Kotlin/Jetpack Compose prototype (v0.1) has been created outside this repository for testing. It contains project creation, CameraX capture, photo import/storage, gallery/review, basic photo-set checks, and a reconstruction-engine interface. Actual 3D reconstruction is not yet wired in.

The next step is to test that v0.1 build on-device, fix any build/runtime issues, and then begin integrating the scan architecture described here.


## v0.8.3 dense-seed recovery

v0.8.3 is a stabilization build after a 175-photo project completed matching, sparse reconstruction, multi-view reconstruction, and bundle refinement but did not create the corrected dense-pair report. Dense stereo now prefers verified pairs from the selected connected camera component and automatically tries fallback candidates if one pair fails. Dense-attempt diagnostics persist even when no dense report is created.

The point-cloud viewer is also now full-screen with yaw/pitch/roll gestures, pinch zoom, fixed orthogonal-view buttons, and optional sliders.

Surface/mesh reconstruction remains the next geometry milestone after this recovery build passes device testing.
