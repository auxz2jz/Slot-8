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
