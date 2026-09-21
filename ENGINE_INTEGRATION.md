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
