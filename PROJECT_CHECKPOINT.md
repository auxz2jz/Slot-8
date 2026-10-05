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


## v0.18.0 build prepared — camera calibration milestone

- Version: **0.18.0**
- versionCode: **30**
- Package: `PhotogrammetryStudioAndroid-v0.18.0-Camera-Calibration-Android-Studio-Ready.zip`
- Package SHA-256: `d5064ce8d2f08b28c0403dd0c680ccc393256c2bbea5a1471b4fba0f97768c7d`
- Local source-manifest file SHA-256: `ad8a95757efe7526e81026a737e7d8fc0f0889d2ec7f8c254ff7b0af0d89fa99`
- v0.17 -> v0.18 patch SHA-256: `a72bb65f2757d0e11fdaa8df5bc588b982ffaf31ccd3af32877a5dea8972134f`
- ZIP integrity test: **passed**
- Gradle wrapper JAR included: **yes**
- Models.kt standalone Kotlin compile: **passed**
- Changed-file brace/syntax sanity checks: **passed**
- Full Android Gradle build here: blocked because services.gradle.org cannot be resolved; Android Studio/device remains authoritative.

v0.18 changes:
1. New Camera Calibration screen from the project list.
2. Built-in printable US Letter landscape checkerboard PDF: 9×6 inner corners, 25 mm squares.
3. Import dedicated calibration photos separately from scan projects.
4. OpenCV checkerboard detection + sub-pixel corner refinement.
5. OpenCV calibrateCamera solve for fx/fy/cx/cy and k1/k2/p1/p2/k3.
6. Minimum 10 accepted frames; 15–25 recommended.
7. Profile activation requires RMS reprojection error <= 1.5 px.
8. Calibration report export lists accepted/rejected frames and solved parameters.
9. Existing reconstruction camera-matrix helpers use calibrated focal/principal-point values when image dimensions are compatible.
10. Distortion coefficients are persisted but full raster/feature undistortion is deliberately deferred until the profile solve is validated on-device.
11. Marker roadmap is explicitly separated:
   - reflective/non-coded dots = extra visual features;
   - coded fiducials/tape = identifiable tracking/pose anchors;
   - known-size reference = future metric scale.

Exact next test:
- export and print target at 100% Actual Size;
- take/import 15–25 sharp board photos with one fixed lens/zoom/orientation;
- run calibration and export its report;
- prefer RMS <1.0 px; <=1.5 px accepted for this milestone;
- rebuild Stage 2 and confirm Intrinsics source begins with Calibrated;
- optionally rebuild test4 Stage 8/9 to check for no major regression;
- export v0.18 version-test report.


## v0.19.0 build prepared — laser-line foundation while physical camera calibration is paused

- Version: **0.19.0**
- versionCode: **31**
- Package: `PhotogrammetryStudioAndroid-v0.19.0-Laser-Line-Foundation-Android-Studio-Ready.zip`
- Package SHA-256: `7b8f91099b4874331e73075bf2473f8c10650454ee788c57237f83a49ccfde84`
- Source-manifest file SHA-256: `a4f6fe2c38c6ad9f23a78c46341ee5ff83184f2bd37939bb9a3747ad6cd3f4ed`
- ZIP integrity test: **passed**
- Gradle wrapper JAR included: **yes**
- Models.kt standalone compile: **passed**
- Changed Kotlin delimiter/syntax sanity checks: **passed**
- Full Gradle build here remains blocked by unresolved services.gradle.org; Android Studio/device build is authoritative.

Camera calibration status:
- v0.18 remains intact.
- User plans proper printed-board calibration after the 1st.
- Temporary screen-displayed calibration must not be treated as the final accuracy baseline.
- Do not tune Stage 2–7 geometry around temporary calibration results.

v0.19 Laser Line Lab:
1. Laser and Hybrid projects can open a dedicated Laser Line Lab.
2. Import one matched laser-OFF frame and one laser-ON frame.
3. Select red/green/blue laser and adjustable signal threshold.
4. OFF/ON channel-difference suppresses static background.
5. Extract weighted sub-pixel stripe center per image row.
6. Reject very broad bright regions.
7. Report stripe-row coverage, continuity, mean signal, mean width and X range.
8. Save/export cyan detected-line preview PNG and text report.
9. Laser files/results remain scoped to the owning project.
10. No metric 3D triangulation is claimed yet.

