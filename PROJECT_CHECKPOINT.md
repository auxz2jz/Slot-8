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


## v0.11.0 device result — PASSED

- Version guide: **7 Works / 0 Problems / 0 Untested**
- Device: Samsung SM-S908U1 / Android 16
- test4 Stage 7: **5,173 vertices / 12,876 faces / 0 non-manifold edges**, safe closed-loop fill **1/1**
- test3 Stage 7: **2,144 vertices / 4,938 faces / 0 non-manifold edges**, unsafe loop intentionally left open
- Combined Project Diagnostics successfully consolidated Quick Check + detailed stage reports into one routine text upload.

## v0.12.0 build prepared

- Version: **0.12.0**
- versionCode: **24**
- Package: `PhotogrammetryStudioAndroid-v0.12.0-Stage8-Photo-Color-Projection-Android-Studio-Ready.zip`
- Package SHA-256: `a43dc5619dc90a82a5fb578e00fd45c26cb9a3dfd6c0aa19d0e2e584b377e630`
- Source manifest SHA-256: `5d2a778f055488b6c5e52f9ef14f242e97d22303cf2052162cc60774b02e6158`
- v0.11.0 -> v0.12.0 patch SHA-256: `3aefe6c24106a206ef1309ac79fd53d70733bda997df0c07674136479098b79b`
- ZIP integrity test: **passed**
- Gradle wrapper JAR included: **yes**
- Android Studio/device compile: **pending**

### Stage 8 — Photo Color Projection

1. Rebuild connected camera orientations from original photos.
2. Reuse Stage 4 refined camera centers and Stage 7 topology-clean geometry.
3. Project Stage 7 vertices into EXIF-normalized source photos.
4. Reject negative-depth, out-of-image and strongly back-facing samples.
5. Rank views by surface facing, image-center position and camera distance.
6. Blend up to three strong source-photo samples per mesh vertex.
7. Persist vertex colors, coverage and per-camera contribution diagnostics.
8. Add a colored mesh preview.
9. Export colored PLY with normals + RGB + faces.
10. Export vertex-colored OBJ.
11. Add Stage 8 automatically to Combined Project Diagnostics.

Stage 8 is appearance-only and must not alter Stage 7 vertex/face counts.

### Known v0.12.0 limitations

- Camera intrinsics are still approximate.
- Lens distortion is still assumed to be zero.
- Full triangle-rasterized visibility/occlusion is not implemented yet.
- This is per-vertex photo color, not yet a UV texture atlas.

### Exact next device test

1. Install v0.12.0 and open **test4**.
2. On **Stage 7 — Surface Mesh**, tap **Next: Stage 8 — Project photo colors**.
3. Confirm the new Stage 8 card reports non-zero recovered camera poses, cameras used and colored vertices.
4. Tap **View Stage 8 photo colors** and inspect several angles.
5. Export **colored PLY**.
6. Repeat Stage 8 on **test3**.
7. Export **Combined Project Diagnostics** after Stage 8; it must include a Stage 8 section automatically.
8. Export the v0.12.0 version-test report.

Routine upload after v0.12.0: version-test report + one combined diagnostic TXT per tested project. Colored PLY is only needed when visual/color geometry inspection is requested.


## v0.12.0 device result — PASSED

- Version guide: **8 Works / 0 Problems / 0 Untested**
- Device: Samsung SM-S908U1 / Android 16
- test4 Stage 8: 5,173 vertices / 12,876 faces; 72/100 connected camera poses recovered; 65 cameras used; 2,880 colored vertices; **55.67% color coverage**.
- test3 Stage 8: 2,144 vertices / 4,938 faces; 41/41 connected camera poses recovered; 35 cameras used; 999 colored vertices; **46.60% color coverage**.
- Stage 8 colored PLY/OBJ exports preserved Stage 7 topology.
- Combined Project Diagnostics correctly extended through Stage 8.

See `TEST_OUTPUT_MANIFEST_v0.12.0.sha256` for the exact uploaded result set.

## v0.13.0 build prepared

- Version: **0.13.0**
- versionCode: **25**
- Package: `PhotogrammetryStudioAndroid-v0.13.0-Stage9-Texture-Source-Assignment-Android-Studio-Ready.zip`
- Package SHA-256: `fd8265a4156b548999dc1fcd66c9c914eb5b636c0427abb323d9e3ee9fa680b5`
- Source-manifest SHA-256: `d4138babad53f12028d207c0328d2e5da60fc71395f17b0f5c37b009af008dad`
- v0.12.0 -> v0.13.0 patch SHA-256: `adbacd3df69fe996f98f41b0fe4f01228ba7a3b800de07f517ad3350f479441e`
- ZIP integrity test: **passed**
- Gradle wrapper JAR included: **yes**
- Source parser/redeclaration checks: **passed**
- Full Gradle compile here: blocked because `services.gradle.org` cannot be resolved; Android Studio/device build remains authoritative.

