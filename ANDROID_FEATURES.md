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


- 2026-09-21 — v0.7.0 passed all 10 functional tests on the user's Galaxy S22 Ultra, including dense-pair reconstruction, export, persistence, and invalidation. The exported dense report produced 48,769 valid disparity pixels and 3,289 filtered points and marked readyForDenseFusion=true. The user reported that the dense preview looked cone-shaped/stretched. Review showed this is a dense-quality issue, not a failure of the overall pipeline direction.
- 2026-09-21 — v0.7.1 prepared as an on-track dense-quality correction before multi-pair fusion. The dense matcher now derives its SGBM disparity window from rectified recovered-pose inliers, reports observed low/median/high disparity, optionally filters dense candidates using the verified sparse pair's broad radius/depth band, and uses robust 95th-percentile point-cloud preview scaling so isolated far outliers do not determine the zoom. The v0.7.1 test guide exports diagnostics before any photo-change invalidation. Multi-pair dense fusion remains the next major milestone after this correction passes.


- 2026-09-21 — v0.7.1 passed user testing: 10 Works, 0 Problems, 0 Untested. Diagnostic snapshot preserved all prior stage metrics. The corrected dense-pair run produced 52,231 valid disparity pixels and 3,464 exported dense points; sparse-depth guidance retained 0 candidates and therefore fell back to dense filtering, so the earlier stretched/cone-like appearance remains a quality observation rather than a functional failure.
- 2026-09-21 — v0.8.0 prepared as the first multi-pair dense-fusion milestone. It rebuilds connected camera orientations, uses v0.6 refined camera centers, densifies a distributed subset of connected stereo pairs, rejects extremely low-parallax or too-sparse pair clouds, transforms accepted dense points into the shared world frame, performs robust global outlier trimming and voxel downsampling, persists/exports pair diagnostics plus a fused PLY, and adds a v0.8-specific test guide. Surface/mesh reconstruction remains the next milestone after device validation.


- 2026-09-21 — v0.8.0 was exercised on the new test3 set with 95 photos. Feature matching was very strong (93/94 adjacent pairs usable/strong), sparse reconstruction produced 1,291 points on the best pair, the largest multi-view component connected 41/95 cameras with 19,400 combined points, and bundle refinement improved RMS from 8.6746 px to 2.2359 px. The corrected single-pair dense seed produced 713 points and therefore reported readyForDenseFusion=false under the old 1,000-point seed threshold. Because the v0.8 UI/ViewModel gated fusion on that flag, multi-pair fusion never got a chance to evaluate the remaining connected stereo pairs.
- 2026-09-21 — v0.8.1 recovered after interrupted tooling check. Adds Single / Rapid Hold / Rapid Start-Stop capture, target intervals 5/sec, 2/sec, 1/sec, every 2 sec, every 5 sec, optional 3-second delayed start for mounted/rotating rigs, serialized full-resolution ImageCapture requests, one project refresh after each Rapid sequence, EXIF orientation/mirror normalization across all OpenCV reconstruction stages and thumbnails, full EXIF orientation audit in the version test report, portrait/landscape-safe photo-size checks, and removal of the old 1,000-point single-pair fusion gate. test3's non-empty 713-point dense seed can now launch fusion so the fusion engine can judge connected pairs itself.
- 2026-09-21 — Recovery root cause for the earlier hour-long “Checking available Kotlin and Gradle tooling” stall: the project has no bundled gradle-wrapper.jar and this runtime has no system Gradle. The custom wrapper tries to bootstrap the JAR from raw.githubusercontent.com, but this runtime cannot resolve that host. v0.8.1 changes the bootstrap scripts to fail fast with short network timeouts instead of appearing hung. Android Studio remains the actual compile/sync check.


- 2026-09-22 — v0.8.1 user testing: version guide reported 6 Works / 3 Problems. Reconstruction/fusion itself succeeded. test3 reached 41 connected cameras and fused 6 dense pair clouds into 40,836 points. test4 used 20 photos, connected 14 cameras, improved bundle RMS from 9.3233 px to 1.8803 px, produced 29,264 single-pair dense points, and fused 6 pair clouds into 59,801 points. Multi-pair dense fusion is therefore functionally validated for this milestone.
- 2026-09-22 — v0.8.1 issues found: Hold Rapid stopped after one photo; 5/sec full-resolution capture only sustained about 1–2/sec on-device; rotating the phone while the camera screen was open recreated navigation and returned the user to the main/project screen and reopened the guide. User also requested full yaw/pitch/roll inspection instead of a one-axis point-cloud orbit, and optional notes on successful test steps.

