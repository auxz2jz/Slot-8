# Android Feature Roadmap

Status legend:
- **DONE** — implemented in the current prototype.
- **PARTIAL** — scaffolding/interface exists but feature is incomplete.
- **TODO** — planned.

## Foundation

- DONE — Kotlin Android project.
- DONE — Jetpack Compose UI foundation.
- DONE — Local scan/project creation.
- DONE — CameraX photo capture.
- DONE — Import multiple existing photos.
- DONE — Local project/photo storage.
- DONE — Photo thumbnail gallery.
- DONE — Delete unwanted photos.
- DONE — Basic photo-set analysis.
- DONE — Warnings for too few photos, changing resolution, and focal-length/zoom changes.
- DONE — Basic capture guidance.
- PARTIAL — Reconstruction-engine interface.
- TODO — Actual on-device 3D reconstruction.
- TODO — Move the tested Android source into this repository.

## Main scan modes

- TODO — Home screen with Photo Scan / Laser Scan / Hybrid Scan.
- TODO — Shared scan-project format that can hold all three data types.
- TODO — Advanced/manual controls.
- TODO — Simple automatic workflow.

## Photogrammetry

- TODO — Smart automatic capture based on camera movement/view change.
- TODO — Guided coverage/overlap feedback.
- TODO — Quality presets: Medium / High / Original-Max.
- TODO — Storage/workspace estimates.
- TODO — Multiple elevation/ring capture workflow.
- TODO — Background/object masking for turntable scans.
- TODO — Native photogrammetry engine integration.
- TODO — Point-cloud preview.
- TODO — Mesh generation.
- TODO — Texture generation/application.
- TODO — Export common 3D formats.

## Laser scanning

- TODO — Calibration-target workflow.
- TODO — Camera calibration.
- TODO — Laser-plane calibration.
- TODO — Red/green/blue laser detection profiles.
- TODO — Locked camera settings for calibrated scans.
- TODO — Laser stripe extraction.
- TODO — Subpixel laser-line localization where practical.
- TODO — Triangulation to 3D points.
- TODO — Live point-cloud preview.
- TODO — Multi-pass point-cloud alignment.
- TODO — Mesh generation from laser scans.

## Hybrid scanning

- TODO — Associate photo and laser passes in one project.
- TODO — Align photogrammetry and laser coordinate systems.
- TODO — Fuse point clouds/meshes.
- TODO — Use photo textures on fused geometry.
- TODO — Quality/confidence visualization.

## External camera sources

- DONE — Built-in phone camera foundation.
- TODO — Camera-source interface.
- TODO — Reolink RLC-811A RTSP preview.
- TODO — Reolink high-resolution snapshot capture.
- TODO — Save calibration profiles by Reolink zoom/settings.
- TODO — USB UVC camera support.
- TODO — Arducam OV2311/B0322 proof-of-concept.
- TODO — Optional external hardware trigger support.

## Motion control

- TODO — Motion-controller interface.
- TODO — USB serial transport.
- TODO — Bluetooth/BLE transport.
- TODO — Wi-Fi transport.
- TODO — ESP32 reference firmware/protocol.
- TODO — Turntable position/speed control.
- TODO — Laser carriage position/speed control.
- TODO — Optional laser-angle axis control.
- TODO — Laser on/off control.
- TODO — Homing/limit-switch support.
- TODO — READY/error/position feedback.
- TODO — Emergency stop / abort scan.

## Automated workflows

- TODO — Step-and-capture laser scan.
- TODO — Automated photogrammetry turntable scan.
- TODO — Hybrid photo + laser sequence.
- TODO — Resume interrupted scan.
- TODO — Continuous-motion scan experimentation after step-and-capture works.

## Project management

- TODO — Project metadata and calibration profiles.
- TODO — Per-project storage usage display.
- TODO — Free-space checks.
- TODO — Export/import complete scan project.
- TODO — Diagnostics/logging.
