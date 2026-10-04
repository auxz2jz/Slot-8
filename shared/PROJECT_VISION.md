# 3D Scan Studio — Shared Product Vision

3D Scan Studio is one cross-platform 3D scanning product with separate Android and Windows implementations.

Shared product capabilities:
- photogrammetry from still images;
- laser-line scanning;
- planned structured-light scanning;
- hybrid workflows that can combine compatible scan sources;
- project-based storage of source media, calibration data, diagnostic reports, point clouds, meshes, textures, and exports.

Platform intent:
- Android: capture-first, portable scanning, on-device processing where practical.
- Windows: future PC implementation for heavier processing, larger datasets, optional external cameras/projectors/controllers, and project interchange with Android.

Android and Windows do not need identical UI or implementation code. They should preserve common feature meaning and interoperable project/data formats where practical.
