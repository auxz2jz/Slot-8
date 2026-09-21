# 3D Scan Studio — Project Notes

## Core goal

Build a self-contained Android 3D scanning application in Kotlin + Jetpack Compose. The Android device is the main controller and processing device. Core scanning should work locally without GitHub or required cloud processing.

## Scan modes

### Photogrammetry
- Capture many overlapping photos and reconstruct a 3D model.
- Support built-in phone camera, imported photos, external IP camera, and USB UVC camera sources.
- Guided single-shot mode.
- Automatic/smart capture mode.
- Import-existing-photos mode.
- Preserve originals and create lower-resolution working copies only when useful.
- Prefer high-quality still images over compressed video frames for reconstruction.
- Support multiple elevation/ring passes around an object.

### Laser Line Scan
- DAVID-style laser triangulation.
- Camera is stationary during a scan pass.
- A visible line laser is swept across the object.
- Calibration target/corner establishes the camera/laser geometry.
- Detect laser stripe, triangulate 3D points, generate point cloud, mesh, and model.
- Support red/green/blue visible lasers with color-specific detection settings.
- Lock focus, exposure, white balance, zoom, day/night mode, and camera geometry during a calibrated scan.
- Start with step-and-capture rather than continuous-motion scanning.

### Structured-Light / Depth Scan
- Support depth cameras that project an infrared pattern or otherwise provide a calibrated depth map (for example Kinect-style structured light or active-stereo depth cameras).
- Treat the depth stream as another geometry source inside the same scan project.
- Prefer devices with documented USB/Android SDK access when possible.
- Candidate hardware families include the existing Xbox 360 Kinect for experimentation and modern Orbbec/Astra-style depth cameras.
- Keep the Android device as the main controller/processor when the selected sensor can stream depth locally over USB/network.

### Hybrid Scan
- Capture ordinary photographs with the laser off.
- Capture laser geometry with the laser on.
- Align/fuse photogrammetry, laser-line, and structured-light/depth point-cloud/mesh data.
- Use photographs for realistic texture/color and laser data to strengthen geometry.
- Keep all data in one scan project so the user does not need separate apps.

## Camera-source abstraction

The app should eventually expose one common camera-source interface:

- Built-in Android camera via CameraX.
- Reolink RLC-811A over local LAN.
- USB UVC camera.
- Imported image set.

This allows the scanning/reconstruction code to operate independently of where frames/images came from.

## Reolink RLC-811A

Known available camera:
- 8 MP / 4K network camera.
- Motorized optical zoom.
- RTSP/live stream available.
- High-resolution snapshot path can be used for photogrammetry.
- Store a separate calibration profile for each chosen zoom position.
- Once calibrated, camera position, zoom, resolution, day/night mode, and other optical settings must remain fixed for that profile.
- For laser scans, disable unwanted IR/spotlight behavior and keep optical mode fixed.

## USB global-shutter camera option

Candidate discussed:
- Arducam OV2311/B0322 2 MP monochrome global-shutter USB UVC camera.
- Useful as a dedicated laser-geometry camera.
- Android/UVC compatible.
- Manual/fixed focus is desirable.
- External hardware trigger support is attractive for synchronized step-and-capture.
- Because it is monochrome and only 2 MP, it is not the preferred sole camera for final color photogrammetry/textures.

Possible later option:
- Higher-resolution color UVC/global-shutter camera with manual controls, so one camera could potentially serve both photogrammetry and laser scanning.

## Processing architecture

Android device responsibilities:
- User interface.
- Scan planning.
- Camera acquisition.
- Photo management.
- Laser-line detection.
- Triangulation.
- Point-cloud generation.
- Photogrammetry processing.
- Mesh creation/cleanup.
- Hybrid alignment/fusion.
- Texture generation/application.
- Project storage.
- Communication with motion controller.

Microcontroller responsibilities:
- Deterministic low-level motor control.
- Laser power switching through proper driver electronics.
- Position tracking.
- Motion completion/READY reporting.

## Automated scanner concept

Physical system can include:
- Lazy-Susan style motorized turntable.
- Stepper-driven laser carriage on a lead screw.
- Optional motorized laser-angle axis.
- Stationary camera.
- ESP32 or Arduino motion controller.
- Android phone/tablet as controller and processor.

Example step-and-capture sequence:
1. Android commands turntable angle.
2. Controller moves and reports READY.
3. Android commands laser carriage height.
4. Controller moves and reports READY.
5. Optional laser-angle command.
6. Android commands laser ON.
7. Camera captures a frame/image.
8. Android processes laser stripe.
9. Android commands laser OFF.
10. Repeat for next height/angle/rotation.

For photogrammetry:
1. Laser OFF.
2. Rotate object to a known angle.
3. Stop and settle.
4. Capture high-quality still image.
5. Repeat through 360 degrees.
6. Repeat at additional camera elevations/angles when needed.

## Photo-quality strategy

Do not assume maximum camera resolution is always best.
- Default to a practical high-quality mode.
- Preserve source originals.
- Offer Medium / High / Original-Max quality presets.
- Avoid repeated recompression of originals.
- Smart capture should avoid hundreds of nearly identical frames.
- Show photo count, project storage, estimated reconstruction workspace, and free device storage.

## Runtime requirement

The finished Android app should be self-contained as much as practical:
- No runtime dependency on GitHub.
- No required cloud reconstruction.
- Bundle required native libraries/configuration/resources locally.
- Network use may be required only when the selected camera/controller is itself a network device.
- Optional cloud/sync features may be added later, but core scanning remains local.

## Existing source repositories available

Photogrammetry engines:
- COLMAP
- OpenMVG
- OpenMVS
- AliceVision
- Meshroom
- ODM / OpenDroneMap
- MicMac

Mesh/model tools:
- Manifold
- Assimp
- trimesh
- meshoptimizer
- MeshLab

These 12 source repositories are already present in the user's GitHub account. They are development sources; the Android app must not call GitHub at runtime.

## Development principle

Do not try to force every desktop application directly onto Android. Reuse/port practical libraries and algorithms, compile suitable C/C++ components with the Android NDK, and keep a clean engine-adapter interface. Desktop-only/heavy components can remain candidates for a separate Windows companion later.

## Near-term order

1. Test v0.1 Android app.
2. Fix build/runtime problems.
3. Establish final project structure in this repository.
4. Add scan-mode selection: Photogrammetry / Laser / Hybrid.
5. Add camera-source abstraction.
6. Add ESP32/Arduino motion-control abstraction.
7. Integrate first practical on-device photogrammetry path.
8. Add DAVID-style laser triangulation.
9. Add automated turntable + laser workflow.
10. Add hybrid point-cloud/mesh alignment and fusion.
