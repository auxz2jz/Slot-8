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


- 2026-09-21 — v0.3.0 feature matching passed user testing with approximately 30 photos. User reported 29/29 adjacent pairs as Strong, confirming the OpenCV matching stage runs successfully. Testing exposed workflow gaps rather than matcher failure: only the first eight pair details were visible, match reports disappeared after leaving/reopening the project, projects could not be deleted, and the version test guide could not capture/export tester explanations.
- 2026-09-21 — v0.3.1 prepared as a testing/persistence cleanup release. Added persistent per-project feature-match reports, automatic invalidation when photos change, scrollable View All pair results, feature-match .txt export, project deletion with confirmation, per-step Problem explanations in the test guide, and full version test-report .txt export for upload back to ChatGPT. Awaiting device test.


- 2026-09-21 — v0.3.1 passed user testing. Exported report showed 8 Works, 0 Problems, 1 Untested; the untested item was the report-export step itself, but the successfully uploaded report proves that export worked. Feature-match export for project "test2" analyzed 22 photos, detected 48,169 keypoints, had 20/21 usable-or-strong adjacent pairs, one weak pair, and reported readyForSparseReconstruction=true.
- 2026-09-21 — v0.4.0 prepared as the first real sparse-geometry milestone. Adds essential-matrix/RANSAC geometric verification, relative two-camera pose recovery, best adjacent-pair selection, sparse 3D triangulation, persistent sparse reports, rotatable point-cloud preview, sparse text export, and ASCII PLY export. This is intentionally a two-view milestone with arbitrary scale; multi-view track building and bundle adjustment remain TODO.


- 2026-09-21 — v0.4.0 passed user testing: 10 Works, 0 Problems, 0 Untested. The uploaded sparse report selected IMG_1789990472607.jpg -> IMG_1789990474100.jpg with 695 ratio-test matches, 555 RANSAC inliers, 555 recovered-pose inliers, 533 triangulated 3D points, EXIF-based focal estimate 664.5 px, 6.52° relative rotation, and readyForMultiView=true. The exported PLY contained 533 vertices. Feature matching on the current 23-photo set reported 21/22 usable-or-strong adjacent pairs and readyForSparseReconstruction=true.
- 2026-09-21 — v0.5.0 prepared as the first full-sequence multi-view sparse milestone. It preserves the v0.4 two-view seed stage, then adds sequential adjacent-camera pose chaining, transforms each connected pair's triangulated points into one shared arbitrary-scale frame, voxel-downsamples the merged cloud, persists the result, shows camera-path/pair diagnostics, exports a multi-view report and PLY, and includes a v0.5-specific in-app test guide. This stage is intentionally pre-bundle-adjustment; unit adjacent baselines and drift are explicitly reported.


- 2026-09-21 — v0.5.0 user test found one real multi-view issue. The build itself worked, but the camera chain connected only 2 of 22 cameras because the algorithm always started at photo 1 and permanently stopped at the first weak adjacent geometry link (17 recovered-pose inliers). All later pair diagnostics were therefore zero even though earlier v0.4 geometry had already shown many strong later pairs.
- 2026-09-21 — v0.5.1 prepared as a weak-link recovery fix. Multi-view now evaluates every adjacent pair first, identifies the largest contiguous usable camera component, reconstructs that component in its own shared frame, preserves diagnostics for pairs outside the selected component, and no longer zeroes every later pair after an early failure. The v0.5.1 test guide specifically verifies recovery beyond the first weak link. Bundle adjustment remains the next milestone after this recovery behavior passes.


- 2026-09-21 — v0.5.1 passed user testing: 10 Works, 0 Problems, 0 Untested. The recovered largest component spans cameras 8–21, connecting 14 cameras / 13 adjacent pairs, with 2,970 raw triangulated points and 2,866 combined points. The report marked readyForBundleAdjustment=true.
- 2026-09-21 — v0.6.0 prepared as the first shared-track bundle-refinement milestone. It builds ORB feature tracks observed in 3+ connected views, re-triangulates those shared tracks, then alternates robust 3D-point optimization with camera-translation refinement while keeping camera rotations/intrinsics fixed. It reports initial/final RMS reprojection error, persists/exports a refined sparse cloud/report, and includes a v0.6-specific in-app test guide. Full rotation/intrinsic bundle adjustment and disconnected-component graph recovery remain later milestones.


- 2026-09-21 — v0.6.0 device test completed with 10 Works, 1 tester-marked Problem, 0 Untested. The marked Problem was interpretation/UX rather than an algorithm failure: the exported bundle report shows 14 cameras refined, 471 3+ view tracks, 1,276 filtered observations, 389 refined points, and RMS reprojection error improving from 9.1838 px to 2.9032 px over 4 iterations. readyForDenseReconstruction=true. Treat v0.6 reconstruction refinement as passed.
- 2026-09-21 — v0.6.1 prepared as testing/reporting maintenance before dense reconstruction. The version-test guide is now reachable from the project list, project screen, and camera screen. RMS results are explained in plain language with percent improvement and “lower is better.” Exported version-test reports automatically embed device/build metadata plus a diagnostic snapshot of the selected project’s feature-match, sparse, multi-view, and bundle metrics. Reconstruction text reports now include app version, project ID, and stable report fingerprints; PLY headers include equivalent identity metadata. These fingerprints are intended to make duplicate uploads recognizable without relying on tester memory.


- 2026-09-21 — v0.6.1 diagnostic/reporting outputs verified. The new exports include app version, project ID, and report fingerprints. Current test2 run has 20 photos, 13 connected cameras, 463 shared tracks, 1,267 observations, 386 refined points, and RMS reprojection error improved from 9.2243 px to 2.8588 px. readyForDenseReconstruction=true. An older v0.6 PLY without the new identity metadata was also uploaded and is treated as historical/duplicate context rather than a new result.
- 2026-09-21 — v0.7.0 prepared as the first dense-stereo milestone. It preserves all prior stages, re-verifies the known-good best sparse pair, rectifies the two views, computes an OpenCV StereoSGBM disparity map on reduced working images, reprojects valid disparity pixels to 3D, filters extreme depths, caps mobile export size, persists/exports the dense-pair report and PLY, and adds dense metrics plus the current pipeline status/error message to the version-test diagnostic report. Multi-pair dense fusion remains the next milestone after device validation.