### Stage 8 robustness upgrade

- Short-gap pose bridging can recover across a failed adjacent orientation link using an older recovered connected camera, up to four positions back.
- Adjacent recovered links and bridged links are reported separately.
- A coarse image-space depth map rejects projected vertices that sit substantially behind the nearest projected mesh surface before color sampling.
- Visibility-rejected candidate counts are persisted and included in Combined Project Diagnostics.

### Stage 9 — Texture Source Assignment

- Reuses the robust recovered camera set and visibility test.
- Tests every Stage 7 face against usable source cameras.
- Requires all three triangle corners to project inside the image and pass visibility.
- Rejects effectively sub-pixel projected faces.
- Chooses one best source photo per assigned triangle.
- Stores normalized source-photo coordinates for all three face corners.
- Reports assigned/unassigned faces, triangle source coverage, camera usage, recovered/bridged pose links and per-camera face contribution counts.
- Exports a triangle source-map TSV.
- Combined Project Diagnostics now includes Quick Check + Stages 1–9.

Stage 9 does not yet build a UV atlas image. It is the source-view validation/preparation step immediately before atlas packing and rasterization.

### Next device test

1. Rebuild Stage 8 on test4 and inspect recovered/bridged poses plus visibility-rejected candidates.
2. Tap **Next: Stage 9 — Assign triangle texture sources**.
3. Confirm Stage 9 assigns non-zero faces, uses multiple cameras and reports triangle source coverage.
4. Repeat Stage 8 + Stage 9 on test3.
5. Export Combined Project Diagnostics for test3 and test4; both must contain Stage 9.
6. Export the v0.13.0 version-test report.

Routine upload: version-test report + one Combined Project Diagnostics TXT per tested project. The Stage 9 source-map TSV is only needed if assignment coverage/behavior needs deeper inspection.

Next after a healthy Stage 9 result: real UV atlas generation and UV-mapped model export.


## v0.13.0 device result — PASSED

- Version guide: **8 Works / 0 Problems / 0 Untested**
- Device: Samsung SM-S908U1 / Android 16
- test4 Stage 8 recovered **100/100 connected camera poses** after gap bridging.
- test4 Stage 9 assigned **3,768 / 12,876 triangles (29.3%)** using 90 cameras and was marked ready for texture-atlas work.
- test3 Stage 9 recovered 41/41 camera poses and assigned **898 / 4,938 triangles (18.19%)** using 39 cameras.
- test3 remains intentionally below the atlas-ready threshold; unassigned faces must stay untextured rather than receiving invented photo data.

## v0.14.0 build prepared

- Version: **0.14.0**
- versionCode: **26**
- Package: `PhotogrammetryStudioAndroid-v0.14.0-Stage10-UV-Texture-Atlas-Android-Studio-Ready.zip`
- Package SHA-256: `91f069323d1f50de94519d166e41e387050d438ccb912bdff62a42249161238c`
- Source-manifest file SHA-256: `2b233f5b0ddae8e06df72d1991f67a2826c3d73e2da8f0b8781953869ed8e9dd`
- v0.13.0 -> v0.14.0 patch SHA-256: `abe0cec7e29445099cc7fd192a53dabf3bf902b563373d6a1eda940be3190484`
- ZIP integrity test: **passed**
- Gradle wrapper JAR included: **yes**
- Core Kotlin model/diagnostic compile: **passed**
- Full Gradle build here: blocked by unresolved Gradle distribution host; Android Studio/device build remains authoritative.

### Stage 10 — UV Texture Atlas

- consumes Stage 9's real source-photo assignments and normalized source-image triangle coordinates;
- packs one padded atlas tile per assigned triangle;
- barycentrically rasterizes the real source-photo patch into a PNG atlas;
- writes matching OBJ UV coordinates;
- keeps unassigned Stage 7 faces in the model under a gray untextured material;
- does not change Stage 7 vertices/faces;
- exports one ZIP containing `textured_model.obj`, `texture_atlas.mtl`, and `texture_atlas.png`;
- allows partial atlas export for low-coverage projects such as test3 without fabricating missing texture;
- extends Combined Project Diagnostics through Stage 10.