Open-source research references:
- LibreScanner Horus/Ciclop + horus-fw — GPLv2 / CC BY-SA mechanical files.
- hairu/FreeLSS — GPL-3.0.
- mariolukas/FabScanPi-Server — project README states GPLv2.
- songyuncen/laser-triangulation — MIT; direct sheet-of-light module split for camera, laser, movement calibration, extraction, reconstruction.
- Sardau/Sardauscan — low-cost modular multi-laser scanner reference.

Licensing rule: GPL implementations are workflow/algorithm references unless project licensing is intentionally made compatible. Prefer independent implementation and permissive references for reusable code.

Next laser milestones:
- laser-plane calibration;
- turntable axis/angle calibration;
- camera-ray / laser-plane triangulation;
- multi-angle point-cloud fusion;
- later ESP32/Arduino hardware control;
- later Hybrid laser/photo alignment.


## Laser hardware scope clarified — manual DAVID-style first

For the first real laser-line scanner milestone, do **not** assume a turntable or Arduino/ESP32 controller.

Current intended physical setup:
- object stationary on a table;
- phone/camera fixed in place;
- DAVID-style 90° calibration corner/backdrop behind the object;
- printed calibration markers/pattern on the corner;
- hand-held line laser swept manually up/down or across the object;
- laser approximately off-axis from the camera; exact laser pose is not treated as a permanently fixed 45° mechanical requirement;
- software detects the moving laser stripe frame-by-frame and later derives 3D from calibrated camera + calibration geometry / laser plane.

Turntable, automated laser motion, and Arduino/ESP32 control are later milestones only.

This manual setup should be treated as the primary first hardware target for Laser Scan mode.


## v0.19.0 compile-fix r1 — Android Studio build failure resolved

User Android Studio build of the first v0.19.0 package reached `:app:compileDebugKotlin` and failed at:
`LaserLineLabScreen.kt:86:13` — Material3 API is experimental.

Root cause:
- New Laser Line Lab used Material3 `TopAppBar`.
- Unlike the existing Projects/Project/Calibration screens, it did not import `ExperimentalMaterial3Api` or opt in.

Fix:
- Added `import androidx.compose.material3.ExperimentalMaterial3Api`.
- Added `@OptIn(ExperimentalMaterial3Api::class)` to `LaserLineLabScreen`.
- No functional or algorithmic changes.
- Keep versionName **0.19.0** / versionCode **31** because the original v0.19.0 package never compiled into a testable APK.

Replacement package:
`PhotogrammetryStudioAndroid-v0.19.0-r1-Laser-Line-Compile-Fix-Android-Studio-Ready.zip`
SHA-256:
`04d0f5312db10acd9a96a5dde87f59a413024454476cafd032aa2f175862a0d6`

Verification:
- Exact source diff contains only the Material3 import + OptIn annotation.
- ZIP integrity test passed.
- Assistant Gradle compile still cannot run because services.gradle.org cannot resolve; Android Studio compile remains authoritative.


## v0.20.0 prepared — selectable calibration print sizes

Version: **0.20.0**  
versionCode: **32**

Replacement Android Studio package:
`PhotogrammetryStudioAndroid-v0.20.0-Multi-Calibration-Targets-Android-Studio-Ready.zip`

Package SHA-256:
`9f74ce9955f189af29ba7c011714cd840911ae52a98262d084555a05ecf99a58`

Source manifest SHA-256:
`9ac0e6f94b5e09ac6a351dfc10418a140a5a68e9b9890bdcf8e7296bd2647177`

v0.19.0-r1 -> v0.20.0 text patch SHA-256:
`abbbdad678a9a8ab6385ac5bd6266b426ff0ecd617ff0b11f75467fdebae0f33`

