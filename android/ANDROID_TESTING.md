# Android Testing Ownership

Android testing remains independent from Windows testing.

The existing Android built-in "Test This Version" workflow, Android test reports, test-output manifests, and device-test history remain Android-owned.

Authoritative Android recovery/testing history:
- `../PROJECT_CHECKPOINT.md`
- root `TEST_RESULTS_*.md` files
- root `TEST_OUTPUT_MANIFEST_*.sha256` files
- Android version/build notes.

A Windows test must never change Android verification status, and an Android test must never verify Windows.

## v0.23.0 targeted regression

The in-app 8-step **Test This Version** guide verifies:
1. Existing Camera Calibration photo presets remain unchanged.
2. The new **12×18 tiled (4 Letter sheets)** laser preset appears alongside the four prior presets.
3. Tiled LEFT export produces a four-page US Letter PDF.
4. Tiled RIGHT export produces a four-page US Letter PDF.
5. Tiled/poster-board instructions export and document the 4.25 in vertical and 9.75 in horizontal assembly seams.
6. Existing laser presets still export.
7. v0.22 persistent laser Analyze history still appends/survives reopen/export.
8. The v0.23 version-test report exports successfully.

Physical calibration is a later validation step after this software regression passes.

## v0.24.0 targeted regression

The in-app 8-step **Test This Version** guide verifies:
1. New Large 4-Letter 38.1 mm photo preset appears without removing old presets.
2. Four-page landscape photo PDF export.
3. Selected square size drives camera-calibration geometry (38.1 mm vs existing 25.0 mm).
4. Pin-align laser preset reports original 12×18 / 35 mm geometry.
5. Four-page LEFT and RIGHT laser exports.
6. Pin-align assembly-instruction export.
7. v0.22 laser-history regression.
8. v0.24 version-test report export with photo/laser print-representation metadata.

## v0.26.0 targeted regression

The v0.26 in-app guide is intended to verify:
1. The existing ready camera-calibration profile still loads.
2. Stage 2 reports distortion-aware calibrated geometry.
3. Stages 3/4 complete and Stage 4 still improves reprojection error.
4. Stage 5/6 use calibrated distortion and the expanded dense-pair budget without corrupting project isolation.
5. Stages 8/9/10 complete with distortion-aware source-photo projection; compare texture coverage against the v0.25 test5 baseline of 34.85%.
6. Laser Analyze is re-run on v0.26 at a useful threshold (roughly 68–81 for the current real test) and records which extraction orientation was selected.
7. The marker-backed laser backdrop solve reports detected LEFT/RIGHT IDs, measured panel angle, fit residual, triangulated count, and exports preview/PLY/report.
8. The v0.26 version-test report exports successfully.

Use the same 274-photo test5 set for the photogrammetry A/B comparison. Do not take a replacement photo set before the software comparison is complete.
