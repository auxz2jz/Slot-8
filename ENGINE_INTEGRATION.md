# Engine Integration Plan

## Principle

The Android app should use a common reconstruction/processing interface so the UI is not tied directly to one engine.

Desktop Windows executables cannot simply be copied into Android. Suitable C/C++ libraries/algorithms must be compiled for Android (typically with the NDK) or adapted behind native interfaces.

## Existing source set

Photogrammetry:
- COLMAP
- OpenMVG
- OpenMVS
- AliceVision
- Meshroom
- ODM / OpenDroneMap
- MicMac

Mesh/post-processing:
- Manifold
- Assimp
- trimesh
- meshoptimizer
- MeshLab

All are available as source repositories in the user's GitHub account.

## Likely Android strategy

### Reuse directly where practical
- C/C++ libraries that can be compiled with Android NDK.
- Camera calibration / geometry algorithms.
- Feature extraction/matching components that build cleanly on Android.
- Mesh import/export and optimization libraries that have manageable dependencies.

### Adapt/wrap
- Engine stages that are useful but designed around desktop command-line applications.
- Expose only stable library/CLI-equivalent functions through a native adapter.
- Keep engine-specific parameters under an Advanced section.

### Keep desktop-only when necessary
Some heavy CUDA/desktop workflows may remain impractical on Android. These can stay candidates for a future Windows companion while Android remains fully capable of capture and the on-device stages we can support.

## Common processing stages

Photogrammetry pipeline abstraction:
1. Validate image set.
2. Feature detection.
3. Feature matching.
4. Camera pose / structure-from-motion.
5. Dense reconstruction / point cloud.
6. Surface/mesh reconstruction.
7. Mesh cleanup/optimization.
8. Texture generation.
9. Export.

Laser pipeline abstraction:
1. Camera calibration.
2. Laser-plane calibration.
3. Frame acquisition.
4. Laser stripe detection.
5. Stripe center/subpixel estimation.
6. Ray/plane triangulation.
7. Transform into scanner/turntable coordinates.
8. Accumulate point cloud.
9. Register multiple passes.
10. Mesh reconstruction.
11. Cleanup/export.

Hybrid pipeline:
1. Run photo and laser pipelines.
2. Establish common coordinate frame.
3. Register/align datasets.
4. Fuse geometry.
5. Clean/optimize mesh.
6. Apply photographic texture.
7. Export.

## Runtime rule

GitHub is never a runtime dependency. Any required native library, configuration file, calibration asset, or model/resource needed for scanning must be packaged locally in the app or stored locally as app data.


## OpenCV Android milestone (v0.3)

The Android prototype now begins real local computer-vision processing with the official OpenCV Android Maven artifact `org.opencv:opencv:4.14.0`.

Implemented stage:
1. Decode reduced in-memory working images while preserving originals.
2. Detect ORB keypoints/descriptors.
3. Match adjacent photos with Hamming k-NN matching.
4. Apply ratio filtering.
5. Report good-match counts and overlap quality.

This dependency is obtained during the Android build and bundled in the APK; the installed app does not require GitHub or cloud access for feature matching.

Next photogrammetry stage after v0.3 passes:
- geometric verification/RANSAC,
- camera-pose estimation,
- triangulation,
- initial sparse point cloud,
- then multi-view expansion/bundle adjustment.


## v0.4.0 sparse geometry milestone

The Android app now advances beyond descriptor matching into real projective geometry:

1. Recompute ORB features/correspondences for adjacent image pairs.
2. Estimate an essential matrix with RANSAC.
3. Recover the relative camera rotation/translation direction.
4. Score adjacent pairs by recovered-pose inliers.
5. Select the best verified pair.
6. Triangulate a first sparse 3D point cloud.
7. Persist the result locally and export it as ASCII PLY.

Camera intrinsics are estimated from 35mm-equivalent EXIF focal metadata when available; otherwise v0.4 uses a documented focal-length fallback. Translation scale remains arbitrary in this two-view stage.

Next engine milestone after device validation:
- build multi-image feature tracks,
- expand camera poses across more views,
- triangulate shared tracks,
- run bundle adjustment,
- only then proceed toward dense reconstruction.


## v0.5.1 weak-link recovery

The first v0.5 multi-view implementation was intentionally simple but too brittle: it started at the first photo and aborted the entire sequential chain when any adjacent pair fell below the pose-inlier threshold.

v0.5.1 changes the pre-bundle-adjustment strategy:

1. Compute geometric verification and relative pose for every adjacent pair.
2. Keep diagnostics for all pairs, even after a weak link.
3. Find the largest contiguous run of usable adjacent-pair poses.
4. Use the first camera of that run as the local origin.
5. Chain poses and triangulated points only inside that largest connected component.
6. Keep cameras outside the component explicitly marked disconnected instead of silently merging unrelated coordinate frames.
7. Export all pair diagnostics so bridge-photo or future graph-matching decisions are data-driven.