## v0.8.2 — Rapid reliability + 3-axis cloud inspection

- Fix Hold Rapid by keeping the press pointer coroutine alive while rapidActive changes.
- Record requested interval, saved count, elapsed time, actual photos/sec, average full-resolution save latency, and stop reason for recent Rapid runs.
- Treat fast Rapid choices as target rates; hardware-limited actual rates are diagnostics, not automatic failures.
- Preserve current app screen/test-guide state and capture style/interval/countdown across configuration recreation.
- Upgrade the shared point-cloud viewer to yaw/pitch/roll, pinch zoom, two-finger roll, sliders, Reset, and Front/Back/Left/Right/Top/Bottom presets.
- Allow optional tester notes on Works as well as Problems.
- Export recent Rapid measurements in the version test report.
- Surface/mesh reconstruction remains the next geometry milestone after v0.8.2 device validation.


- 2026-09-22 — v0.8.2 user test: 8 Works, 1 Problem. Rapid Hold now works; measured full-resolution throughput on the Galaxy S22 Ultra is about 1.5–1.7 photos/sec at the fastest targets, while 1/sec hits target. EXIF normalization handled a mixed-orientation 175-photo test4 set. The full 3-axis viewer worked but its controls were too crowded. test3 remained healthy and reached 41,017 fused dense points.
- 2026-09-22 — test4 contained 175 photos with 174/174 adjacent pairs usable/strong. It completed sparse reconstruction (1,096 points), largest-component multi-view (100 connected cameras / 36,702 points), and bundle refinement (12.3533 px -> 2.2892 px RMS), but tapping the corrected dense-pair stage produced no saved dense report. The global best sparse pair was photos 54-55, immediately before the selected multi-view component beginning at camera 57.

## v0.8.3 — dense-seed recovery + full-screen point-cloud viewer

- DONE — Dense seed selection now prefers verified pairs inside the connected multi-view component.
- DONE — Up to six verified dense-pair candidates are attempted automatically if a pair fails.
- DONE — The original global sparse pair is only prioritized when it belongs to the selected connected component; otherwise connected candidates come first.
- DONE — Fallback pairs do not reuse unrelated sparse-depth guidance from another pair.
- DONE — Dense-attempt diagnostics persist separately from the dense report, including final failure details when no dense cloud is created.
- DONE — Dense progress/failure feedback appears next to the dense-build button.
- DONE — Point clouds now open in a full-screen viewer with fixed Front/Back/Left/Right/Top/Bottom buttons, gestures, zoom, and optional sliders.
- DONE — Preset controls no longer require horizontal scrolling and no longer push the cloud off-screen by default.
- DONE — v0.8.3 test guide specifically retests test4 dense recovery/fusion and exports the dense-attempt log.
- NEXT — Surface/triangle mesh reconstruction after this recovery build passes device testing.


- 2026-09-22 — v0.8.2 device test: 8 Works / 1 Problem. Rapid Hold now works. Galaxy S22 Ultra full-resolution Rapid throughput measured about 1.5–1.7 photos/sec at the 5/sec target, while 1/sec hit target accurately. Mixed EXIF orientations normalized successfully. The 3-axis viewer worked but controls were crowded. test3 rebuilt all stages successfully; test4 (175 photos) completed feature matching, sparse, 100-camera multi-view, and bundle refinement but pressing corrected dense stereo did not produce a saved dense report.
- 2026-09-22 — v0.8.3 prepared as large-project dense recovery + viewer usability. Root-cause analysis found test4's global best sparse pair (cameras 54→55) lies outside the largest refined multi-view component (cameras 57→156). v0.8.3 keeps the successful sparse pair when it belongs to the refined component; otherwise it chooses the strongest connected adjacent pair for dense stereo. Persistent dense-stage progress/failure diagnostics are now exported in the version report. The point-cloud viewer is now full-screen with fixed preset buttons and optional sliders so the cloud remains visible.


## v0.8.4 — fusion candidate preflight robustness

