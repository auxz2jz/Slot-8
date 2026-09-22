# 3D Scan Studio

This repository is the canonical source of truth for the Android 3D Scan Studio project.

The app is intended to be a self-contained Android application written in Kotlin + Jetpack Compose. The Android device acts as the main controller and processing device. GitHub is used for development and documentation only; the finished scanner should not require GitHub or cloud processing at runtime.

## Planned scan modes

1. **Photogrammetry** — build 3D models from many overlapping photographs.
2. **Laser Line Scan** — DAVID-style structured-light/laser-line triangulation.
3. **Hybrid Scan** — combine photographic reconstruction/texture with laser-derived geometry.

## Main project documents

- `PROJECT_CHECKPOINT.md` — current recovery-safe state and exact next step.
- `TEST_RESULTS_v0.8.3.md` — complete v0.8.3 device-test analysis.
- `TEST_OUTPUT_MANIFEST_v0.8.3.sha256` — hashes for the 23 uploaded v0.8.3 test outputs.
- `SOURCE_MANIFEST_v0.8.3.sha256` — hashes for the recovered v0.8.3 Android source snapshot.
- `PROJECT_NOTES.md` — overall architecture and decisions.
- `ANDROID_FEATURES.md` — DONE / PARTIAL / TODO roadmap and test history.
- `HARDWARE.md` — camera, turntable, laser, ESP32/Arduino, and motion-control plan.
- `ENGINE_INTEGRATION.md` — photogrammetry and mesh-engine integration plan.

## Current source baseline

**v0.8.3 / versionCode 17**

Recovered Android Studio archive:

`PhotogrammetryStudioAndroid-v0.8.3-dense-recovery-fullscreen-viewer.zip`

Archive SHA-256:

`0dd2ec35195a214018c3c1695dd35e7a6bad7089a4d7b3f06fe8064c9d42e47f`

The recovered snapshot contains 41 files / 393,404 bytes.

## v0.8.3 device test — PASSED

The v0.8.3 guide completed **6 Works / 0 Problems / 0 Untested**.

The important 175-photo `test4` recovery succeeded:
- 100-camera connected/refined component
- bundle RMS 12.3533 px -> 2.2892 px
- connected-component corrected dense pair produced 30,000 points
- fusion produced a 14,191-point cloud
- readyForSurfaceReconstruction=true

The earlier v0.8.2 failure where corrected dense stereo produced no saved report/cloud is resolved.

`test3` also remained healthy and fused all six selected dense pairs into 41,017 points.

## Current next step

**v0.8.4 / versionCode 18 is prepared for device testing.** It keeps the successful v0.8.3 dense recovery and adds a fusion-candidate preflight so only pairs with both images, both refined camera centers, and a recovered shared-world pose are eligible for distributed dense fusion.

The package is `PhotogrammetryStudioAndroid-v0.8.4-fusion-preflight.zip` with SHA-256 `1fac6fdb2cffc1cc43f644b5617964af80619f30c1e41245daaf818ccd835996`.

This environment does not contain an Android SDK or usable local Gradle wrapper JAR, so the source package has not been APK-compiled here. The next step is Android Studio build/install followed by the built-in v0.8.4 regression guide. After that passes, continue to **surface / triangle mesh reconstruction**.

See `TEST_RESULTS_v0.8.3.md` for the previous device-test evidence and `PROJECT_CHECKPOINT.md` for the exact v0.8.4 test sequence.

## Recovery/checkpoint policy

GitHub is updated during development, not only at the end of a long chat. Refresh the checkpoint after meaningful code/version changes, before and after long device tests, when a bug/root cause is identified, before packaging a new build, and whenever a chat is approaching its context limit.
