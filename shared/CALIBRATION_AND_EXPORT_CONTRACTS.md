# 3D Scan Studio — Shared Calibration and Export Expectations

These are platform-neutral expectations. They do not require Android and Windows to use identical internal classes or storage layouts.

## Camera calibration records

A reusable camera calibration should identify, when available:
- camera/device/lens identity;
- image width and height;
- orientation/optical mode relevant to the calibration;
- physical target geometry actually used;
- accepted/rejected calibration frames;
- RMS/reprojection quality metric;
- fx, fy, cx, cy;
- radial/tangential distortion coefficients such as k1, k2, p1, p2, k3;
- creation time;
- readiness/quality status.

Changing lens, optical zoom, or materially incompatible image geometry may require a different calibration profile.

## Laser calibration records

Future laser-plane/background calibration records should identify:
- camera calibration/profile reference;
- physical background/target geometry;
- marker/layout preset or exact dimensions;
- fitted laser-plane or equivalent per-frame plane solution;
- fit quality/error metrics;
- calibration timestamp/version.

## Common geometry/export expectations

Where practical, both platforms should understand standard interchange formats:
- PLY for point clouds and/or meshes;
- OBJ for mesh geometry;
- MTL + PNG/JPEG texture assets for textured OBJ workflows;
- ZIP packages for multi-file exports when appropriate.

Platform-specific native project storage may differ, but exported interchange data should be documented.

## Reports

Human-readable diagnostic reports may use TXT.

Structured cross-platform reports should prefer versioned JSON or JSON Lines when practical.

Readers should ignore unknown optional fields. Breaking semantic changes require a schema/version update.

## Current shared structured format

`laser_analysis_history.jsonl` schema v1 is defined in `shared/DATA_FORMATS_AND_INTERFACES.md`.
