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


## v0.9.0 — Stage 7 surface mesh

v0.8.4 passed device testing: test4 improved to 40,922 fused points while test3 stayed at 41,017. v0.9.0 is the first surface/triangle-mesh milestone.

The reconstruction UI now uses stable Stage 1–7 names, and continuation buttons explicitly name the next stage. Stage 7 performs robust outlier trimming, adaptive voxel reduction, spatial-hash neighborhood lookup, and conservative local triangle generation. It persists the mesh, provides a full-screen wireframe viewer, and exports a report, OBJ, and triangle PLY.

Current test package: `PhotogrammetryStudioAndroid-v0.9.0-Stage7-Surface-Mesh-Android-Studio-Ready.zip`.
SHA-256: `9d70424152884746fe693d49bcdde286e8230f9a7f75f597e88f521edccbe174`.

See `PROJECT_CHECKPOINT.md` for the exact device-test sequence.


## v0.9.1 topology-cleanup test build

v0.9.0 passed all seven device-test steps and proved Stage 7 triangle generation, persistence, viewing, OBJ export, and triangle PLY export. Inspection of the exported topology found many overlapping non-manifold edges, so v0.9.1 cleans the mesh before later smoothing/hole/texture work.

v0.9.1 uses ordered local triangle fans, limits every undirected edge to at most two incident faces, filters tiny disconnected fragments, compacts unused vertices, and exposes topology statistics in the Stage 7 card/report.

Android Studio package:
`PhotogrammetryStudioAndroid-v0.9.1-Stage7-Topology-Cleanup-Android-Studio-Ready.zip`

SHA-256:
`a52e4fbdf42f6ef022f609a462c0d595ee686bb414474b1a5e5fc72008d9df83`

Pure-Kotlin validation on the real uploaded fused clouds reached zero non-manifold edges for both test4 and test3. See `PROJECT_CHECKPOINT.md` for the exact device-test sequence.


## v0.9.1 complete / v0.9.2 maintenance build

The complete v0.9.1 device set passed: 7 Works / 0 Problems / 0 Untested. test4 completed all stages from 175-photo feature matching through a topology-clean Stage 7 mesh with 12,872 faces and 0 non-manifold edges. test3 remained topology-clean with 4,938 faces and 0 non-manifold edges.

A rerun exposed a UI-state bug: persisted downstream files were correctly invalidated, but an already-loaded Stage 7 card could remain visible until a later stage cleared it from memory.

v0.9.2 fixes state consistency and adds **Reset reconstruction stages** while leaving all reconstruction/mesh math unchanged.

Current Android Studio package:
`PhotogrammetryStudioAndroid-v0.9.2-Pipeline-Reset-State-Fix-Android-Studio-Ready.zip`

SHA-256:
`5f6b078a1b960c7220f36c1854f65cd03a3f9797a648706efc01c5c94454abdc`

See `PROJECT_CHECKPOINT.md` for exact v0.9.2 test instructions.


## v0.10.0 — project isolation + Stage 7 surface quality

v0.9.2 passed its reset/state test. A follow-up navigation test exposed that the on-disk project data was isolated correctly but live ViewModel progress could visually bleed into another selected project.

v0.10.0 makes every heavy Stage 1–7 job project-owned. Background progress is shown only in the owner project, completion cannot change the user's selected project, and one heavy reconstruction job is allowed at a time until explicit multi-job scheduling is designed.

The same build advances Stage 7 with boundary-preserving smoothing, per-vertex normals, OBJ/PLY normal export, and classification of remaining borders into simple closed loops versus open/branched boundary groups. Broad automatic hole filling is intentionally deferred until these diagnostics are validated on-device.

Current package:
`PhotogrammetryStudioAndroid-v0.10.0-Project-Isolation-Surface-Quality-Android-Studio-Ready.zip`

SHA-256:
`f9bca4a4096189c020c1c640b511fd9da0684ccd18507f3948b2224252687ba7`


## v0.11.0 — combined diagnostics + conservative hole fill

v0.10.0 passed 8 Works / 0 Problems / 0 Untested. v0.11.0 reduces repetitive uploads with **Export combined project diagnostics**, one text export containing Quick Check plus every available Stage 1–7 detailed diagnostic section for that project.

Stage 7 now attempts conservative filling of only simple closed boundary loops. Open or branched borders are never auto-filled. On the real validation clouds, test4's small loop is safely capped (+1 vertex/+4 faces) while test3's less suitable loop is deliberately skipped; both remain at zero non-manifold edges.

Current package SHA-256: `392f033753fa059a2b64d1d57a5cdbce8e9901427abab77911679bb0cd2f2ba9`.

See `PROJECT_CHECKPOINT.md` for the exact device test.


## v0.12.0 — Stage 8 photo color projection

v0.11.0 passed 7 Works / 0 Problems / 0 Untested and confirmed the one-file Combined Project Diagnostics workflow.

v0.12.0 adds the first photographic appearance stage. Stage 8 rebuilds the connected camera orientation chain, reuses Stage 4 refined camera centers, projects Stage 7 vertices into EXIF-normalized source photos, scores usable views, and blends photo color onto mesh vertices. It adds a colored preview, persistent color diagnostics, colored PLY/OBJ exports, and automatically extends Combined Project Diagnostics through Stage 8.

Stage 8 does not change Stage 7 topology. This is intentionally a vertex-color validation milestone before triangle visibility refinement, UV coordinates and a real texture atlas.

Current package:
`PhotogrammetryStudioAndroid-v0.12.0-Stage8-Photo-Color-Projection-Android-Studio-Ready.zip`

SHA-256:
`a43dc5619dc90a82a5fb578e00fd45c26cb9a3dfd6c0aa19d0e2e584b377e630`
