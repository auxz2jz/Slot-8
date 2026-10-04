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

## F-005 — Structured-light scanning
Intent: project known coded patterns, capture them with a calibrated camera, decode correspondences, and reconstruct 3D geometry.

## F-006 — Cross-platform project interchange
Intent: Android and Windows can exchange or consume the same project-level scan data and platform-neutral reports without requiring identical implementation code.

Platform status is tracked separately.
