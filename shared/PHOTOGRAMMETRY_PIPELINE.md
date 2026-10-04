# 3D Scan Studio — Shared Photogrammetry Pipeline Definitions

This file defines platform-neutral stage meaning. Android and Windows may use different libraries, UI, threading, hardware acceleration, or internal architecture.

## Stage 1 — Feature analysis and matching
Detect useful image features and establish reliable correspondences between overlapping photos.

Expected outputs:
- per-photo feature information;
- pair match diagnostics;
- usable/rejected pair reasons.

## Stage 2 — Initial sparse reconstruction
Choose a geometrically valid starting pair and triangulate an initial sparse 3D structure.

Expected outputs:
- initial camera relationship;
- sparse 3D points;
- quality/readiness diagnostics.

## Stage 3 — Multi-view reconstruction
Extend the reconstruction through the connected photo/camera set and combine additional triangulated structure.

Expected outputs:
- connected camera poses;
- multi-view sparse geometry;
- accepted/rejected connection diagnostics.

## Stage 4 — Bundle refinement
Jointly refine camera geometry and 3D structure to reduce reprojection error.

Expected outputs:
- refined camera parameters/poses;
- refined sparse points;
- before/after reprojection metrics.

## Stage 5 — Dense pair reconstruction
Generate dense depth/point information from one or more suitable image pairs.

Expected outputs:
- disparity/depth diagnostics;
- dense points;
- pair quality/readiness information.

## Stage 6 — Dense fusion
Combine accepted dense pair results into a common point cloud.

Expected outputs:
- fused point cloud;
- per-pair acceptance/rejection reasons;
- readiness for surface reconstruction.

## Stage 7 — Surface mesh
Convert fused geometry into a usable triangle surface while preserving topology/quality diagnostics.

Expected outputs:
- vertices;
- triangle faces;
- normals where available;
- boundary/manifold/component diagnostics;
- OBJ/PLY-compatible geometry.

## Stage 8 — Photo color projection
Project source imagery onto reconstructed geometry to validate photo/camera alignment and derive appearance/color information.

Expected outputs:
- per-vertex or equivalent projected color information;
- camera contribution/coverage diagnostics.

## Stage 9 — Texture source assignment
Choose an appropriate source image for textureable surface regions/faces and preserve image-space mappings.

Expected outputs:
- source-photo assignments;
- coverage;
- assignment/rejection diagnostics.

## Stage 10 — Texture atlas/export preparation
Build UV mapping/atlas data and standard textured-model outputs.

Expected outputs:
- UVs/texture atlas;
- OBJ + MTL + image or equivalent documented interchange;
- coverage/occupancy/export-readiness diagnostics.

## Shared behavior

- Upstream rebuilds invalidate stale downstream results.
- Each stage preserves useful diagnostics and rejection reasons.
- A stage is not considered successful solely because a process exited.
- Platform implementations may add internal sub-stages without changing these shared user-visible meanings.