Implemented:
- Photo calibration target selector: US Letter 8.5×11 or 12×18.
- Both photo sizes deliberately use the same 9×6 inner-corner / exact 25.0 mm checkerboard geometry.
- Selected photo target persists and is recorded into newly solved camera-calibration profiles/reports.
- Older saved calibration profiles remain loadable with backward-compatible target metadata.
- Laser background selector: US Letter or 12×18.
- Each laser preset provides separate LEFT and RIGHT printable PDFs for the 90° DAVID-style corner.
- Laser target choice persists and is included in laser diagnostics.
- Photo, laser LEFT/RIGHT, and print-shop instruction PDFs are exportable from inside the app.
- Corrected PDFs are bundled as app assets and copied into `CALIBRATION_PRINT_PACK/`.
- v0.20 guided testing covers target selection persistence, PDF exports, profile regression and report export.

Important boundaries:
- Proper physical camera calibration remains pending the printed target.
- Do not tune Stages 2–7 around the temporary monitor-displayed calibration.
- v0.20 does not yet decode laser-background markers or perform laser-plane/3D triangulation.
- Existing v0.19 OFF/ON laser stripe extraction is preserved.

Verification:
- ZIP integrity passed; Gradle wrapper JAR present.
- Standalone Models.kt Kotlin compile passed.
- Calibration asset paths and embedded/public PDF hashes match.
- PDFs were rendered and visually checked after correcting a print-text color issue.
- Full Gradle Android compilation is still unavailable in the assistant environment because services.gradle.org cannot resolve/connect. Android Studio remains authoritative.

Next device test:
1. Verify Letter ↔ 12×18 photo target selection persists.
2. Export both photo PDFs.
3. Verify Letter ↔ 12×18 laser selection persists.
4. Export matching LEFT and RIGHT laser PDFs plus print instructions.
5. Confirm an existing calibration profile still loads; physical recalibration is not required for this software regression.
6. Export the v0.20 test report.


## Pre-v0.21.0 checkpoint — standard in-store print sizes

Baseline preserved before this change:
- Latest packaged candidate: **v0.20.0 / versionCode 32**
- Artifact: `PhotogrammetryStudioAndroid-v0.20.0-Multi-Calibration-Targets-Android-Studio-Ready.zip`
- SHA-256: `9f74ce9955f189af29ba7c011714cd840911ae52a98262d084555a05ecf99a58`
- Status: **CANDIDATE / not yet physically verified as a complete v0.20 release**
- v0.19.0-r1 compiled successfully in the user's Android Studio; physical laser and camera calibration testing remained pending.

New task:
- Add standard copy-print target sizes that Staples publicly lists for store/self-service/full-service copies: Letter 8.5x11, Legal 8.5x14, and Ledger 11x17.
- Preserve 12x18 as an optional poster preset for backward compatibility.
- Show exact page dimensions and calibration-pattern dimensions in the app.
- Add dedicated printable PDFs for every supported photo target and laser LEFT/RIGHT target size.
- Preserve existing calibration profiles and existing reconstruction/laser functionality.


## v0.21.0 candidate packaged — standard copy-size calibration targets

- Version: **0.21.0** / versionCode **33**
- Status: **CANDIDATE** pending Android Studio/device test
- Package: `PhotogrammetryStudioAndroid-v0.21.0-Standard-Print-Sizes-Android-Studio-Ready.zip`
- Package SHA-256: `19b40b62bce3796696cf4dc1daf2c2e1b0124096c481da4d634f388691a9e0ca`
- Calibration PDF pack: `3D_Scan_Studio_All_Calibration_PDFs_v0.21.zip`
- Calibration pack SHA-256: `016cf8ea3a16138c59fa60316ae02cf63be6b177e35839362a1fdb06584f16c0`
- v0.20 -> v0.21 text patch SHA-256: `8fd4329b3ccab7869a340f3255474089a0b850d61eeaa9672aa7479e371b9a80`
- v0.21 source-manifest file SHA-256: `64ef22a5fb3fb90809c16194df9e9c44d1211f865a9041397a5d22275930cd8c`
- ZIP integrity test: **passed**
- Gradle wrapper included: **yes**
- Models.kt standalone Kotlin compile: **passed**
- All 13 calibration PDFs rendered and visually inspected; page MediaBox sizes verified.

