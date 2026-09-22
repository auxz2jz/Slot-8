# v0.8.3 Device Test Results — 2026-09-22

## Result

**v0.8.3 passed its recovery objective.**

Version test report:
- App version: 0.8.3
- versionCode: 17
- Device: Samsung SM-S908U1
- Android 16 / SDK 36
- Test-guide result: **6 Works / 0 Problems / 0 Untested**
- Exported: 2026-09-22 02:57:45 AM local device time

## test3 regression

Project `test3` remained healthy after the v0.8.3 dense-recovery changes.

- Photos: 95
- Connected cameras: 41
- Bundle refined points: 1,002
- Bundle RMS: 10.1079 px -> 1.7285 px
- Dense pair: 30,000 points
- Dense-pair valid disparity pixels: 154,729
- Dense pairs fused: 6 / 6 selected
- Fused/downsampled points: **41,017**
- Report result: `Ready for surface reconstruction: true`

This confirms the recovery changes did not regress the previously working test3 dense/fusion path.

## test4 large-project recovery

Project `test4` contains 175 photos.

Earlier v0.8.2 state:
- feature matching, sparse reconstruction, 100-camera multi-view, and bundle refinement succeeded
- corrected dense stereo did not produce a saved report/cloud
- global sparse-best pair was cameras/photos 54->55, outside the selected refined component beginning at camera 57

v0.8.3 result:
- Feature matching: 175 photos, 437,500 keypoints, 174/174 adjacent pairs usable/strong
- Sparse: best pair 54->55, 1,096 points
- Multi-view: **100/175 connected cameras**, 99 connected pairs, 36,702 combined points
- Bundle: 6,654 tracks built, strongest 1,200 optimized, 957 refined points
- Bundle RMS: **12.3533 px -> 2.2892 px**
- v0.8.3 correctly rejected the global sparse-best pair as the dense seed because it is outside the refined component
- Connected-component dense seed selected: `IMG_1790063675891.jpg -> IMG_1790063676511.jpg`
- Dense valid disparity pixels: 143,648
- Dense points: **30,000**
- Dense fusion: 98 connected candidates; 6 distributed pairs selected
- Fusion accepted: 3 pairs
- Fusion rejected: 3 pairs
- Fused/downsampled points: **14,191**
- Report result: `Ready for surface reconstruction: true`

The original v0.8.2 blocker is therefore resolved.

## Persistent diagnostics validation

The new dense-attempt diagnostics also passed. The exported version report records:
- Dense v0.8.3 START
- 4%, 12%, 28%, 45%, 70%, 84%, and 100% progress stages
- final SUCCESS
- selected dense pair
- valid disparity count
- final dense point count

For test4 the final event recorded:
- pair `IMG_1790063675891.jpg -> IMG_1790063676511.jpg`
- disparity pixels: 143,648
- points: 30,000

## Fusion robustness issue discovered

test4 fusion accepted only 3 of the 6 distributed candidates.

Rejected pairs:
1. `IMG_1790063675260.jpg -> IMG_1790063675891.jpg`
   - rejected because fewer than 250 dense points remained inside the verified disparity band
2. `IMG_1790063738934.jpg -> IMG_1790063739607.jpg`
   - reported `Missing refined pose or image file.`
3. `IMG_1790063751667.jpg -> IMG_1790063752327.jpg`
   - reported `Missing refined pose or image file.`

The latter two pairs are **not outside the refined component**:
- cameras 136 and 137 both exist in the bundle report
- cameras 155 and 156 both exist in the bundle report
- both pairs are connected and have valid multi-view pose support

Recovered-source inspection identifies the likely cause in `OpenCvDenseFusionReconstructor.kt`:

- fusion rebuilds a separate sequential `globalR` rotation chain
- if an earlier relative-pose recovery fails during that rebuild, later cameras can lack a world `Pose`
- candidate filtering currently checks connected status, pose-inlier threshold, and presence of that pair's relative pose
- it does **not** require that the selected candidate's first camera has a completed world `Pose`
- the later pair is therefore selectable and only fails afterward at the generic `Missing refined pose or image file.` guard

### Planned maintenance fix

Before selecting the distributed six fusion pairs:
1. Require both image files.
2. Require the refined camera centers needed for the pair.
3. Require the first camera's completed world pose.
4. Prefer/replace candidates so the six attempted pairs are actually transformable into the shared world frame.
5. Split the generic failure message into explicit diagnostics (missing image vs missing refined center vs missing world pose).

This should be a small fusion-selection robustness maintenance change rather than a change to the successful v0.8.3 dense-seed recovery.

## Recommended next sequence

1. Make the fusion-candidate robustness fix as the next maintenance build.
2. Regression-test test3 and test4 fusion.
3. Then begin **surface / triangle mesh reconstruction** from the validated fused dense cloud.

## Test-output integrity

See `TEST_OUTPUT_MANIFEST_v0.8.3.sha256` for hashes of all 23 files uploaded from this v0.8.3 test run.