### Exact next device test

1. On test4 Stage 9 tap **Next: Stage 10 — Build UV texture atlas**.
2. Confirm non-zero textured faces, atlas size, source photos used, and Ready for export=true.
3. Export **textured model package (.zip)** and confirm it contains OBJ + MTL + PNG.
4. Export/open the atlas PNG and confirm it contains real photo-derived triangle patches.
5. Run Stage 10 on test3 as a partial-atlas regression.
6. Confirm Stage 7 geometry counts remain unchanged.
7. Export Combined Project Diagnostics and the v0.14.0 version-test report.

Next after v0.14 device validation: verify texture orientation/viewer compatibility, then improve UV-island packing, seam/exposure blending and Stage 9 source coverage.


## v0.14.0 device result — PASSED

- Version guide: **8 Works / 0 Problems / 0 Untested**.
- test4 Stage 10: 2048×2048 atlas; **3,768 / 12,876 textured faces (29.26%)**; 90 source photos; 0 missing-photo/rasterization skips; readyForExport=true.
- test3 Stage 10: 2048×2048 atlas; **898 / 4,938 textured faces (18.19%)**; 39 source photos; 0 missing-photo/rasterization skips; readyForExport=true.
- Both textured packages contained `textured_model.obj`, `texture_atlas.mtl`, and `texture_atlas.png`.
- The v0.14 PNG by itself looks like a scrambled mosaic because each textured triangle had its own independent tile; OBJ UVs assemble those tiles on the 3D model.

## v0.15.0 build prepared

- Version: **0.15.0**
- versionCode: **27**
- Package: `PhotogrammetryStudioAndroid-v0.15.0-UV-Islands-Textured-Preview-Android-Studio-Ready.zip`
- Package SHA-256: `3207682ee1202e53fd90c9eeb14edaa8386d31acb0a811235c030e6bc3544cb4`
- Source-manifest file SHA-256: `d2a1cbc9b3cd95644d16fa0180dca81073e36c1580c0fd12b90c274a0ce30e49`
- v0.14.0 -> v0.15.0 patch SHA-256: `50044e99d5134b33a70033f758c9193e8b8318bf45f148e0312df3451f2e954c`
- ZIP integrity test: **passed**
- Gradle wrapper JAR included: **yes**
- Core Kotlin/model/diagnostic compile: **passed**
- TextureAtlasBuilder Android-stub compile: **passed**
- Synthetic two-triangle same-photo test: **1 UV island / 2 textured faces**
- Full Gradle build here: blocked because `services.gradle.org` cannot be resolved; Android Studio/device remains authoritative.

### v0.15.0 changes

1. Edge-connected Stage 9 faces that use the same source photo are grouped into a shared UV island.
2. Each island preserves the member triangles' relative coordinates inside their source-photo region.
3. Variable rectangular islands are shelf-packed into a 2048 or 4096 atlas as needed.
4. Stage 10 now reports UV island count, largest island face count, and atlas occupancy.
5. New **View textured model in app** button applies the saved atlas PNG and actual Stage 10 UVs to a rotatable mesh preview.
6. Unassigned faces remain gray in preview and export.
7. Standard OBJ + MTL + PNG export stays unchanged.
8. Stage 7 geometry remains unchanged.

### Exact v0.15.0 device test

1. Rebuild Stage 10 on test4 and confirm UV islands > 0 plus Ready for export=true.
2. Tap **View textured model in app** and inspect all six fixed views plus rotation/zoom.
3. Export the atlas PNG; it should contain larger photo regions/islands instead of thousands of isolated one-triangle tiles.
4. Export the textured ZIP and optionally open the OBJ in MeshLab.
5. Repeat Stage 10 on test3 as a partial-texture regression.
6. Confirm Stage 7 vertex/face counts are unchanged.
7. Export Combined Project Diagnostics and the v0.15.0 version-test report.

Next after v0.15 passes: improve Stage 9 coverage and add seam/exposure blending across neighboring islands.


## v0.15.0 device result — PASSED

- Guide: **8 Works / 0 Problems / 0 Untested** on Samsung SM-S908U1 / Android 16.
- test4 Stage 10: 4096×4096 atlas, **1,503 UV islands**, largest island **81 faces**, **13.60% occupancy**, 3,768/12,876 textured faces (**29.26%**), 90 source photos, 0 missing/mapping skips, readyExport=true.
- test3 Stage 10: 2048×2048 atlas, **365 UV islands**, largest island **52 faces**, **13.06% occupancy**, 898/4,938 textured faces (**18.19%**), readyExport=true.
- In-app textured preview, standard OBJ/MTL/PNG export, project isolation, and Stage 7 geometry preservation all passed.
- The test4 atlas visually shows larger grouped source-photo regions, confirming the v0.15 UV-island change.

