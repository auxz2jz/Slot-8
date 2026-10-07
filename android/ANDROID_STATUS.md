# 3D Scan Studio — Android Status

Owner: Android implementation

Last physically confirmed laser-capable baseline:
- v0.19.0-r1 compiled successfully in the user's Android Studio.
- Real laser hardware testing is underway.

Latest packaged candidate before v0.22 work:
- v0.21.0 / versionCode 33
- Standard print-size calibration presets
- Status: CANDIDATE, not yet fully device-verified.

Current Android task:
- v0.22 laser Analyze run-history diagnostics.
- Preserve every threshold experiment.
- Do not change laser extraction math in this diagnostic build.

Windows verification never changes Android verification status.


## Current candidate — v0.22.0

- versionCode 34
- Laser Analyze history append/persistence/export implemented.
- Shared JSONL schema v1 implemented on Android.
- Artifact SHA-256: `2c6d3468f3d35e813de6b71290b86a49754344a71f13cc7956cee9d06d743a85`
- Status: CANDIDATE pending user Android Studio/device test.
- Laser extraction algorithm unchanged.

## Current candidate — v0.23.0

- versionCode 35.
- Preserves v0.22 laser Analyze run history and extraction math.
- Adds **12×18 tiled (4 Letter sheets)** laser calibration preset alongside Letter, Legal, Ledger and single-sheet 12×18.
- LEFT and RIGHT each export as 4 US Letter pages.
- Photo calibration remains the existing one-sheet Letter 9×6-inner-corner / 25 mm-square / 250×175 mm checkerboard.
- Android Studio package SHA-256: `35770aa5eed29e3f46fcb7e9fba982d3a0cc249f6f55ce9d4ddc6924f9270d13`.
- Status: **CANDIDATE** pending user Android Studio/device test.
- Last physically confirmed laser-capable baseline remains v0.19.0-r1 until the user verifies a later candidate.

## Current candidate — v0.24.0

- versionCode 36.
- Preserves v0.22 laser extraction/history behavior.
- Adds **Large 4-Letter (38.1 mm)** camera calibration preset.
- Upgrades the tiled laser preset to sacrificial pin-align crosshairs + straight-cut seams while preserving the same preset ID and 12×18 / 35 mm geometry.
- Existing single-sheet presets remain.
- Android Studio package SHA-256: `3b881940d1731abb2d1c4f34bee479455e23b082caf9472ef4a82abf18ab7b9e`.
- Status: **CANDIDATE** pending user Android Studio/device test.
- Last physically confirmed laser-capable baseline remains v0.19.0-r1 until the user verifies a later candidate.

## Current candidate — v0.26.0

- versionCode 38.
- Built from preserved v0.25.0 clean-single-sheet candidate/baseline.
- Photogrammetry geometry is now distortion-aware from Stage 2 onward while Stage 1 descriptors remain on original pixels.
- Dense fusion candidate budget increased to 12 and fused point cap to 80k.
- Laser line extraction is dual-orientation.
- Laser backdrop markers now support physical panel pose/angle solve, instantaneous laser-plane recovery and metric single-frame PLY output.
- Package SHA-256: `8a9155a10424dc6c255141c8a46ead78b826ba3bb566e639e33be9f5d4b5ea7e`.
- Status: **CANDIDATE** pending Android Studio/device test.
- v0.25 remains the immediate rollback package.
