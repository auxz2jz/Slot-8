# 3D Scan Studio — Shared Requirements

1. Android and Windows are separate implementations of one product.
2. Platform-specific source, builds, tests, diagnostics, checkpoints, and verified baselines remain separate.
3. Shared feature names and core behavior should remain recognizable across platforms.
4. Project interchange should use documented, versioned, platform-neutral formats where practical.
5. Existing user source media must not be silently modified by processing.
6. Rebuilding an upstream reconstruction stage invalidates stale downstream results.
7. Diagnostic actions must distinguish user request, operation start, progress/state, result, and failure.
8. Laser Analyze runs must append to history rather than replacing historical diagnostic evidence.
9. Calibration records must include the physical target geometry actually used, not merely a paper-size label.
10. A platform must not mark the other platform's feature VERIFIED.
