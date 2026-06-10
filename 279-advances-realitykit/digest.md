---
title: "Explore advances in RealityKit — Full Digest"
session: WWDC26 · 279
url: https://developer.apple.com/videos/play/wwdc2026/279/
duration: 24m
speakers: Dennis (RealityKit team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 279 — Explore advances in RealityKit

## TL;DR

RealityKit's year-two-of-spatial grab bag. Six feature areas, mostly demoed through a
new sample game, **Chaparral Village** (a village shrunk into the player's room, with an
alchemy area to reach):

1. **Lighting & shadows** — baked **lightmaps** (indirect/AO/beauty) from Reality Composer Pro 3; **soft shadows** for dynamic lights (`lightSize` + `quality`); **projective textures** (flashlight-through-film); **physical space lighting** (`SurroundingsLight`) so virtual lights paint your real room.
2. **Navigation mesh** — proper game-style pathfinding: `NavigationMeshResource` → `NavigationComponent` → `NavigationController.computePath`, with traversal costs and off-mesh connections (ladders, bridges).
3. **Cloth simulation** — particle/spring cloth via `ClothBodyComponent` + `ClothColliderComponent`; pin vertices by marking them `.kinematic`.
4. **Performance** — mesh **LOD** (`addByCameraDistance` / `addByScreenArea`) and **thermal-state** monitoring to degrade gracefully.
5. **3D Gaussian splats** — render real-world captures by supplying position/scale/rotation/opacity/spherical-harmonics buffers into `GaussianSplatComponent`.
6. **Immersive audio (Vision Pro)** — raytraced acoustics with a **custom reverb mesh**: `ReverbMeshResource`, preset + custom `Audio.Material` (absorption/scattering).

Also name-dropped for this year but not demoed: coordinated multi-source audio,
high-quality character rendering (subsurface scattering + hair shaders), and portal
customizations. Companion tool throughout is **Reality Composer Pro 3**.

---

## 1. Lighting and shadows

### Lightmaps (static lighting)

The alchemy area looked dark in the corners because they weren't getting reflected
light. Solution: bake an **indirect-lighting lightmap** with Reality Composer Pro 3's
**light baker** and apply it. The corners brighten as they pick up bounced light.

RealityKit's API lets you attach your own lightmap textures for three channels —
**indirect lighting**, **ambient occlusion**, and **beauty** — but Apple recommends
baking them in RCP 3 rather than hand-authoring. Lightmaps only cover **static**
lighting (deep dive: *Iterate your spatial scenes faster with Reality Composer Pro 3*).

### Soft shadows (dynamic lights)

By default RealityKit shadows have hard edges — accurate only for an infinitesimally
small light. Real area lights produce a **penumbra** whose size scales with the light's
area. Soft shadows bring that to dynamic lights:

```swift
// Enable soft shadows for the hearth spotlight

guard var shadow = hearthSpotlight.components[SpotLightComponent.Shadow.self] else {
    // handle error
}
shadow.lightSize = 0.7 // meters  (diameter of the light; default 0 = hard shadow)

shadow.quality = .medium // or .high
// shadow.quality = .low // will result in hard shadows

hearthSpotlight.components.set(shadow)
```

- `lightSize` is the light's **diameter in meters**; larger = softer/larger penumbra.
- `quality` controls the **sample count**. `.high` looks best but costs more; `.medium`
  is the demo's choice. `.low` forces a **hard** shadow regardless of `lightSize` — to
  get soft shadows at all you must use `.medium` or `.high`.

### Projective textures

Analogy: shine a flashlight through a piece of film and the film's image appears on
whatever surface the beam hits. Uses: light through an intricate window, or animated
sea-floor caustics. In the planetarium demo, stars and nebulae are projected from
rotating spotlights.

```swift
// Create one of the planetarium spotlights

let spotLightEntity = Entity()
spotLightEntity.components.set(SpotLightComponent(
    color: .white,                 // white so it doesn't tint the projected texture
    intensity: intensity,          // larger rooms need more intensity to read on walls
    innerAngleInDegrees: innerAngle,
    outerAngleInDegrees: outerAngle,
    attenuationRadius: attenuationRadius,
))

let projectiveTexture: TextureResource = generateStarsAndNebulaeTexture()
spotLightEntity.components.set(SpotLightComponent.ProjectiveTexture(
    texture: projectiveTexture
))
```

### Physical space lighting

This is the part that makes virtual light land on your **real** room. It lets virtual
lights interact with system environments / the world around you using RealityKit's
**scene-understanding mesh**. One line:

```swift
// Enable physical space lighting

spotLightEntity.components.set(SpotLightComponent.SurroundingsLight())
```

- Currently supported for **spotlights and point lights only**.
- Also enabled in Chaparral Village's alchemy area.

## 2. Navigation mesh

Conceptual walkthrough: get from one side of a map to flags on the other; obstacles
(dense forests) appear; a **navigation mesh** defines the traversable area so RealityKit
can compute a path. Refinements:

- **Traversal cost** — instead of excluding the forest entirely, give it a higher cost
  so the path prefers faster terrain but can still cut through if worthwhile.
- **Off-mesh connections** — when a rift splits the scene into two disconnected meshes,
  bridge them with an off-mesh connection (a bridge here, ladders in the game).

The pipeline:

- **`NavigationMeshResource`** — geometric data: labeled areas, custom flags per area,
  connections between areas. Authored via the Swift API or in Reality Composer Pro 3
  (deep dive: *Supercharge your spatial workflows with Reality Composer Pro 3*).
- **`NavigationComponent`** — has a **filter** defining area costs and which areas to
  include/exclude by flag.
- **`NavigationController`** — computes the path, synchronously or asynchronously.

```swift
// Querying the navigation mesh in Chaparral Village

extension Entity {
    public func navigate(/* ... */) async {
        let navigator = try! NavigationController(entity: self)
        guard let result = await navigator.computePath(from: fromPosition, to: toPosition)
        else {
            return                       // nil → no valid path
        }
        if result.isEmpty {
            return                       // empty → already at destination
        }
        for node in result {
            switch node.category {
                case .meshPoint:
                    finalPath.append(node.position)
                case .offMeshConnection:
                    // handle ladders
            }
        }
    }
}
```

So a returned path is a list of nodes you iterate: `.meshPoint` nodes contribute a
position to your final path; `.offMeshConnection` nodes are special traversals (ladder,
bridge) you handle yourself.

## 3. Cloth simulation

Cloth is modeled as a mesh where **vertices = particles** and **edges = springs**. With
enough vertices RealityKit simulates flowing dresses and bed covers that crease and fold
in real time. Component model:

- **`ClothBodyComponent`** — the cloth itself; references material properties + a cloth
  mesh resource (particle/spring layout).
- **`ClothColliderComponent`** — rigid objects cloth collides with (bed, mannequin);
  references material properties + collider geometry.
- **Cloth simulation component** — holds the **array of materials** referenced by bodies
  and colliders. Material properties differ for cloth vs. collider (spring stiffness,
  friction, …). Simulation-wide properties affect all descendants: which **solver**,
  **gravity**, and **time step**.

### Pinning cloth (kinematic vertices)

The curtain hoops are implemented by pinning specific vertices so the simulation can't
move them:

```swift
// Pin the curtains to the Alchemist's lab

for (pin, pinComponent) in pins {
        let position = pin.position(relativeTo: event.entity)
        let selectionSphere = ClothSphereShape(radius: pinComponent.radius)

        let vertices = clothMesh.vertices(in: .sphere(selectionSphere),
                                    center: position)
        clothBody.motionTypes.set(vertexIndices: vertices, value: .kinematic)
}
```

Select the vertices inside a sphere at the pin's position, then set them `.kinematic`.
**Kinematic vertices move only with the entity's transform, not the simulation** — so
they stay put and hold the curtain up.

## 4. Performance

### Mesh level of detail (LOD)

Render distant/small geometry at lower detail with negligible visual impact. The
cauldron has LODs 0–5; LOD 5 looks bad up close but is indistinguishable from LOD 0 when
scaled down (far away), at much lower compute. Each LOD is an **array of entities**; an
LOD entity holds and switches between them via a switching algorithm.

By camera distance:

```swift
LevelOfDetailComponent.addByCameraDistance(to: entity, levels: [
    (entities: lod0, maxDistance: 1.0 /* meters */), // highest detail
    (entities: lod1, maxDistance: 5.0),              // medium detail
    (entities: lod2, maxDistance: .infinity),        // lowest detail
])
```

By screen area (fraction of the screen the entity occupies):

```swift
LevelOfDetailComponent.addByScreenArea(to: entity, levels: [
    (entities: lod0, minArea: 0.2 /* fraction of screen area */),  // highest detail
    (entities: lod1, minArea: 0.1),                                // medium detail
    (entities: lod2, minArea: 0.01),                               // lowest detail
])
```

- Camera distance: beyond each `maxDistance`, switch to the next (lower) LOD; the final
  level uses `.infinity`.
- Screen area: when the entity occupies **less** than `minArea` of the screen, switch to
  the next (lower) LOD.

### Thermal-state monitoring

LODs improve performance; you should also **react** when the device runs hot:

```swift
NotificationCenter.default.addObserver(of: ProcessInfo.self,
                                       for: .thermalStateDidChange) {_ in
    switch ProcessInfo.processInfo.thermalState {
        case .nominal, .fair:
            // Stay the course
        case .serious, .critical:
            // Improve performance by:
            // More aggressive LOD switching
            // Lower shadow quality
    }
}
```

`.nominal`/`.fair` → keep running as is; `.serious`/`.critical` → degrade gracefully
(more aggressive LOD thresholds, lower shadow quality). Framed as both user comfort and
a prerequisite for using the heavier features below.

## 5. 3D Gaussian splats

A high-performance, high-quality way to render volumetric real-world captures. A scene
is a collection of 3D gaussians — think **ellipsoids with varying opacity**. Rendering
evaluates a ray across all gaussians per pixel; RealityKit handles the optimizations for
you. Demo: a potted succulent captured with fine geometric detail, rendered on Vision
Pro. (Sample on developer.apple.com.)

RealityKit assumes **no specific file format** — you provide buffers describing each
splat: **position, scale, rotation, opacity, and spherical harmonics**. Spherical
harmonics control how an ellipsoid's color changes with viewing direction; the **degree**
sets how many color variations there are as you move around it (degree 0 = solid color
from all directions).

```swift
// Create Gaussian splat resource and component

let resource = try GaussianSplatResource.BufferResource(count: splatCount,
                                                        position: positionBuffer,
                                                        scale: scaleBuffer,
                                                        rotation: rotationBuffer,
                                                        opacity: opacityBuffer,
                                                        sphericalHarmonics:
                                                            (sphericalHarmonicsBuffer, degree))

let splatResource = GaussianSplatResource(resource)

let splatComponent = GaussianSplatComponent(splatResource)

splatEntity.components.set(splatComponent)
```

## 6. Immersive audio (Apple Vision Pro)

Realistic spatial audio needs accurate direction and timing of both the **direct path**
and **reflection path**, updated as the listener and source move. Environment geometry
and materials matter a lot — the same source sounds different in a small living room vs.
a large museum. RealityKit simulates reflections and reverb with **raytraced geometrical
acoustics** via a **custom reverb mesh** (e.g., modeling wood floors, plaster walls, and
a stone countertop in a kitchen/dining scene). Reverb adapts to where listener and
source are. Sample: a virtual band in a museum with independently controllable
instruments (Vision Pro only; on developer.apple.com).

### Building a reverb mesh

```swift
// Create and use custom reverb mesh

let mesh: ReverbMeshResource = .shoebox(size: [5, 4, 6])  // 5w × 4h × 6d meters

let reverb: Reverb = .simulated(mesh: mesh, materials: [.dryWall])

entity.components.set(ReverbComponent(reverb: reverb))
```

`ReverbMeshResource` can come from a mesh descriptor or mesh resource, but the easiest
start is a **shoebox** (a box with faces pointed inward). Combine the mesh with material(s)
to make a **simulated** reverb, wrap it in a `ReverbComponent`, attach to an entity.

### Custom audio materials

```swift
// Create custom materials for custom reverb mesh

let thickCarpet: Audio.Material = .carpet.scalingAbsorption {freq in 0.1 }

let bookshelf: Audio.Material

// Absorption coefficients by center frequency:
// 31.5Hz, 63Hz, 125Hz, 250Hz, 500Hz, 1kHz, 2kHz, 4kHz, 8kHz, 16kHz
let bookshelfAbsorption = Audio.Absorption(
    [0.10, 0.15, 0.28, 0.20, 0.15, 0.10, 0.10, 0.07, 0.07, 0.05])

// Scattering coefficients for: 500Hz, 1000Hz, 4000Hz
let bookshelfScattering = Audio.Scattering([500: 0.5, 1000: 0.6, 4000: 0.7])

bookshelf = .init(absorption: bookshelfAbsorption,
              scattering: bookshelfScattering)
```

- Start from a preset (`.dryWall`, `.carpet`) and tweak — e.g. `scalingAbsorption` to
  make a carpet more absorbent across all frequencies.
- Or build from scratch: **absorption** is a 10-band array (center frequencies
  31.5 Hz → 16 kHz); **scattering** can be given for just a few frequencies and
  RealityKit extrapolates across the audible spectrum. Both describe how much sound
  energy is absorbed vs. scattered.
- **Custom reverb meshes only work in immersive spaces.** In a **shared space**, the
  system's **room-sense reverb geometry** (a mesh Vision Pro builds from your real
  surroundings) is used instead.

## 7. Also shipping this year (named, not demoed)

- **Coordinated multi-source audio** — precise, synchronized audio playback across
  multiple entities.
- **High-quality character rendering** — subsurface scattering + advanced hair shaders.
- **Portal customizations** — custom portal materials to alter a portal's opacity/shape.

Pair all of this with **Reality Composer Pro 3** (light baker, navigation-mesh authoring,
faster scene editing, particle/character behavior graphs).
