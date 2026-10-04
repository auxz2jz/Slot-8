# 3D Scan Studio — Cross-Platform Ownership Map

3D Scan Studio is ONE PRODUCT with TWO SEPARATE PLATFORM IMPLEMENTATIONS:

- Android implementation — owned by the Android worker/chat.
- Windows/PC implementation — owned by the future Windows/Codex worker.

The master collaboration rules are in:
`auxz2jz/master-instruction-library/CROSS_PLATFORM_COLLABORATION_STANDARD.md`

## Critical preservation rule

The existing repository root is the historical Android project workspace. It MUST NOT be reorganized merely to make the repository look cross-platform.

Existing root Android files remain where they are unless the user explicitly approves a later migration.

## Ownership zones

### Shared product area
`shared/`

May contain only platform-neutral product intent, feature definitions, shared terminology, data/interchange contracts, algorithm/stage definitions, calibration/export expectations, and cross-platform decisions.

### Android-owned area
`android/`

New Android-specific status/handoff documentation may live here.

The historical Android-owned root files also remain Android-owned, including:
- `ANDROID_FEATURES.md`
- `ENGINE_INTEGRATION.md`
- `HARDWARE.md`
- `LASER_LINE_RESEARCH.md`
- `PROJECT_CHECKPOINT.md`
- `PROJECT_NOTES.md`
- Android build notes, source manifests, test manifests/results, artifact hashes, and Android release records.

The root `README.md` is also currently Android-oriented legacy documentation and remains protected until an explicit migration is approved.

### Windows-owned area
`windows/`

The future Windows/Codex worker owns Windows source, project/build files, Windows memory/checkpoint/status/roadmap, tests, diagnostics, releases, and Windows verified baseline.

Android workers must not implement or modify Windows source unless the user explicitly authorizes it.

## Separate verification

Maintain independently:
- ANDROID LAST VERIFIED BASELINE
- WINDOWS LAST VERIFIED BASELINE

Verification on one platform never verifies the other.

## No-move rule

Do not move, rename, mass-clean, or refactor historical Android files simply to fit a new directory layout.

Cross-platform structure is additive first. Any future migration requires an explicit plan and user approval.
