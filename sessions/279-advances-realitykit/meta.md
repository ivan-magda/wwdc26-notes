# Session 279 — Explore advances in RealityKit

- **URL:** https://developer.apple.com/videos/play/wwdc2026/279/
- **Duration:** 24m
- **Speakers:** Dennis (software engineer, RealityKit team)

## Description

Explore the latest advancements in RealityKit, the framework for building 3D spatial
experiences across visionOS, iOS, iPadOS, macOS, and tvOS. This year's additions span
lighting and shadows (lightmaps, soft shadows, projective textures, physical space
lighting), a navigation mesh for character/NPC pathfinding, cloth simulation,
performance tools (mesh LOD, thermal-state monitoring), 3D Gaussian splat rendering,
and immersive audio with custom reverb meshes. Most features are demonstrated through a
new sample game, Chaparral Village.

## Key topics

- **Lighting & shadows** — lightmap textures (indirect lighting, ambient occlusion, beauty) baked in Reality Composer Pro 3; soft shadows for dynamic lights via `SpotLightComponent.Shadow` (`lightSize`, `quality`); projective textures (`SpotLightComponent.ProjectiveTexture`); physical space lighting (`SpotLightComponent.SurroundingsLight`) so virtual lights paint the real room via scene-understanding mesh — spotlights and point lights only.
- **Navigation mesh** — `NavigationMeshResource` (areas, flags, off-mesh connections), `NavigationComponent` (filter for cost/include/exclude), `NavigationController` (sync/async `computePath`); path nodes are `.meshPoint` or `.offMeshConnection` (e.g. ladders/bridges); traversal costs for slow regions.
- **Cloth simulation** — `ClothBodyComponent`, `ClothColliderComponent`, cloth simulation component holding shared materials; cloth as particle/spring mesh; pin vertices by setting them `.kinematic` via `vertices(in:center:)` selection.
- **Performance** — `LevelOfDetailComponent.addByCameraDistance` / `addByScreenArea`; thermal-state monitoring via `.thermalStateDidChange` notification → degrade LOD/shadow quality on `.serious`/`.critical`.
- **3D Gaussian splats** — `GaussianSplatResource.BufferResource` (position, scale, rotation, opacity, spherical harmonics + degree) → `GaussianSplatResource` → `GaussianSplatComponent`; no fixed file format, you supply buffers.
- **Immersive audio (Vision Pro)** — raytraced geometrical acoustics; `ReverbMeshResource` (`.shoebox(size:)` or from mesh), `.simulated(mesh:materials:)`, `ReverbComponent`; preset materials (`.dryWall`, `.carpet`) and custom `Audio.Material` via `Audio.Absorption` (10-band) / `Audio.Scattering`; immersive-space only — shared space falls back to system room-sense reverb.
- **Also shipping (named, not demoed):** coordinated multi-source audio, high-quality character rendering (subsurface scattering, hair shaders), portal customizations.

## Related sessions to fetch (referenced in this talk)

- [ ] Iterate your spatial scenes faster with Reality Composer Pro 3
- [ ] Supercharge your spatial workflows with Reality Composer Pro 3
- [ ] (Reality Composer Pro 3 intro sessions — named at 0:51 / 23:32 without titles)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Dennis, RealityKit team; RealityKit (since 2019) ships to visionOS/iOS/iPadOS/macOS/tvOS; Reality Composer Pro 3 is the new authoring tool. Agenda: lighting/shadows, navigation mesh, cloth, performance, Gaussian splats, immersive audio. Demo vehicle: Chaparral Village.
- **2:00 Lighting and shadows** — lightmaps (indirect/AO/beauty) baked in RCP 3; soft shadows (`lightSize`, `quality` medium/high; low = hard); projective textures (flashlight-through-film); physical space lighting via `SurroundingsLight` (spotlights + point lights only).
- **7:44 Navigation mesh** — traversable-path concept with costs and off-mesh connections; `NavigationMeshResource` → `NavigationComponent` → `NavigationController.computePath`; iterate `.meshPoint` / `.offMeshConnection` nodes.
- **11:01 Cloth simulation** — cloth as particle/spring mesh; `ClothBodyComponent`, `ClothColliderComponent`, simulation component + materials (stiffness, friction, solver, gravity, time step); pin curtains by setting selected vertices `.kinematic`.
- **13:42 Performance** — mesh LOD (cauldron LOD 0–5); `addByCameraDistance` / `addByScreenArea`; monitor `.thermalStateDidChange` and degrade gracefully on serious/critical.
- **17:09 3D Gaussian splats** — gaussians as opacity ellipsoids; supply position/scale/rotation/opacity/spherical-harmonics buffers + degree; `BufferResource` → `GaussianSplatResource` → `GaussianSplatComponent`; succulent sample on Vision Pro.
- **19:08 Immersive audio** — raytraced acoustics; custom reverb mesh models room geometry/materials; `ReverbMeshResource.shoebox`, `.simulated`, `ReverbComponent`; preset + custom `Audio.Material` (absorption/scattering); immersive-space only, else system room-sense reverb; museum band sample.
- **22:42 Next steps** — more features (coordinated multi-source audio, character rendering, portal customizations); download samples; check out Reality Composer Pro 3.

## Code

See `code.md` — 11 snippets extracted from the Code tab.
