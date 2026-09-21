# Hardware Plan

## Controller architecture

The Android phone/tablet is the main brain and processing device.

An ESP32 or Arduino is the low-level motion controller.

### Android device
Responsible for:
- User interface.
- Scan sequencing.
- Camera/image acquisition.
- Image processing.
- 3D reconstruction.
- Project storage.
- Sending motion/laser commands.

### ESP32 / Arduino
Responsible for:
- Stepper timing.
- Turntable motion.
- Laser-carriage motion.
- Optional laser-angle motion.
- Laser power switching.
- Limit switches/homing.
- Reporting READY/position/error states.

ESP32 is attractive because Wi-Fi and Bluetooth are built in, but USB serial should also be supported.

## Turntable

Concept:
- Lazy-Susan style rotating platform.
- Stepper motor for repeatable angular positioning.
- Configurable speed and step size.
- Known absolute/relative angle tracked by the controller.
- Possible home/index sensor.

Example capture intervals:
- 10 degrees = 36 images per revolution.
- 5 degrees = 72 images.
- 3 degrees = 120 images.

These are examples, not fixed requirements.

## Laser carriage

Concept:
- Line laser mounted on a vertical lead screw.
- Stepper motor controls height.
- Optional third axis controls laser angle.
- Laser ON/OFF controlled electrically by ESP32/Arduino through appropriate driver hardware, not directly from a GPIO pin.

Start with:
- Fixed laser angle.
- Motorized vertical motion.
- Step-and-capture.

Add motorized laser-angle adjustment only after the simpler geometry works.

## Cameras

### Reolink RLC-811A
Available hardware.
Use cases:
- External stationary color camera.
- RTSP/live preview.
- High-resolution snapshots for photogrammetry.

Important:
- Motorized zoom means each zoom position/settings set needs its own calibration profile.
- Never change zoom or move the camera during a calibrated scan.
- Keep day/night/IR/spotlight behavior fixed for laser scanning.

### Built-in phone camera
Use cases:
- Mobile photogrammetry.
- Guided hand-held capture.
- Potential laser scanning when mounted stationary.

During laser scan:
- Lock focus.
- Lock exposure.
- Lock white balance.
- Lock zoom.
- Keep resolution and optical mode fixed.

### USB UVC global-shutter camera
Candidate:
- Arducam OV2311/B0322 2 MP monochrome global shutter.

Strengths:
- Dedicated laser geometry camera.
- Global shutter.
- Android/UVC compatibility.
- Manual/fixed-focus style use.
- Hardware triggering potential.

Limitations:
- Monochrome.
- 2 MP is not ideal as the sole high-detail color photogrammetry camera.

## Possible two-camera configuration

- USB monochrome global-shutter camera for precise laser measurement.
- Phone camera or RLC-811A for high-resolution color photos/textures.

Because both cameras can be calibrated relative to the same turntable/scanner frame, their data can be aligned later.

## Lighting

Photogrammetry:
- Even, diffuse illumination.
- Laser off.
- Avoid shiny/specular glare where possible.

Laser scan:
- Visible line laser.
- Controlled/dim ambient lighting is preferred.
- Disable unnecessary IR illumination.
- Background subtraction/frame differencing can help isolate the stripe.

## Safety

Use an appropriately low-power visible line laser and avoid eye exposure and reflective surfaces. The software cannot make an unsafe laser safe.

## Proposed command concepts

Examples only; final protocol may change:

- `HOME ALL`
- `ROTATE 45.0`
- `TURN_SPEED 20`
- `LASER_HEIGHT 120.0`
- `LASER_SPEED 10`
- `LASER_ANGLE 30.0`
- `LASER ON`
- `LASER OFF`
- `STATUS`

Controller responses:
- `READY`
- `BUSY`
- `POSITION ...`
- `ERROR ...`

The Android app should not capture a step-and-capture frame until the controller reports that motion has completed.
