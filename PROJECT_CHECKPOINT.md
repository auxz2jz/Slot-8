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


## v0.9.1 build prepared

- Version: **0.9.1**
- versionCode: **20**
- Package: `PhotogrammetryStudioAndroid-v0.9.1-Stage7-Topology-Cleanup-Android-Studio-Ready.zip`
- Package SHA-256: `a52e4fbdf42f6ef022f609a462c0d595ee686bb414474b1a5e5fc72008d9df83`
- Source-manifest SHA-256: `d14b74ba1a815221818daf45ece5a7e2334301527407354cc7e423f7d56b1ea5`
- v0.9.0 -> v0.9.1 patch SHA-256: `54d67a8529dd80bc7438dde9d5739ded7b8f9e5cd8b681d382c9a8bd1f4b36c3`
- ZIP integrity test: passed
- Included Gradle wrapper JAR: yes
- Pure Kotlin mesher compile: passed
- Kotlin validation on uploaded test4/test3 fused clouds: passed
- Android Studio/device compile/install: pending

Validated Kotlin topology result:
- test4: 5,172 vertices / 12,872 faces / 7,982 boundary edges / **0 non-manifold edges** / 11 retained components.
- test3: 2,144 vertices / 4,938 faces / 3,364 boundary edges / **0 non-manifold edges** / 34 retained components.

Next device test: rebuild Stage 7 on test4 and test3, verify Non-manifold edges = 0, inspect viewer, export both Stage 7 reports/OBJ/PLY, verify persistence, then export the v0.9.1 version-test report.


## v0.9.1 device test / UI state issue — 2026-09-22

The v0.9.1 version test report shows:
- Samsung SM-S908U1 / Android 16
- **7 Works / 0 Problems / 0 Untested**
- test4 Stage 7: 5,172 vertices / 12,872 triangles / 7,982 boundary edges / **0 non-manifold edges** / 11 retained components / readyForExport=true
- test3 Stage 7: 2,144 vertices / 4,938 triangles / 3,364 boundary edges / **0 non-manifold edges** / 34 retained components / readyForExport=true

User found a reconstruction-state UI bug while intentionally rerunning test4/test3 from Stage 1:
- Stage 7 remains visible immediately after Quick check and Stage 1 rerun.
- It disappears only when Stage 6 begins, then reappears after Stage 7 is rebuilt.

Root cause confirmed in v0.9.1 source:
- `matchSelectedPhotos()`, `startReconstruction()`, `startMultiViewReconstruction()`, and `startBundleAdjustment()` clear saved downstream results through repository cascade, but do not set `_surfaceMeshReport.value = null`.
- `startDenseFusion()` does set `_surfaceMeshReport.value = null`, which explains why the stale Stage 7 card disappears when Stage 6 begins.
- There is currently no reconstruction-pipeline reset action; existing Reset controls only reset viewer orientation.

### Required next UI/state fix

1. Centralize downstream invalidation so rerunning any earlier stage immediately clears all later in-memory cards as well as persisted files.
2. Add a visible **Reset reconstruction stages** action for the current project.
3. Reset must preserve the project's photos and project metadata.
4. Reset must clear Stage 1–7 saved reports/models and return the reconstruction UI to the starting state.
5. Use a confirmation dialog explaining that photos are kept but all generated reconstruction results are removed.
6. Optionally add a safer per-stage “Rebuild from here” behavior by automatically invalidating only later stages when an earlier stage is rerun.
7. Add this exact behavior to the next version test guide.

Additional test4 artifacts are still being uploaded; defer final next-geometry decision until the set is complete.


## v0.9.1 complete test set — PASSED

Full uploaded v0.9.1 pipeline set is now complete.

Version guide:
- 7 Works / 0 Problems / 0 Untested
- Samsung SM-S908U1 / Android 16

test4 full pipeline:
- Feature match: 175 photos, 437,500 keypoints, 174/174 usable-or-strong adjacent pairs
- Sparse: 1,096 triangulated points; ready for multi-view
- Multi-view: 100 connected cameras, 99 connected adjacent pairs, 36,702 combined points
- Bundle: 6,654 tracks built, 1,200 selected, 957 refined points, RMS 12.3533 px -> 2.2892 px
- Dense pair: 143,648 valid disparity pixels, 30,000 exported points
- Dense fusion: 71 eligible pair candidates, 4 selected pair clouds accepted, 40,922 fused points
- Stage 7: 5,172 vertices, 12,872 faces, 7,982 boundary edges, 0 non-manifold edges, 11 retained components

test3 Stage 7 regression:
- 2,144 vertices, 4,938 faces, 3,364 boundary edges, 0 non-manifold edges, 34 retained components

