# Laser-line 3D scanner research — v0.19.0

This document records the public/open-source projects being used as architectural and algorithmic references for the Laser Scan / Hybrid Scan roadmap.

## Reference projects

### LibreScanner / Horus + Ciclop
- Repositories: LibreScanner/horus, LibreScanner/ciclop, LibreScanner/horus-fw
- Purpose: general-purpose laser-line scanning application and open Ciclop turntable scanner.
- Useful ideas: camera/laser calibration workflow, turntable control, laser on/off control, scan configuration, point-cloud generation, firmware protocol.
- Horus is GPL v2; Ciclop mechanical files are CC BY-SA 4.0. Treat Horus code as a reference unless project licensing is deliberately made compatible.

### FreeLSS
- Repository: hairu/freelss
- Purpose: Raspberry Pi turntable laser scanner with a web UI.
- Useful ideas: self-contained turntable scan state machine, laser/camera geometry, scan capture sequencing, hardware GPIO separation.
- License: GPL-3.0. Use as design/algorithm reference unless licensing strategy changes.

### FabScanPi
- Repository: mariolukas/FabScanPi-Server
- Purpose: Raspberry Pi + camera + red line laser + NEMA17 turntable scanner.
- Useful ideas: scanner calibration, hardware driver abstraction, serial/stepper control, web UI, simulation without physical hardware.
- License stated by the main project README: GPL v2. Use as architecture/reference unless licensing strategy changes.

### songyuncen / laser-triangulation
- Repository: songyuncen/laser-triangulation
- Purpose: OpenCV implementation of sheet-of-light / laser triangulation.
- Useful source separation: calibrate_camera, calibrate_laser, calibrate_movement, extract, reconstruction, plane fitting, back-projection, PLY output.
- License: MIT.
- This is the most permissively licensed direct algorithm reference found so far and maps closely to our desired Android modules.
- Its README also warns that its laser-line extraction is not especially robust, so our OFF/ON differencing and diagnostics should remain independently tested.

### Sardauscan
- Repository: Sardau/Sardauscan
- Purpose: low-cost multi-laser scanner using Arduino, camera and small stepper hardware.
- Useful ideas: plugin/task pipeline, support for multiple lasers, hardware independence, separate scan/filter/smooth/mesh tasks.

## Our Android roadmap derived from this research

1. v0.19 — 2D laser-line extraction foundation
   - Laser/Hybrid project opens Laser Line Lab.
   - Import matched laser-OFF and laser-ON frames.
   - Color-difference extraction for red/green/blue line lasers.
   - Weighted sub-pixel stripe centroid per image row.
   - Coverage, continuity, signal and stripe-width diagnostics.
   - Overlay preview and report export.
   - No 3D claim yet.

2. Camera calibration
   - Already introduced in v0.18.
   - Proper printed target remains pending user test.

3. Laser-plane calibration
   - Detect laser stripe on a known calibration plane at several poses.
   - Back-project stripe rays using calibrated camera intrinsics.
   - Fit the 3D laser plane.
   - Persist laser-plane equation and quality metrics.

4. Turntable / movement calibration
   - Establish rotation axis, center and degrees/step.
   - Manual angle entry first; ESP32/Arduino control later.

5. Laser triangulation
   - Intersect calibrated camera ray for each stripe sample with calibrated laser plane.
   - Transform points by turntable angle into common coordinates.
   - Accumulate scan cloud and export PLY.

6. Hardware control
   - Abstract controller interface: laser on/off, turntable step, optional laser carriage.
   - ESP32/Arduino serial/BLE/Wi-Fi implementation later.
   - Architecture should remain usable with manual capture/control.

7. Hybrid fusion
   - Align laser-derived geometry with photo-derived texture/geometry in the same project.

## Licensing rule

Do not copy GPL implementation code into the app unless the project license is intentionally made GPL-compatible. GPL projects are valuable references for workflows and algorithms. Prefer independent reimplementation from documented mathematics and permissive sources (such as the MIT laser-triangulation project) when code reuse is desired.


## Current physical target — manual DAVID-style scanner

The first hardware target is intentionally simpler than Horus/Ciclop/FabScanPi:
1. stationary object on a table;
2. fixed camera/phone;
3. rigid 90° calibration corner/backdrop with printed calibration markers;
4. hand-held line laser swept over the object;
5. capture frames while the stripe moves across the surface;
6. reconstruct from calibrated camera geometry plus detected laser stripe.

This mirrors the classic DAVID Laserscanner workflow more closely than a turntable scanner. The turntable/controller architecture remains useful later but is not required for the first laser reconstruction milestone.

Important design implication:
- do not hard-code a permanently fixed 45° laser plane as the only mode;
- support a manual swept-laser workflow in which the laser plane can vary from frame to frame if the calibration background supplies enough information to recover that plane;
- retain a fixed-laser-plane mode as an optional later/simple case.

Future hardware automation:
- turntable;
- Arduino/ESP32 motor control;
- fixed or motorized laser carriage;
- automatic capture sequencing.
