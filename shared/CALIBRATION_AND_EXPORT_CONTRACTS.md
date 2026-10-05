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

## Tiled calibration print representations

A calibration target may be distributed across multiple printer sheets without becoming a new physical calibration geometry.

Requirements:
- The assembled target must preserve the exact intended physical dimensions, marker/checker geometry and orientation.
- Tiling/crop/registration marks used only for assembly should remain outside the calibration field or be removed during trimming so they do not become unintended machine-visible features.
- The selected calibration record should identify the physical target geometry independently from its print-sheet representation when practical.

Current Android v0.23 example:
- laser: the existing 12×18 in / 35.0 mm-marker LEFT and RIGHT geometry can be exported as four US Letter pages per panel;
- photo/camera: the 9×6-inner-corner / 25.0 mm-square / 250×175 mm checkerboard remains one Letter sheet because its complete physical geometry already fits at 1:1 scale.

## Large tiled camera-calibration geometry

Tiling may be either a print representation of an unchanged target or a distinct larger physical calibration preset. These cases must not be conflated.

Current Android v0.24 examples:
- laser: four Letter sheets are only a print representation; assembled geometry remains the existing 12×18 in / 35.0 mm-marker target;
- camera/photo: the optional Large 4-Letter target is a **new physical calibration preset** with 9×6 inner corners and 38.1 mm squares. Calibration object points must use 38.1 mm when that board is selected.

A saved camera-calibration profile must continue recording `targetPresetId` and `squareSizeMm` so profiles solved from 25.0 mm and 38.1 mm boards remain distinguishable.