New target presets:
- Letter 8.5x11 — standard copy size
- Legal 8.5x14 — standard copy size
- Ledger/Tabloid 11x17 — standard copy size
- 12x18 — optional poster/backward-compatible

Photo calibration keeps one invariant geometry for all sizes: 9x6 inner corners, 25.0 mm squares, 250x175 mm checker pattern.

Exact next action:
Build/install v0.21.0 in Android Studio and run the 9-step in-app Test This Version guide. No physical calibration board is required for this software regression.


## v0.22.0 plan — laser analysis run history + cross-platform foundation

User evidence from the real v0.19 laser hardware test:
- Red laser, threshold 64
- 645/1000 detected rows
- 64.50% coverage
- 84.63% continuity
- 100.56 mean signal
- 4.80 px mean line width
- readyForTriangulation=true
- User confirmed they are actively changing threshold and rerunning Analyze.

Root diagnostic gap:
- Current app persists only the newest laser-line report/preview.
- Repeated threshold experiments overwrite the prior result, so there is no durable per-run history.

Planned Android candidate:
1. Preserve every Analyze run in a bounded per-project laser analysis history.
2. Record threshold/color, run ID/time, duration, input pair names, target preset, success/failure, metrics and readiness.
3. Persist in a platform-neutral JSONL format suitable for future Windows consumption.
4. Show recent runs in Laser Line Lab and keep the newest normal result/preview behavior.
5. Export the JSONL history.
6. Include run-history summary in human-readable laser diagnostics.
7. Add v0.22 guided tests proving multiple thresholds append rather than overwrite and survive reopen.
8. Do not change the laser extraction algorithm in this build; this is diagnostic instrumentation first.

Cross-platform requirement:
- 3D Scan Studio is one Android + future Windows product.
- Shared product intent/data formats belong in shared coordination docs.
- Android and Windows source, versions, tests, diagnostics, checkpoints, and verified baselines stay separate.
- Windows implementation status remains NOT STARTED until a Windows agent actually creates it.


## v0.22.0 candidate packaged — laser analysis history + cross-platform foundation

- Version: **0.22.0** / versionCode **34**
- Status: **CANDIDATE** pending Android Studio/device verification
- Package: `PhotogrammetryStudioAndroid-v0.22.0-Laser-Run-History-Cross-Platform-Android-Studio-Ready.zip`
- Package SHA-256: `2c6d3468f3d35e813de6b71290b86a49754344a71f13cc7956cee9d06d743a85`
- v0.21 -> v0.22 patch SHA-256: `94fdceeb48df8fbc7c552813e4757e1b62a01a8776ff84d7d480460e05356573`
- source-manifest file SHA-256: `f2cc88cf96267f07302da9f6b711b7d5d2923c7d0796c9e3b52464d3bb3c8c9e`
- ZIP integrity test: **passed**
- Gradle wrapper JAR present: **yes**
- Models.kt standalone Kotlin compile: **passed**
- Changed Kotlin delimiter/string/comment balance checks: **passed**
- Full Gradle build here remains blocked because the Gradle 9.6 distribution host cannot resolve/connect; Android Studio remains authoritative.

Implemented:
1. Every Laser Line Lab Analyze press creates a unique run and appends it to project history.
2. Moving the threshold slider alone does not create a run.
3. Success runs record threshold/color, UTC/epoch time, duration, input-pair metadata, selected laser-target preset, coverage, continuity, signal, stripe width, X range and readiness.
4. Failure runs record attempted settings, error type/message/full stack trace and duration.
5. History is bounded to newest 200 runs per project.
6. UI shows newest 10 runs and retained count.
7. Export `laser_analysis_history.jsonl` using shared schema v1.
8. Human laser report includes recent history summary.
9. Existing newest detailed report/preview behavior remains; historical preview PNGs are not retained in this build.
10. Laser extraction math is unchanged from the v0.19 foundation.

