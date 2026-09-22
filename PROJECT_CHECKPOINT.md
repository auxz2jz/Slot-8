# 3D Scan Studio — Recovery Checkpoint

## Current baseline

- App version: **v0.8.3**
- Android versionCode: **17**
- Recovered source archive: `PhotogrammetryStudioAndroid-v0.8.3-dense-recovery-fullscreen-viewer.zip`
- Archive SHA-256: `0dd2ec35195a214018c3c1695dd35e7a6bad7089a4d7b3f06fe8064c9d42e47f`
- Extracted source snapshot: **41 files / 393,404 bytes**
- Canonical repository: `auxz2jz/Slot-8`, branch `main`

## Last confirmed device state before v0.8.3

The v0.8.2 device test recorded **8 Works / 1 Problem**. Rapid Hold was fixed, mixed EXIF orientations were normalized successfully, and the 3-axis point-cloud viewer worked but was too crowded. `test3` rebuilt successfully and produced a healthy fused dense cloud.

For `test4`:
- 175 photos
- 174/174 adjacent pairs usable/strong
- sparse reconstruction: 1,096 points
- largest connected component: 100 cameras / 36,702 points
- bundle RMS: 12.3533 px -> 2.2892 px
- failure: corrected dense stereo produced no saved dense report
- root-cause clue: global best sparse pair was cameras/photos 54->55, while the selected refined connected component began at camera 57

## v0.8.3 recovery build

v0.8.3 is the current source baseline and is **awaiting device validation**. It adds:

1. Dense-seed selection from verified pairs inside the selected connected component.
2. Automatic fallback across up to six verified candidate pairs.
3. No reuse of sparse-radius guidance when the fallback pair belongs to a different baseline.
4. Persistent dense-attempt start/progress/success/failure diagnostics even when no dense report is created.
5. Dense progress/failure feedback in the project UI.
6. Full-screen point-cloud viewer with Front/Back/Left/Right/Top/Bottom presets, yaw/pitch/roll gestures, pinch zoom, and optional sliders.
7. A v0.8.3 test guide focused on `test4` dense recovery and fusion.

## Next milestone

1. Build/install v0.8.3 in Android Studio.
2. Run the v0.8.3 in-app test guide.
3. Re-run `test4` through corrected dense stereo and multi-pair fusion.
4. Export the version-test diagnostics and dense-attempt log.
5. If recovery passes, proceed to **surface/triangle mesh reconstruction**.

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
