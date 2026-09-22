# 3D Scan Studio — Recovery Checkpoint

## Current build

- Version: **v0.8.4**
- Android versionCode: **18**
- Package: `PhotogrammetryStudioAndroid-v0.8.4-Android-Studio-Ready.zip`
- Package SHA-256: `e9c1b5ce1fecbcccd41e698084b95eb2ca3cabf0bbcb6753782d2b0339345a2e`
- Canonical repository: `auxz2jz/Slot-8`, branch `main`
- Packaging status: **corrected Android Studio-ready ZIP verified**
- Includes: `settings.gradle.kts`, root/app Gradle files, `gradlew`, `gradlew.bat`, `gradle-wrapper.properties`, and included `gradle-wrapper.jar`
- ZIP integrity test: **passed**
- Device compile/test: **pending**

## Last confirmed device results

v0.8.3 passed its recovery objective:
- Version guide: **6 Works / 0 Problems / 0 Untested**
- test3: 95 photos, 41 connected cameras, 30,000-point dense seed, 6/6 selected fusion pairs accepted, 41,017 fused points, ready for surface reconstruction.
- test4: 175 photos, 100-camera refined component, bundle RMS 12.3533 px -> 2.2892 px, corrected connected-component dense seed 30,000 points, 14,191 fused points, ready for surface reconstruction.

## v0.8.4 maintenance fix

v0.8.3 exposed a smaller fusion-selection issue: two selected later test4 pairs were inside the refined component but their first camera had no completed shared-world pose after an earlier break in the fusion engine's separately rebuilt rotation chain. They were selected anyway and later reported with the overly broad `Missing refined pose or image file.` reason.

v0.8.4 now:
1. Preflights every dense-fusion candidate before distributed selection.
2. Requires both image files.
3. Requires both refined camera centers.
4. Requires a completed shared-world pose for the pair's first camera.
5. Selects up to six distributed pairs only from this eligible set.
6. Records how many geometric candidates were filtered before selection.
7. Uses precise defensive reasons for missing camera index, first/second image, refined center, or world pose.
8. Adds a v0.8.4-specific in-app test guide.

Dense-stereo math, dense-pair reconstruction math, global trimming, voxel downsampling, and readiness thresholds are unchanged.

## Validation limitation

This recovery environment has Java and Kotlin but no Android SDK, no system Gradle, and no local Gradle wrapper JAR. Therefore the v0.8.4 package was source-checked and ZIP-integrity-tested here, but **not compiled into an APK in this environment**. Android Studio on the user's development PC remains the authoritative compile/install check.

## Next device test

1. Open the v0.8.4 project in Android Studio and build/install it.
2. Run the built-in v0.8.4 guide.
3. Open test4 and rebuild multi-pair dense fusion.
4. Confirm selected pairs no longer fail with the old generic missing-pose-or-image message.
5. Export the test4 dense-fusion report.
6. Re-run test3 fusion as a regression check.
7. Export the v0.8.4 version-test report.
8. If regression passes, begin **surface/triangle mesh reconstruction**.

## GitHub checkpoint rule

Keep GitHub current after meaningful code/version changes, before and after long device tests, when a root cause is identified, before packaging a replacement build, and whenever a chat approaches its context limit.


## Packaging correction — 2026-09-22

The first v0.8.4 ZIP was labeled Android Studio-ready while omitting `gradle/wrapper/gradle-wrapper.jar` and relying on a bootstrap download. That package has been superseded.

Use only:
`PhotogrammetryStudioAndroid-v0.8.4-Android-Studio-Ready.zip`

SHA-256:
`e9c1b5ce1fecbcccd41e698084b95eb2ca3cabf0bbcb6753782d2b0339345a2e`

The replacement includes the wrapper JAR and all required project files. No patch file or manual source copying is required.
