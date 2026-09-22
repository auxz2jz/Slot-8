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


## v0.8.1 capture/orientation integration

v0.8.1 keeps the established reconstruction sequence and adds a capture-quality layer plus one fusion-gating correction.

Capture:
- Single still.
- Rapid Hold: capture while the shutter is held.
- Rapid Start/Stop: tap once to run automatically and tap again to stop.
- Target intervals from 5 stills/sec through one still every 5 seconds.
- Optional 3-second delayed start for mounted/rotating-camera use.
- Full-resolution CameraX stills are serialized: each save callback completes before the next request, so unsupported requested rates fall back to the fastest safe device rate instead of creating a request backlog.
- Project refresh/analyze runs once at the end of a Rapid sequence.

Orientation:
- All OpenCV stages now decode through one EXIF-aware bitmap loader.
- EXIF rotation and mirror values are normalized before ORB matching, sparse/multi-view geometry, bundle refinement, dense stereo, and dense fusion.
- Project thumbnails use the same normalized loader.
- The exported version test report audits the EXIF orientation value of every selected-project photo.

Fusion gate:
- A non-empty corrected dense seed is sufficient to expose/run multi-pair fusion.
- The fusion engine already performs independent pair-level quality rejection, so the old 1,000-point single-pair readiness threshold must not block the entire multi-pair stage.
- This specifically allows the 713-point test3 seed to continue into v0.8 fusion evaluation.

Tooling recovery:
The interrupted v0.8.1 session stalled during a tooling check because this runtime has Java and a standalone Kotlin compiler but no system Gradle and no gradle-wrapper.jar in the project. The wrapper bootstraps that JAR from raw.githubusercontent.com, which this runtime cannot resolve. Wrapper bootstrap commands now use short timeouts so that limitation fails immediately instead of looking hung.


## v0.8.2 capture/viewer stabilization

Two independent v0.8.1 projects reached successful multi-pair dense fusion, so v0.8.2 intentionally leaves the reconstruction math unchanged and fixes capture/viewer/testability issues before surface reconstruction.

1. Hold Rapid: the v0.8.1 shutter pointerInput was keyed on rapidActive. Starting Rapid changed that key, cancelling the active press coroutine and immediately running the release/finally path. v0.8.2 removes rapidActive from the pointer-input restart keys and reads the current Rapid state without restarting the gesture.
2. Rapid throughput: each run records target interval, elapsed time, saved count, actual photos/sec, average full-resolution ImageCapture save latency, and stop reason. A 5/sec request remains a target; the serialized full-resolution pipeline may sustain less.
3. Rotation/configuration: app screen/test-guide state and camera capture settings are saveable so normal Android activity recreation no longer returns the user to the project list merely for rotating the phone.
4. Viewer: the common point-cloud preview now applies yaw around Y, pitch around X, then roll around Z before perspective projection. Touch drag controls yaw/pitch, pinch controls zoom, two-finger twist controls roll, and explicit sliders/preset orthogonal views provide deterministic inspection.
5. Test notes: Works steps can carry optional saved/exported tester notes, and version reports include recent Rapid diagnostics.

Next geometry milestone: create a surface/triangle mesh from a validated fused dense cloud.


## v0.8.3 dense-seed recovery

The 175-photo test4 run proved that project size alone did not stop the pipeline: feature matching, sparse reconstruction, the 100-camera main component, and bundle refinement all completed. The failure occurred specifically when entering the one-pair dense stage.

The previous dense seed always reused the globally best sparse pair. On test4 that pair is photos 54-55, while the selected/refined multi-view component begins at camera 57. v0.8.3 aligns dense-seed selection with the component used by later fusion:

1. Rank verified connected multi-view pairs by recovered-pose support and triangulated support.
2. Prefer the global sparse pair only when it is itself inside the connected component.
3. Attempt up to six verified candidates automatically.
4. Keep pair-specific sparse-depth guidance only for the original sparse pair; fallback pairs use their own dense filtering rather than an unrelated depth prior.
5. Persist a dense-attempt log independently from the dense report, so a failed run remains diagnosable after navigation/reopen.
6. Show candidate/stage progress beside the dense button and include the attempt log in exported version-test diagnostics.

The StereoSGBM and multi-pair fusion math remain otherwise unchanged in this maintenance build.


## v0.8.3 connected-component dense recovery

The v0.8.2 test4 project contained 175 photos and successfully completed feature matching, two-view sparse reconstruction, a 100-camera largest connected component, and limited bundle refinement. Its global best sparse pair was immediately before the chosen largest connected component, so the one-pair dense stage was not anchored to geometry included in the refined camera solution.

v0.8.3 changes dense-seed selection:
1. If the saved best sparse pair belongs to the largest connected multi-view component, keep it unchanged.
2. Otherwise choose the connected adjacent pair with the strongest recovered-pose support (triangulated-point count as tie-breaker).
3. Skip the old sparse-radius guidance when a different pair is selected, because that saved sparse cloud belongs to another baseline.
4. Preserve adaptive rectified-disparity selection and all previous dense filtering.
5. Persist start/progress/success/failure events for the dense attempt so a future no-output event has an exact stage and exception in the exported test report.

This is a recovery/stability change; multi-pair fusion mathematics is unchanged.


## v0.8.4 fusion candidate preflight

v0.8.3 fixed the 175-photo test4 dense-seed failure and produced a valid 30,000-point connected-component dense seed plus a 14,191-point fused cloud. Review of the pair diagnostics found a smaller fusion-selection issue: two selected later pairs were inside the refined component but failed because their first camera had no shared-world rotation after an earlier break in the fusion engine's separately rebuilt rotation chain.

v0.8.4 adds preflight before distributed pair selection:
1. Keep the existing connected-pair, pose-inlier, and local relative-pose checks.
2. Require both source image files.
3. Require both refined camera centers from bundle refinement.
4. Require a completed shared-world pose for the pair's first camera.
5. Select up to six distributed pairs only from this eligible set.
6. Record how many geometric candidates were filtered before selection.
7. Use exact defensive diagnostics for missing camera index, first/second image, refined center, or recovered world pose.

The dense-stereo math, pair reconstruction math, world transform, robust global trimming, voxel downsampling, and readiness thresholds remain unchanged. This is a candidate-selection/diagnostic maintenance release before surface meshing.


## v0.9.0 Stage 7 local-neighbor surface milestone

The validated Stage 6 fused dense cloud is now input to the first on-device surface stage. This is deliberately a conservative mobile validation algorithm rather than a claim of final Poisson/ball-pivoting quality.

Pipeline:
1. Reject non-finite fused points.
2. Use robust 1st/99th percentile coordinate bounds and trim isolated extremes with padding.
3. Voxel-average the cloud, increasing voxel size only as needed to stay mobile-friendly.
4. Build a 3D spatial hash for local-neighbor queries.
5. Estimate local point spacing from nearest neighbors.
6. Form triangle candidates only when all three edges satisfy adaptive local/global limits.
7. Reject highly stretched and nearly degenerate triangles.
8. Orient accepted faces approximately outward relative to the cloud centroid.
9. Cap the first mobile mesh at 30,000 faces.
10. Persist and export OBJ plus triangle PLY.

The first viewer is wireframe so connectivity can be inspected without requiring normals/materials. Later mesh work can add stronger surface reconstruction, normal estimation/orientation, smoothing, hole filling, component filtering, and texture projection.
