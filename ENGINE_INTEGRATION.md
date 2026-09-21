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
