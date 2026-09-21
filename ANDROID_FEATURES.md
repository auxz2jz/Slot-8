# Android Feature Roadmap

Status legend:
- **DONE** — implemented in the current prototype.
- **PARTIAL** — scaffolding/interface exists but feature is incomplete.
- **TODO** — planned.

## Foundation

- DONE — Kotlin Android project.
- DONE — Jetpack Compose UI foundation.
- DONE — Local scan/project creation.
- DONE — CameraX photo capture.
- DONE — Import multiple existing photos.
- DONE — Local project/photo storage.
- DONE — Photo thumbnail gallery.\n- DONE — v0.2: project page scrolls vertically and photo thumbnails scroll horizontally for easier review.
- DONE — Delete unwanted photos.
- DONE — Basic photo-set analysis.
- DONE — Warnings for too few photos, changing resolution, and focal-length/zoom changes.
- DONE — Basic capture guidance.
- PARTIAL — Reconstruction-engine interface.
- TODO — Actual on-device 3D reconstruction.
- TODO — Move the tested Android source into this repository after v0.2 is confirmed building.

## Main scan modes

- PARTIAL — Project creation supports Photo Scan / Laser Scan / Hybrid Scan in v0.2; dedicated home-mode workflow can be refined later.
- PARTIAL — Scan mode is persisted in project metadata; new projects create photo/laser/depth/model folders. Full shared data model is still TODO.
- TODO — Advanced/manual controls.
- TODO — Simple automatic workflow.

## Photogrammetry

- TODO — Smart automatic capture based on camera movement/view change.
- TODO — Guided coverage/overlap feedback.
- TODO — Quality presets: Medium / High / Original-Max.
- TODO — Storage/workspace estimates.
- TODO — Multiple elevation/ring capture workflow.
- TODO — Background/object masking for turntable scans.
- TODO — Native photogrammetry engine integration.
- TODO — Point-cloud preview.
- TODO — Mesh generation.
- TODO — Texture generation/application.
- TODO — Export common 3D formats.

## Laser scanning

- TODO — Calibration-target workflow.
- TODO — Camera calibration.
- TODO — Laser-plane calibration.
- TODO — Red/green/blue laser detection profiles.
- TODO — Locked camera settings for calibrated scans.
- TODO — Laser stripe extraction.
- TODO — Subpixel laser-line localization where practical.
- TODO — Triangulation to 3D points.
- TODO — Live point-cloud preview.
- TODO — Multi-pass point-cloud alignment.
- TODO — Mesh generation from laser scans.

## Hybrid scanning

- TODO — Associate photo and laser passes in one project.
- TODO — Align photogrammetry and laser coordinate systems.
- TODO — Fuse point clouds/meshes.
- TODO — Use photo textures on fused geometry.
- TODO — Quality/confidence visualization.

## External camera sources

- DONE — Built-in phone camera foundation.
- TODO — Camera-source interface.
- TODO — Reolink RLC-811A RTSP preview.
- TODO — Reolink high-resolution snapshot capture.
- TODO — Save calibration profiles by Reolink zoom/settings.
- TODO — USB UVC camera support.
- TODO — Arducam OV2311/B0322 proof-of-concept.
- TODO — Optional external hardware trigger support.

## Motion control

- TODO — Motion-controller interface.
- TODO — USB serial transport.
- TODO — Bluetooth/BLE transport.
- TODO — Wi-Fi transport.
- TODO — ESP32 reference firmware/protocol.
- TODO — Turntable position/speed control.
- TODO — Laser carriage position/speed control.
- TODO — Optional laser-angle axis control.
- TODO — Laser on/off control.
- TODO — Homing/limit-switch support.
- TODO — READY/error/position feedback.
- TODO — Emergency stop / abort scan.

## Automated workflows

- TODO — Step-and-capture laser scan.
- TODO — Automated photogrammetry turntable scan.
- TODO — Hybrid photo + laser sequence.
- TODO — Resume interrupted scan.
- TODO — Continuous-motion scan experimentation after step-and-capture works.

## Project management

- TODO — Project metadata and calibration profiles.
- TODO — Per-project storage usage display.
- TODO — Free-space checks.
- TODO — Export/import complete scan project.
- TODO — Diagnostics/logging.


## Build test history

- 2026-09-21 — v0.1 first Android Studio build reached Kotlin compilation but failed in `ProjectScreen.kt` because of an explicit `import androidx.compose.foundation.layout.weight`. Current Compose exposes `weight` through `RowScope` / `ColumnScope`; the explicit import resolved to an internal member. Fixed by removing that import and repackaged as v0.1.1. Awaiting retest.

- 2026-09-21 — v0.1.1 successfully compiled, installed, opened, and captured photos on the user's Android test device. User reported photo review needed better scrolling. v0.2 prepared with vertically scrollable project content, horizontally scrollable photo strip, persisted Photo/Laser/Hybrid scan modes, and backward compatibility for v0.1 projects. Awaiting v0.2 build test.


## Structured-light / depth scanning

- TODO — Depth-camera source abstraction.
- TODO — Kinect-style structured-light/depth proof of concept.
- TODO — USB depth-camera SDK integration.
- TODO — Depth-frame preview.
- TODO — Convert depth frames to point clouds.
- TODO — Register multiple turntable views.
- TODO — Fuse depth geometry with photogrammetry and laser-line scans.


## Permanent test-guide rule

Starting with Android v0.2.1, every future test build must include an in-app **Test This Version** walkthrough.

Requirements:
- The guide appears automatically the first time each new app version is launched.
- The guide is always accessible later from the main screen.
- Each step tells the tester exactly what to do.
- Each step states the expected result.
- Each step can be marked **Works** or **Problem**.
- Test progress/results should persist when the tester temporarily closes the guide to perform a test.
- The guide must focus on features added or changed in that version and include important regression checks.
- A build should not be considered fully tested until its version-specific guide has been worked through or intentionally skipped with notes.

v0.2.1 adds this test-guide system. Its test plan covers scan-mode creation, camera capture, horizontal photo-strip scrolling, vertical project-page scrolling, image import/delete, Analyze, and Laser/Hybrid placeholder screens.


- 2026-09-21 — v0.2.1 test-guide build failed at Kotlin compilation in `ProjectsScreen.kt` because `IconButton` was used without importing `androidx.compose.material3.IconButton`. Fixed the missing import, bumped the test build to v0.2.2/versionCode 4, and retained the same version-specific test guide. Awaiting v0.2.2 build test.


- 2026-09-21 — v0.2.2 passed user testing: build/install/open, Photo/Laser/Hybrid project creation, camera capture, scrolling, import/delete, and test-guide behavior all reported working.
- 2026-09-21 — v0.3.0 prepared. This is the first real on-device photogrammetry computation stage. Added official OpenCV Android AAR (4.14.0), ORB feature detection, adjacent-photo Hamming k-NN matching with ratio filtering, overlap-quality labels, keypoint/match statistics, warnings for weak neighboring views, progress reporting, and v0.3-specific in-app test guide. Awaiting build/device test.
