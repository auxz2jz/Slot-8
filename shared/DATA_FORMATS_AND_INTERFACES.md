# 3D Scan Studio — Shared Data Formats and Interfaces

## Laser analysis history v1

Canonical interchange filename:
`laser_analysis_history.jsonl`

Encoding:
UTF-8 JSON Lines; one complete JSON object per line.

Each line represents one laser Analyze attempt.

Required fields:
- `schemaVersion`: integer, currently 1
- `runId`: unique string
- `timestampEpochMs`: integer
- `timestampUtc`: ISO-8601 UTC string
- `laserColor`: `RED`, `GREEN`, or `BLUE`
- `threshold`: integer
- `success`: boolean
- `durationMs`: integer

Recommended context fields:
- `engine`
- `projectId`
- `laserOffFileName`
- `laserOnFileName`
- `laserTargetPresetId`
- `imageWidth`
- `imageHeight`

Successful-run measurement fields:
- `detectedRows`
- `coveragePercent`
- `continuityPercent`
- `meanSignal`
- `meanLineWidthPx`
- `minX`
- `maxX`
- `readyForTriangulation`

Failure fields:
- `errorType`
- `errorMessage`
- `errorStackTrace`

Compatibility rules:
- readers must ignore unknown fields;
- new optional fields may be added without changing schemaVersion;
- breaking semantic changes require a new schemaVersion;
- JSONL history is diagnostic metadata and does not replace the detailed latest laser-line report/preview.