Cross-platform:
- Slot-8 now contains `shared/`, `android/`, and `windows/` coordination/status docs.
- Windows implementation is explicitly **NOT STARTED**.
- Shared laser-history JSONL schema is documented for future Windows consumption.
- Android and Windows keep separate source ownership, versions, tests, diagnostics, artifacts, checkpoints and verified baselines.

Exact next device test:
Install/build v0.22.0, open the real laser project, run threshold 32 once and threshold 64 twice, verify all three runs remain after reopening Laser Line Lab, export the JSONL history and laser report, then export the v0.22 version-test report.


## 2026-10-04 cross-platform ownership boundary established

User declared 3D Scan Studio one product with two separate implementations: Android and future Windows/PC.

Repository transition completed conservatively:
- Existing historical root remains Android-owned and unchanged in place.
- No Android source/history file was moved, renamed, refactored, or rewritten for the transition.
- Added root `CROSS_PLATFORM_OWNERSHIP.md`.
- `shared/` now contains product vision, feature catalog, requirements, decisions, data/interchange contracts, shared Stage 1-10 photogrammetry definitions, and calibration/export expectations.
- `android/` contains Android status/ownership/roadmap/testing/diagnostic pointers while root Android history remains authoritative.
- `windows/` is reserved for future PC/Codex ownership with NOT STARTED status, memory/roadmap/testing/diagnostic placeholders only; no Windows program source has been created.
- Android and Windows verified baselines remain independent.

Current Android candidate remains **v0.22.0 / versionCode 34**, unchanged by this repository-structure work.
Exact next Android action remains: build/install v0.22.0 and run its laser-analysis-history guided test.

## v0.23.0 candidate — tiled Letter calibration panels

- Version: **0.23.0** / versionCode **35**.
- Starting point: preserved v0.22.0 candidate; v0.22 laser extraction/history behavior is unchanged.
- Android Studio source package: `PhotogrammetryStudioAndroid-v0.23.0-Tiled-Letter-Calibration-Android-Studio-Ready.zip`
- Package SHA-256: `35770aa5eed29e3f46fcb7e9fba982d3a0cc249f6f55ce9d4ddc6924f9270d13`
- Calibration print pack: `3D_Scan_Studio_Budget_Calibration_Print_Pack.zip`
- Print-pack SHA-256: `416776d65fa9b4dbca88c89ccc8a646c9bb81d9120c46f6edad99dc6d044bb1c`
- New laser preset: **12×18 tiled (4 Letter sheets)**, separate LEFT/RIGHT 4-page PDFs.
- Physical laser geometry remains exactly the existing 12×18 / 35.0 mm-marker geometry.
- Tiled split: vertical 4.25 in from left; horizontal 9.75 in from top, chosen through clear regions rather than coded markers/reference dots.
- Tile pages carry crop/registration marks outside the calibration artwork; final assembly uses trimmed butt joints on a flat rigid board.
- Photo calibration geometry is unchanged. The existing Letter target remains one sheet: 9×6 inner corners / 25.0 mm squares / 250×175 mm checker pattern.
- Existing Letter, Legal, Ledger and single-sheet 12×18 presets remain available and saved IDs remain backward-compatible.
- Added tiled/poster-board assembly-instructions export.
- Off-device PDF verification: all new PDFs rendered successfully; software reassembly of both tiled panels matched the original 12×18 v0.22 artwork pixel-for-pixel at 120 dpi (mean/max difference 0).
- `Models.kt` standalone Kotlin compile: **PASSED**.
- Full Gradle compile attempt: **BLOCKED only by environment network resolution** while the wrapper attempted to download Gradle 9.6; Android Studio remains authoritative.
- Status: **CANDIDATE / not yet Android Studio or device verified**.

Exact next action:
1. Build/install v0.23.0 in Android Studio.
2. Run the 8-step in-app **Test This Version** guide.
3. Export tiled LEFT and RIGHT PDFs and assembly instructions from the app.
4. After software regression passes, print at 100% Actual Size, verify scale, trim/mount the eight laser pages, and mount the one-sheet photo checkerboard flat.
5. Keep v0.22.0 and earlier artifacts preserved until v0.23 is user-tested.
