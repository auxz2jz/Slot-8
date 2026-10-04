# 3D Scan Studio — Shared Decisions

## 2026-09-21 — Android + Windows product direction
Use one 3D Scan Studio product with an Android capture/on-device implementation and a future Windows implementation for heavier processing and expanded hardware support.

## 2026-09-26 — Cross-platform collaboration rule
Use shared product information with separate Android and Windows ownership, baselines, tests, diagnostics, versions, and artifacts.

## 2026-10-04 — Laser diagnostic interchange
Laser threshold experiments must preserve every Analyze run. The interchange format is platform-neutral JSON Lines so a future Windows implementation can consume the same run records.

## 2026-10-04 — Conservative cross-platform ownership boundary
The historical repository root remains the protected Android workspace. Cross-platform structure is additive: `shared/` holds platform-neutral product information, `android/` holds Android coordination, and `windows/` is reserved for the future Windows/Codex implementation. No Android source or historical Android file is moved, renamed, or refactored solely for repository cleanliness. See `../CROSS_PLATFORM_OWNERSHIP.md`.
