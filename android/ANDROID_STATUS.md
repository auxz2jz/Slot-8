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