## v0.16.0 build prepared

- Version: **0.16.0**
- versionCode: **28**
- Package: `PhotogrammetryStudioAndroid-v0.16.0-Texture-Coverage-Exposure-Android-Studio-Ready.zip`
- Package SHA-256: `bfb575d5dde6d18916a3bddacac46699bc77cdc8f1d3fff7a2bcd12392fa263b`
- Source-manifest SHA-256: `2e7b7b6efa123fd96a198530154f5acb56dce60b224c7d13b26591b96867f11b`
- v0.15.0 -> v0.16.0 patch SHA-256: `4861a56566d47a486f86bdb19b3b3345f81366ae88a0dca1dc3fe8a14835a0a1`
- ZIP integrity test: **passed**
- Gradle wrapper JAR included: **yes**
- Core Kotlin/model/diagnostic compile: **passed**
- TextureAtlasBuilder Android-stub compile: **passed**
- Full Gradle build here remains blocked because the recovery environment cannot resolve the Gradle distribution host; Android Studio/device remains authoritative.

### v0.16.0 appearance changes

1. Strict Stage 9 three-vertex visibility assignments remain first choice.
2. A second conservative candidate pool may recover only a previously unassigned face.
3. Fallback still requires all three corners to have positive-depth, in-image, front-facing geometric projections.
4. At least two of the three corners must pass the coarse depth visibility check.
5. Fallback also requires a minimum projected area and mean source-view score.
6. Stage 9 diagnostics report how many assignments came from the fallback.
7. Stage 10 estimates mean luminance for each used source photo, chooses the median used-photo luminance as target, and applies a clamped 0.82..1.22 gain.
8. Exposure normalization is luminance-only; no aggressive per-channel color correction is performed.
9. v0.15 connected UV islands, in-app textured preview, and Stage 7 geometry remain unchanged.

### v0.16.0 device baselines

- test4 Stage 9/10 coverage must be **>= 29.26%**.
- test3 Stage 9/10 coverage must be **>= 18.19%**.
- Texture alignment must remain correct in the in-app preview.
- Brightness stepping between source-photo regions should be no worse than v0.15 and ideally reduced.


## v0.16.0 device-result upload checkpoint — analysis intentionally paused

User explicitly stopped the analysis loop on 2026-09-23 and requested that all progress be saved before any further work.

Received v0.16.0 artifacts (9 total):
1. 3D_Scan_Studio_v0.16.0_test_report.txt
2. test4_textured_model_package.zip
3. test4_stage10_texture_atlas_report.txt
4. test4_combined_project_diagnostics.txt
5. test3_textured_model_package.zip
6. test3_stage10_texture_atlas_report.txt
7. test3_combined_project_diagnostics.txt
8. test4_texture_atlas.png
9. test3_texture_atlas.png

Confirmed from the uploaded reports before stopping:
- v0.16.0 guide: **8 Works / 0 Problems / 0 Untested**
- test4 Stage 9/10: **3810 / 12876 faces = 29.59% coverage**
- test4 Stage 10: 4096×4096 atlas, 1535 UV islands, largest island 81 faces, 13.79% occupancy, 90 source photos, 0 missing-photo skips, 0 mapping skips
- test4 exposure normalization adjusted 41/90 source photos by at least 3%
- test3 Stage 9/10: **927 / 4938 faces = 18.77% coverage**
- test3 Stage 10: 2048×2048 atlas, 384 UV islands, largest island 52 faces, 13.52% occupancy, 39 source photos, 0 missing-photo skips, 0 mapping skips
- test3 exposure normalization adjusted 16/39 source photos by at least 3%

The visible results therefore show a small non-regressing coverage increase over v0.15:
- test4: 29.26% -> 29.59%
- test3: 18.19% -> 18.77%

IMPORTANT:
- No v0.17.0 source changes have been started.
- No further interpretation should be assumed beyond the confirmed values above.
- Next action, when explicitly resumed by the user, is to do one concise v0.16.0 review from these saved artifacts and then decide the next build once.
- Do not re-request these nine artifacts unless a file is actually unavailable.


## v0.16.0 concise device review — PASSED