- 2026-09-22 — v0.8.3 device test passed 6 Works / 0 Problems / 0 Untested.
- test3 remained healthy: 6/6 selected fusion pairs accepted, 41,017 fused points, ready for surface reconstruction.
- test4 recovered the former dense blocker and produced a 30,000-point corrected dense seed plus a 14,191-point fused cloud, ready for surface reconstruction.
- FOUND — two later test4 fusion candidates were selected even though their first camera lacked a completed shared-world pose after an earlier break in the rebuilt rotation chain.
- DONE — v0.8.4 preflights candidates before distributed selection.
- DONE — candidate eligibility requires both image files, both refined camera centers, and a recovered world pose for the first camera in addition to existing connected/pose checks.
- DONE — unusable transform candidates are filtered before the up-to-six distributed pairs are selected.
- DONE — defensive failures now name the exact missing prerequisite instead of the generic missing-pose-or-image reason.
- DONE — fusion reports record how many candidates were filtered by preflight and report the remaining eligible candidate count.
- DONE — v0.8.4-specific in-app test guide covers test4 preflight, test4 readiness, test3 regression, and precise diagnostics.
- TEST — Android Studio compile/install and device regression.
- NEXT — surface/triangle mesh reconstruction after v0.8.4 regression passes.


## Post-v0.8.4 UI clarity rule

- DONE — v0.8.4 fusion preflight passed device testing. test4 improved to 40,922 fused points; test3 remained 41,017 points.
- TODO — Replace historical release-number card titles as the primary navigation labels.
- TODO — Number the reconstruction pipeline cards/stages in execution order.
- TODO — Make every primary action button identify the next stage explicitly.
- TODO — Keep old milestone version labels only as secondary/history text.
- TODO — Version-test instructions must quote exact on-screen card and button labels.


## v0.9.0 — Stage 7 experimental surface mesh

- PASSED — v0.8.4 device test: 6 Works / 0 Problems / 0 Untested.
- PASSED — test3 remained 41,017 fused points.
- PASSED — test4 v0.8.4 preflight filtered 27 unusable candidates and produced 40,922 fused points.
- DONE — Version 0.9.0 / versionCode 19.
- DONE — Stable Stage 1–7 reconstruction card titles.
- DONE — Main continuation buttons explicitly identify the next stage.
- DONE — Test-guide instructions quote exact visible labels.
- DONE — Stage 7 local-neighbor triangle mesher from Stage 6 fused points.
- DONE — Robust extreme-point trim, adaptive voxel reduction, and spatial-hash neighbors.
- DONE — Long/high-aspect/degenerate triangle rejection.
- DONE — 30,000-face mobile safety cap.
- DONE — Persistent mesh result.
- DONE — Full-screen wireframe mesh viewer.
- DONE — Stage 7 report, OBJ, and triangle PLY exports.
- LOCAL VALIDATION — test4: 5,795 vertices / 30,000 triangles; test3: 2,840 vertices / 28,555 triangles.
- TEST — Android Studio compile/install and v0.9.0 device guide.
- NEXT — use the device result to improve topology, normals, smoothing/hole filling, then texture projection.

### External USB camera clarification
- TODO — Camera-source selector.
- TODO — Android USB UVC enumeration/permission.
- TODO — Direct still capture from supported UVC cameras into the active project.
- TODO — Manual focus/exposure/resolution controls where supported.


## v0.9.0 device result / v0.9.1 topology target

- PASSED — v0.9.0 guide: 7 Works / 0 Problems / 0 Untested.
- PASSED — test4 Stage 7 persisted/exported 5,795 vertices / 30,000 triangles.
- PASSED — test3 Stage 7 persisted/exported 2,840 vertices / 28,555 triangles.
- FOUND — v0.9.0 local neighbor combinations create many non-manifold edges because multiple overlapping triangles can reuse the same undirected edge.
- FOUND — test4 exported many downsampled vertices that were never referenced by an accepted face.
- TODO v0.9.1 — ordered local triangle fans.
- TODO v0.9.1 — enforce edge face-count <= 2.
- TODO v0.9.1 — compact unused vertices after cleanup.
- TODO v0.9.1 — filter tiny disconnected fragments without forcing one single component.
- TODO v0.9.1 — export topology counts in Stage 7 report.
- NEXT after cleanup validation — normals/smoothing/hole strategy, then texture projection.
