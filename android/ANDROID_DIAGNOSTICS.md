# Android Diagnostics Ownership

Android diagnostics follow the master `DIAGNOSTICS_STANDARD.md`.

Existing Android diagnostics include specialized reconstruction stage reports, combined project diagnostics, guided version-test reports, capture diagnostics, pipeline history, calibration reports, and laser diagnostics.

Cross-platform diagnostic concepts may be shared, but Android diagnostic implementation and Android test evidence remain Android-owned.

Shared structured diagnostic contracts, such as `laser_analysis_history.jsonl`, are documented under `../shared/`.