The uploaded rerun also confirmed persisted stage files are regenerated consistently. The stale Stage 7 observation is a UI/in-memory invalidation defect, not stale geometry being reused from disk.

See TEST_OUTPUT_MANIFEST_v0.9.1.sha256 for hashes of the 18 uploaded v0.9.1 artifacts.

## v0.9.2 build prepared

- Version: **0.9.2**
- versionCode: **21**
- Package: `PhotogrammetryStudioAndroid-v0.9.2-Pipeline-Reset-State-Fix-Android-Studio-Ready.zip`
- Package SHA-256: `5f6b078a1b960c7220f36c1854f65cd03a3f9797a648706efc01c5c94454abdc`
- Source-manifest file SHA-256: `fcfd45dfe7983728bfac1a23e167762b795224b28d55419b99ffdd1909965696`
- v0.9.1 -> v0.9.2 patch SHA-256: `8aee4f94489673a1f793cc01fc5ca1ab009f00a589ecc9b4dfc35fde51563ca0`
- ZIP integrity test: passed
- Includes Gradle wrapper JAR: yes
- Geometry algorithms: unchanged from v0.9.1
- Android Studio/device compile: pending

v0.9.2 changes:
1. Centralized memory + disk invalidation at every pipeline stage boundary.
2. Rebuilding Stage 1 immediately clears Stages 1–7; Stage 2 clears 2–7; and so on.
3. Stage 1 now clears the old persisted feature-match result before replacement matching starts.
4. Added **Reset reconstruction stages** with confirmation.
5. Reset keeps project photos/settings and deletes generated Stages 1–7 only.
6. Reset is disabled while analysis/reconstruction is actively running.
7. Added exact v0.9.2 in-app test steps for cancel/reset/stale-card/persistence behavior.

Next after v0.9.2 passes: Stage 7 boundary/hole cleanup plus normals/smoothing before texture projection.


## v0.9.2 cross-project live-state observation — 2026-09-22

User started Stage 1 feature matching on test4, navigated back to the project list, and opened test3 while test4 matching was still running. test3 appeared to show the live matching activity.

Source inspection confirms:
- Each reconstruction function captures the selected PhotoProject at job launch.
- Stage inputs and repository saves use that captured project's ID/photos, so test4 Stage 1 continues reading/saving test4 data even after navigation.
- However, live `_reconstruction` progress and stage report StateFlows are global to MainViewModel, not keyed by project ID.
- A background job from test4 can therefore update the progress/status/report displayed while test3 is selected.
- Several later-stage success handlers also assign `_selectedProject = repository.getProject(project.id)`, which could pull the UI back to the job-owning project when a background job finishes.

Project-isolation requirement:
1. Photos, reports, point clouds, meshes, and settings remain stored strictly by project ID.
2. Every running reconstruction job records an immutable ownerProjectId.
3. Background job callbacks update shared UI only when the currently selected project matches ownerProjectId.
4. Completion of a background job must never change the user's currently selected project.
5. When reopening the owner project, load its newly completed result from repository.
6. Use one heavy reconstruction worker at a time for now; if another project's job is requested while one is active, show which project is processing instead of silently running concurrent heavy jobs.
7. Optional future UI: project-list badge such as "Processing Stage 1" / "Completed" for background work.
8. Cross-project merge/compare remains explicitly out of scope unless intentionally added later.

This is a live-state isolation issue, not evidence of on-disk project-data contamination.


## v0.9.2 device result — PASSED

- Guide: **7 Works / 0 Problems / 0 Untested**
- Reset kept test3's 95 photos and cleared generated Stages 1–7.
- Stage rebuild invalidation behaved correctly and stale Stage 7 cards no longer survived an upstream rebuild.
- test4 remained healthy through 40,922 fused points and its topology-clean Stage 7 result.

### Cross-project observation after v0.9.2

While test4 Stage 1 was running, the user opened test3. Source review confirmed project files/results were still keyed to the correct project ID, but live progress/report StateFlows were shared globally. This could make test3 *look* as though it was processing test4's job. Some later-stage completion paths could also reselect the job-owning project.

## v0.10.0 build prepared

- Version: **0.10.0**
- versionCode: **22**
- Package: `PhotogrammetryStudioAndroid-v0.10.0-Project-Isolation-Surface-Quality-Android-Studio-Ready.zip`
- Package SHA-256: `f9bca4a4096189c020c1c640b511fd9da0684ccd18507f3948b2224252687ba7`
- Source-manifest file SHA-256: `c707b56cdc7766270ee1d11a90893d4054730863f4f35472ab209cad4a174fa8`
- ZIP integrity test: **passed**
- Gradle wrapper JAR included: **yes**
- Full Android compile here: blocked only because this environment cannot resolve services.gradle.org; Android Studio/device build remains authoritative.

### Project-scoped execution

