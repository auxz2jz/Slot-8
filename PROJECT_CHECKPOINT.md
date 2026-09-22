# 3D Scan Studio — Recovery Checkpoint

## Current build

- Version: **v0.9.0**
- Android versionCode: **19**
- Package: `PhotogrammetryStudioAndroid-v0.9.0-Stage7-Surface-Mesh-Android-Studio-Ready.zip`
- Package SHA-256: `9d70424152884746fe693d49bcdde286e8230f9a7f75f597e88f521edccbe174`
- Source-manifest SHA-256: `4c84ca84258b447a616ff4ec092ed17900c14227c2df0f6c3b9716ca6432471a`
- v0.8.4 -> v0.9.0 patch SHA-256: `354cecfc80d2a1ce0d3941f6a1a9c80f99e33f9b37bd8e8100e6e512d27b3840`
- ZIP integrity test: **passed**
- Includes Gradle wrapper JAR and normal Android Studio project files.
- Android Studio/device compile: **pending**

## Last confirmed device results — v0.8.4 PASSED

Guide: **6 Works / 0 Problems / 0 Untested**.

test3:
- 95 photos
- 41 connected cameras
- 40 eligible dense candidates
- 6/6 selected pairs fused
- **41,017 fused points**
- readyForSurfaceReconstruction=true

test4:
- 175 photos
- 100 connected cameras
- 27 unusable connected candidates removed by v0.8.4 preflight
- 71 eligible dense candidates
- 4/6 selected pairs fused
- **40,922 fused points**
- readyForSurfaceReconstruction=true

v0.8.4 therefore fixed the candidate/world-pose issue without regressing test3.

## v0.9.0 — Stage 7 surface mesh

1. Stable reconstruction labels now use Stage 1 through Stage 7 rather than old release numbers as primary card names.
2. Primary continuation buttons explicitly name the next stage.
3. Stage 7 builds an experimental local-neighbor triangle mesh from the Stage 6 fused dense cloud.
4. Robust outlier trimming prevents isolated points from controlling mesh scale.
5. Adaptive voxel reduction and a 3D spatial hash keep the algorithm phone-friendly.
6. Long, degenerate, and highly stretched triangles are rejected.
7. First mobile safety cap: 6,500 mesh vertices target / 30,000 faces maximum.
8. Mesh persists with the project.
9. Full-screen wireframe viewer supports yaw/pitch/roll, zoom, and six orthogonal views.
10. Exports: Stage 7 report, OBJ, and face-containing ASCII PLY.
11. v0.9.0 test guide quotes the exact card/button wording shown on screen.

## Local mesher validation

The pure-Kotlin Stage 7 mesher was run against the uploaded v0.8.4 fused clouds:
- test4: 40,922 input points -> **5,795 mesh vertices / 30,000 triangles**.
- test3: 41,017 input points -> **2,840 mesh vertices / 28,555 triangles**.
- synthetic curved 25×25 grid: 625 vertices -> **2,304 triangles**.

The project-level Gradle build could not run here because this environment cannot resolve the Gradle distribution host. Android Studio remains the authoritative compile/install test.

## Exact next device test

1. Build/install v0.9.0.
2. Open **test4**.
3. Find **Stage 6 — Dense Fusion**.
4. Tap **Next: Stage 7 — Build surface mesh**.
5. On **Stage 7 — Surface Mesh**, tap **View Stage 7 mesh** and inspect all six views.
6. Export **Stage 7 mesh report**, **mesh OBJ**, and **triangle PLY**.
7. Leave/reopen test4 and confirm Stage 7 persists.
8. Repeat Stage 7 on test3.
9. Export the v0.9.0 version-test report and upload it with both Stage 7 mesh reports.

## External camera roadmap note

USB UVC still capture remains planned as a separate camera-source feature. The intended workflow is direct still capture into the current scan project, not mandatory video-frame extraction.


## v0.9.0 device test — PASSED

User completed v0.9.0 on Samsung SM-S908U1 / Android 16.

Version guide:
- **7 Works**
- **0 Problems**
- **0 Untested**

test4 Stage 7:
- source fused points: 40,922
- mesh vertices: 5,795
- triangles: 30,000 (hit safety cap)
- robustly trimmed: 146 extreme points
- readyForExport=true

test3 Stage 7:
- source fused points: 41,017
- mesh vertices: 2,840
- triangles: 28,555
- robustly trimmed: 933 extreme points
- readyForExport=true

All viewer, export, persistence, and stage-label tests passed.

### Topology inspection of exported v0.9.0 meshes

Independent inspection of the uploaded PLY face connectivity found that v0.9.0 succeeded as a triangle-generation proof of concept but is not yet a production-quality manifold mesh.

test4:
- 30,000 faces, no invalid indices, no duplicate faces
- 3,070 mesh vertices are unused by any face
- 11,137 undirected edges have more than two incident faces
- 7,182 boundary edges
- several disconnected surface components

test3:
- 28,555 faces, no invalid indices, no duplicate faces
- 107 unused vertices
- 11,600 undirected edges have more than two incident faces
- 5,492 boundary edges
- several disconnected surface components

This is consistent with the v0.9.0 local-neighbor combinatorial surface strategy: it deliberately proved that the phone could create/export triangles, but many overlapping local triangles can share the same edge.

## Next build — v0.9.1 topology cleanup

1. Generate a more ordered local fan around each mesh vertex rather than accepting every neighbor pair combination.
2. Enforce at most two faces per undirected edge.
3. Compact/remove vertices unused by any accepted face.
4. Remove only tiny disconnected fragments while preserving meaningful larger surface sections.
5. Add topology diagnostics to the Stage 7 report: used vertices, boundary edges, non-manifold edges, component counts, and rejected cleanup faces.
6. Keep Stage 1–7 UI wording unchanged.
7. Retest test4 and test3 before beginning texture projection.