- Guide: **8 Works / 0 Problems / 0 Untested**
- test4: 3810/12876 textured faces = **29.59%**, up from 29.26%.
- test4 v0.16 fallback recovered **42** faces from 20,683 relaxed candidates.
- test4 Stage 10: 1535 UV islands, largest 81 faces, 13.79% occupancy, 90 source photos, 0 missing/mapping failures; exposure normalization adjusted 41/90 photos.
- test3: 927/4938 textured faces = **18.77%**, up from 18.19%.
- test3 v0.16 fallback recovered **29** faces from 672 relaxed candidates.
- test3 Stage 10: 384 UV islands, largest 52 faces, 13.52% occupancy, 39 source photos, 0 missing/mapping failures; exposure normalization adjusted 16/39 photos.
- User noted both coverage gains were only slight. v0.17 therefore does not broadly loosen visibility again.

## v0.17.0 build prepared

- Version: **0.17.0**
- versionCode: **29**
- Package: `PhotogrammetryStudioAndroid-v0.17.0-Neighbor-Texture-Recovery-Android-Studio-Ready.zip`
- Package SHA-256: `f55782bfd4906bb0c42d640fee02666d85bd12b4be18350f546b66c0a9a3b728`
- Source-manifest SHA-256: `f437a1438afb6a921870bad1b60f7c7d7761a5b91750fa2281720e0c92a1b188`
- v0.16.0 -> v0.17.0 patch SHA-256: `011dfffbf0773990f3bd617055d9532544e52b7a5456b81714bf661ff8826c97`
- ZIP integrity test: **passed**
- Gradle wrapper JAR included: **yes**
- Changed Kotlin files passed delimiter/parser sanity checks; full Android Studio/device compile remains authoritative.

### v0.17 Stage 9 recovery order

1. Strict three-vertex visibility assignment.
2. v0.16 conservative two-of-three visibility fallback.
3. v0.17 neighbor-consistency recovery for still-unassigned faces only.

Neighbor recovery requires:
- at least two edge-neighbor faces already assigned to the same source photo;
- only pre-existing strict/v0.16 assignments may vote;
- recovered v0.17 faces never vote for additional faces;
- the target face still projects positive-depth, in-image and front-facing in that camera;
- projected area and mean source-view score pass conservative thresholds;
- at least one target vertex passes the coarse depth-visibility check.

### Capture guidance

The project Capture guidance card now includes:
- about 70–80% overlap,
- same lens/zoom,
- small steps,
- low/middle/high rings,
- steady/even lighting and sharpness,
- reflection avoidance,
- temporary visual texture/markers for plain objects.

### v0.17 device baselines

- test4 coverage must be **>= 29.59%**.
- test3 coverage must be **>= 18.77%**.
- Stage 7 geometry must remain unchanged.
- Texture alignment must remain usable.
- Combined diagnostics must report neighbor-consistency candidate/assignment counts.

If v0.17 still produces only a small coverage gain, the next priority is camera calibration/intrinsics and graph-based camera/source recovery rather than looser visibility rules.


## v0.17.0 device result — PASSED / recovery experiment plateau

- Guide: **8 Works / 0 Problems / 0 Untested**
- test4 Stage 9: 3810/12876 = **29.59%**, unchanged from v0.16
- test4 Stage 10: 1535 UV islands, largest 81 faces, 13.79% occupancy, 90 photos, 0 missing/mapping failures
- test3 Stage 9: 927/4938 = **18.77%**, unchanged from v0.16
- test3 Stage 10: 384 UV islands, largest 52 faces, 13.52% occupancy, 39 photos, 0 missing/mapping failures
- Neighbor-consistency recovery did not produce a measurable coverage increase on these test sets.
- Decision: stop loosening/stacking texture-recovery heuristics here.

### Next accuracy priority — camera calibration

The current pipeline still derives intrinsics approximately from EXIF focal information and assumes zero lens distortion. The next substantial accuracy milestone should therefore be a guided camera-calibration workflow and use of calibrated intrinsics/distortion in reconstruction and texture projection.

Preferred direction:
1. OpenCV ChArUco or checkerboard calibration target.
2. Guided capture of calibration images using the exact lens/zoom/resolution intended for scanning.
3. Solve and persist fx, fy, cx, cy plus radial/tangential distortion coefficients.
4. Store calibration as a camera/lens/resolution profile.
5. Use that profile in Stages 2–10 instead of approximate EXIF intrinsics / zero distortion.
6. Keep calibration distinct from object-scale markers; absolute object scale still needs a known-size reference in the scan scene or known rig geometry.
