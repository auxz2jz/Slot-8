# 3D Scan Studio — Recovery Checkpoint

## Current baseline

- App version: **v0.8.3**
- Android versionCode: **17**
- Recovered source archive: `PhotogrammetryStudioAndroid-v0.8.3-dense-recovery-fullscreen-viewer.zip`
- Archive SHA-256: `0dd2ec35195a214018c3c1695dd35e7a6bad7089a4d7b3f06fe8064c9d42e47f`
- Extracted source snapshot: **41 files / 393,404 bytes**
- Canonical repository: `auxz2jz/Slot-8`, branch `main`
- Device-test status: **PASSED recovery objective**
- v0.8.3 guide: **6 Works / 0 Problems / 0 Untested**

## v0.8.3 confirmed results

### test3 regression

- 95 photos
- 41 connected cameras
- Bundle RMS: 10.1079 px -> 1.7285 px
- Dense pair: 30,000 points
- Fusion: 6/6 selected pairs fused
- Fused cloud: **41,017 points**
- readyForSurfaceReconstruction=true

### test4 large-project recovery

- 175 photos
- 174/174 adjacent pairs usable/strong
- sparse reconstruction: 1,096 points
- largest connected component: 100 cameras / 36,702 points
- bundle RMS: 12.3533 px -> 2.2892 px
- v0.8.3 correctly selected a dense seed inside the refined connected component instead of the global sparse-best pair outside it
- corrected dense pair: **30,000 points**
- dense valid disparity pixels: 143,648
- fusion: 3 of 6 distributed pairs accepted
- fused cloud: **14,191 points**
- readyForSurfaceReconstruction=true

The v0.8.2 no-dense-output blocker is fixed.

See `TEST_RESULTS_v0.8.3.md` for full analysis and `TEST_OUTPUT_MANIFEST_v0.8.3.sha256` for the exact uploaded-output hashes.

## Remaining issue

test4 fusion rejected 3 of its 6 selected distributed pairs. One was a legitimate low-dense-support rejection. Two were reported as `Missing refined pose or image file.`

Those two pairs are present inside the refined 100-camera component. Source inspection shows the fusion candidate selector can choose a pair whose own relative pose exists while its first camera lacks a completed world pose because an earlier link failed during the separately rebuilt rotation chain.

### Next maintenance target

- Filter fusion candidates for actual world-transform availability before distributed selection.
- Require image files + refined centers + completed first-camera world pose.
- Replace the generic missing-file/pose message with exact reason diagnostics.
- Retest test3/test4 fusion.

After that maintenance pass, proceed to **surface/triangle mesh reconstruction**.

## GitHub checkpoint rule

For this project, GitHub should be updated throughout development rather than only at the end of a long chat. At minimum create/update a checkpoint:

- after each meaningful code milestone or version bump,
- before beginning a long device-test cycle,
- after device-test results are received,
- immediately after a bug/root cause is identified,
- before producing a replacement ZIP/build, and
- whenever a chat is approaching a context/size limit.

Each checkpoint should state the current version, what changed, what is tested, what is still failing, and the exact next step. Source-code commits should accompany documentation updates whenever the connector/workflow permits.

## Source integrity

See `SOURCE_MANIFEST_v0.8.3.sha256` for SHA-256 hashes of every file in the recovered source snapshot.