1. Every heavy Stage 1–7 job has an immutable owner project ID/name/stage.
2. Job progress/results are only published to the screen when that owner project is selected.
3. Switching to another project no longer displays the first project's live progress.
4. Background completion never changes the user's selected project.
5. Reopening the owner project reloads its saved result from that project's repository folder.
6. One heavy reconstruction job runs at a time for now; a second project receives an explicit message naming the project already processing.
7. No cross-project data sharing is allowed unless a deliberate future merge/compare workflow is implemented.

### Stage 7 surface-quality progress

v0.10.0 keeps the v0.9.1 manifold local-fan topology and adds:
- two conservative smoothing passes on interior vertices only;
- fixed boundary vertices during smoothing;
- area-weighted per-vertex normals;
- normal-aware OBJ and PLY export;
- closed boundary loop vs open/branched boundary-component analysis;
- boundary branch-vertex count.

No holes are automatically filled yet.

### Local Kotlin validation on the real fused clouds

- test4: 5,172 vertices / 12,872 faces / 7,982 boundary edges / 0 non-manifold / 1 closed loop / 5 open-or-branched groups / 2,480 branch vertices / 5,172 valid normals.
- test3: 2,144 vertices / 4,938 faces / 3,364 boundary edges / 0 non-manifold / 1 closed loop / 5 open-or-branched groups / 1,043 branch vertices / 2,144 valid normals.

Next after device validation: conservative filling of small/simple closed loops, then texture-preparation work.


## v0.10.0 device test — PASSED

- Version guide: **8 Works / 0 Problems / 0 Untested**
- Device: Samsung SM-S908U1 / Android 16
- Project-isolation behavior passed.
- test3 Stage 7 remained 2,144 vertices / 4,938 faces / 0 non-manifold edges with 2,144 valid normals.
- Boundary classification remained stable enough to begin conservative closed-loop filling.

## v0.11.0 build prepared

- Version: **0.11.0**
- versionCode: **23**
- Package: `PhotogrammetryStudioAndroid-v0.11.0-Combined-Diagnostics-Safe-Hole-Fill-Android-Studio-Ready.zip`
- Package SHA-256: `392f033753fa059a2b64d1d57a5cdbce8e9901427abab77911679bb0cd2f2ba9`
- Source manifest SHA-256: `c28c9d7370536773dfc5979e698c175020fd47a4676207c05fccc873bab46ab0`
- v0.10.0 -> v0.11.0 code patch SHA-256: `0ae8b78e5a4010d98fd3801f7b8d6157d91a9451def81f394cb82d97c79c3570`
- ZIP integrity test: **passed**
- Gradle wrapper JAR included: **yes**
- Android Studio/device compile: **pending**

### Combined Project Diagnostics

A new project card exports a single text file containing:
- Quick Check when available,
- full Stage 1 feature-match diagnostics,
- full Stage 2 sparse diagnostics,
- full Stage 3 multi-view camera/pair diagnostics,
- full Stage 4 bundle diagnostics,
- full Stage 5 dense-pair diagnostics,
- full Stage 6 fusion diagnostics,
- full Stage 7 mesh diagnostics.

The export is rebuilt from current saved project state each time, so new/rebuilt stage reports automatically appear. Raw PLY/OBJ geometry remains separate only when actual geometry inspection is needed.

### Stage 7 safe closed-loop fill

Only simple closed boundary loops are considered. Conservative size, planarity, edge-length, aspect, degeneracy, and mobile-cap checks must all pass. Open/branched boundary groups are never filled automatically.

Actual Kotlin validation using the real uploaded Stage 6 fused clouds:
- **test4:** 40,922 source points -> 5,173 vertices / 12,876 faces / 0 non-manifold edges. 1 closed loop before fill, **1 filled**, 0 remaining, +1 center vertex, +4 faces.
- **test3:** 41,017 source points -> 2,144 vertices / 4,938 faces / 0 non-manifold edges. 1 closed loop before fill, **0 filled**, 1 remaining. It was intentionally skipped because a centroid fan would create an overly long/thin/degenerate triangle.

The test3 skip is a safety success, not a fill failure.

### Next device test

1. Export **Combined Project Diagnostics** on test3 and verify it contains all available Stage 1–7 sections.
2. Rebuild Stage 7 on test3; verify non-manifold=0 and unsafe closed-loop skip reason is reported if it matches local validation.
3. Rebuild Stage 7 on test4; verify non-manifold=0 and the safe loop is filled if it matches local validation.
4. Export Combined Project Diagnostics again and verify the newest Stage 7 statistics are already included.
5. Export the v0.11.0 version-test report.

After v0.11.0 passes: begin texture-preparation work (camera/image selection, UV strategy, and first texture projection groundwork).
