# 3D Scan Studio — Shared Feature Catalog

## F-001 — Photogrammetry
Intent: create 3D geometry and textured output from overlapping still images.

## F-002 — Camera calibration
Intent: solve reusable camera/lens/resolution calibration profiles from known printed targets.

## F-003 — Laser-line scanning
Intent: detect a projected laser stripe and later triangulate metric 3D points using calibrated camera and laser/background geometry.

## F-004 — Laser analysis run history
Intent: preserve every laser Analyze attempt instead of overwriting the prior diagnostic result.
Shared behavior:
- each run has a stable run ID and timestamp;
- threshold/color and relevant input/calibration settings are captured;
- success/failure and measured output metrics are captured;
- repeated threshold experiments append;
- history can be exported in a platform-neutral format.
Origin: Android hardware testing.
Platform status: Android CANDIDATE v0.22.0; Windows NOT STARTED.

## F-005 — Structured-light scanning
Intent: project known coded patterns, capture them with a calibrated camera, decode correspondences, and reconstruct 3D geometry.

## F-006 — Cross-platform project interchange
Intent: Android and Windows can exchange or consume the same project-level scan data and platform-neutral reports without requiring identical implementation code.

Platform status is tracked separately.

## F-007 — Pin-align multi-sheet calibration
Intent: allow accurate calibration targets to be assembled from ordinary printer sheets while preserving or explicitly recording physical target geometry.

Shared behavior:
- matching registration marks support precise multi-sheet alignment;
- registration marks are sacrificial or outside the active calibration field;
- internal overlaps may be straight-cut and butted so no sheet covers active calibration artwork;
- a tiled print representation must not silently change physical calibration geometry;
- if a tiled target intentionally uses different physical geometry, the square/marker dimensions and preset identity must be recorded and used by calibration math.

Origin: Android budget poster-board calibration workflow.
Platform status: Android CANDIDATE v0.24.0; Windows NOT STARTED.

## F-008 — Distortion-aware calibrated reconstruction
Intent: use a measured camera model consistently in geometric reconstruction without destructively rewriting original source photographs.

Shared behavior:
- original image pixels may remain authoritative for feature description and texture sampling;
- geometric feature coordinates can be undistorted before epipolar/pose calculations;
- dense stereo should undistort/rectify with measured intrinsics/distortion when compatible calibration exists;
- source-photo projection should account for the source raster's lens distortion;
- reports must state when calibrated distortion is active, suppressed, or unavailable.

Origin: Android v0.26.
Platform status: Android CANDIDATE v0.26.0; Windows NOT STARTED.

## F-009 — Marker-backed laser plane and single-frame triangulation
Intent: recover the instantaneous plane of a hand-swept line laser from a known LEFT/RIGHT calibration backdrop and triangulate metric 3D stripe points.

Shared behavior:
- identify the selected target's marker IDs and physical marker layout;
- solve physical backdrop plane(s) with a calibrated camera;
- report measured panel geometry/angle rather than silently assuming the backdrop is perfect;
- intersect calibrated camera rays with known backdrop planes to obtain metric laser-plane samples;
- fit and quality-score the instantaneous laser plane;
- triangulate the current frame's stripe and export metric point-cloud data;
- multi-frame accumulation is a later layer, not silently implied by a single-frame result.

Origin: Android v0.26.
Platform status: Android CANDIDATE v0.26.0; Windows NOT STARTED.