This is still not bundle adjustment and still uses unit-length relative translations, but it provides a valid connected component for the next optimization stage instead of discarding later strong geometry.


## v0.6.0 shared-track bundle refinement

v0.5.1 proved a stable 14-camera connected component, but its point cloud is a merge of independently triangulated adjacent-pair points. Bundle adjustment needs shared observations of the same physical feature across multiple views.

v0.6 therefore:
1. Recomputes ORB correspondences inside the connected component.
2. Builds explicit feature tracks observed in at least 3 cameras.
3. Triangulates one shared 3D point per usable track.
4. Alternates robust reprojection-error optimization of shared 3D points and camera translations.
5. Keeps camera rotations/intrinsics fixed for this first on-device optimization milestone.
6. Fixes the first two cameras to preserve gauge/scale.
7. Reports before/after RMS reprojection error and filters high-error tracks.
8. Exports the refined sparse PLY and a detailed refinement report.

This is a limited but genuine reprojection-error bundle-refinement stage. Later versions can refine rotations/intrinsics and add graph/loop-closure constraints before dense reconstruction.


## v0.6.1 diagnostics and report identity

The v0.6 shared-track refinement test succeeded numerically: 471 tracks were built/optimized, 1,276 observations survived filtering, 389 refined sparse points remained, and RMS reprojection error fell from 9.1838 px to 2.9032 px. The user's test-step Problem flag reflected uncertainty about interpreting the metric, not an optimizer regression.

v0.6.1 therefore leaves the geometry engine unchanged and improves observability:
- report the RMS change in plain language and percentage terms,
- include build/device information in the version test report,
- embed a pipeline diagnostic snapshot in the version test report,
- tag exported reports with project ID and stable fingerprints,
- add matching identity comments to exported PLY files,
- make the test guide accessible from all major screens.

This establishes a stronger test/debug contract before dense reconstruction work begins.


## v0.7.0 first dense stereo pair

v0.6/v0.6.1 established a refined sparse component and shared multi-view tracks. v0.7 deliberately validates dense stereo on one already verified pair before attempting full multi-pair fusion.

Pipeline:
1. Select the saved best sparse pair.
2. Recompute ORB correspondences and essential-matrix/RANSAC geometry.
3. Recover the relative two-camera pose.
4. Stereo-rectify the two working images.
5. Compute disparity with OpenCV StereoSGBM.
6. Convert fixed-point disparity to float and reproject it with the stereo Q matrix.
7. Keep finite positive-depth points, trim extreme depth outliers, and cap the export to a mobile-friendly point count.
8. Persist a dense-pair report and point cloud; export fingerprinted text/PLY results.

The pair translation magnitude remains arbitrary, so the dense cloud is not metric yet. This first milestone also assumes zero lens distortion during rectification. Camera calibration, full intrinsic/rotation refinement, and multi-pair dense fusion come later.

The v0.7 version-test report now includes current ReconstructionStatus (state/progress/message) so failures that occur before a stage report is created are still captured automatically.


## v0.7.1 adaptive dense correction

The first dense-pair run passed functionally but exposed a stretched/cone-like preview. The v0.7 report used a generic disparity interval; that interval can miss the disparity band occupied by the verified target and favor farther background structure.

v0.7.1 stays on the same dense-stereo path and makes the search data-driven:
1. Rectify the already verified pose-inlier feature coordinates with R1/P1 and R2/P2.
2. Measure robust 5th/50th/95th percentile rectified disparities.
3. Pad and quantize that band into an SGBM-compatible minDisparity/numDisparities interval.
4. Reproject dense disparity as before.
5. Compare dense point radius against the verified sparse pair's broad radius distribution and use it as an optional object-depth prior when enough samples survive.
6. Use robust 95th-percentile preview scaling instead of the single most extreme point.

This is a correction to the one-pair dense milestone, not a new branch. After validation, continue with multi-pair dense fusion.


## v0.8.0 multi-pair dense fusion

v0.7/v0.7.1 proved the dense stereo stage on one verified pair. v0.8 combines several connected viewpoints while protecting the shared cloud from weak pair geometry.

Pipeline:
1. Read the largest connected multi-view component and v0.6 refined camera centers.
2. Recompute adjacent relative rotations across that component.
3. Select a distributed subset of verified connected pairs for the first mobile fusion pass.
4. Run adaptive StereoSGBM per selected pair.
5. Reject extremely low-parallax pairs and pair clouds with too few verified-band dense samples.
6. Undo left-image rectification, scale local pair geometry to the refined adjacent baseline, and transform it into the shared world coordinate frame.
7. Robustly trim global outliers, voxel-downsample, and cap the exported fused point cloud.
8. Persist/export pair-level FUSED/SKIPPED diagnostics and the fused PLY.

This remains arbitrary-scale and still assumes zero lens distortion. The next major stage after validation is surface/mesh reconstruction, while calibration and broader dense-pair coverage remain follow-up quality improvements.
