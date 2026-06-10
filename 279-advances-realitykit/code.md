# Code samples — Session 279

Extracted from the session's Code tab. Timestamps approximate the matching
moment in the transcript.

## ~4:06 — Soft shadows for a spotlight

```swift
// Enable soft shadows for the hearth spotlight

guard var shadow = hearthSpotlight.components[SpotLightComponent.Shadow.self] else {
    // handle error
}
shadow.lightSize = 0.7 // meters

shadow.quality = .medium // or .high
// shadow.quality = .low // will result in hard shadows

hearthSpotlight.components.set(shadow)
```

## ~6:13 — Spotlight with a projective texture

```swift
// Create one of the planetarium spotlights

let spotLightEntity = Entity()
spotLightEntity.components.set(SpotLightComponent(
    color: .white,
    intensity: intensity,
    innerAngleInDegrees: innerAngle,
    outerAngleInDegrees: outerAngle,
    attenuationRadius: attenuationRadius,
))

let projectiveTexture: TextureResource = generateStarsAndNebulaeTexture()
spotLightEntity.components.set(SpotLightComponent.ProjectiveTexture(
    texture: projectiveTexture
))
```

## ~7:22 — Physical space lighting

```swift
// Enable physical space lighting

spotLightEntity.components.set(SpotLightComponent.SurroundingsLight())
```

## ~9:52 — Querying the navigation mesh

```swift
// Querying the navigation mesh in Chaparral Village

extension Entity {
    public func navigate(/* ... */) async {
        let navigator = try! NavigationController(entity: self)
        guard let result = await navigator.computePath(from: fromPosition, to: toPosition)
        else {
            return
        }
        if result.isEmpty {
            return
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

## ~13:15 — Pinning cloth vertices (kinematic)

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

## ~15:31 — Mesh LOD by camera distance

```swift
// Create entity with LODs

let lod0 = [ModelEntity(mesh: lodMesh0)]
let lod1 = [ModelEntity(mesh: lodMesh1)]
let lod2 = [ModelEntity(mesh: lodMesh2)]

let entity = Entity()

LevelOfDetailComponent.addByCameraDistance(to: entity, levels: [
    (entities: lod0, maxDistance: 1.0 /* meters */), // highest detail
    (entities: lod1, maxDistance: 5.0),              // medium detail
    (entities: lod2, maxDistance: .infinity),        // lowest detail
])
```

## ~16:02 — Mesh LOD by screen area

```swift
// Create entity with LODs

let lod0 = [ModelEntity(mesh: lodMesh0)]
let lod1 = [ModelEntity(mesh: lodMesh1)]
let lod2 = [ModelEntity(mesh: lodMesh2)]

let entity = Entity()

LevelOfDetailComponent.addByScreenArea(to: entity, levels: [
    (entities: lod0, minArea: 0.2 /* fraction of screen area */),  // highest detail
    (entities: lod1, minArea: 0.1),                                // medium detail
    (entities: lod2, minArea: 0.01),                               // lowest detail
])
```

## ~16:26 — Reacting to thermal state

```swift
// Respond to changes in device thermal state

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

## ~18:44 — Gaussian splat resource and component

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

## ~21:10 — Custom reverb mesh (shoebox + preset material)

```swift
// Create and use custom reverb mesh

let mesh: ReverbMeshResource = .shoebox(size: [5, 4, 6])

let reverb: Reverb = .simulated(mesh: mesh, materials: [.dryWall])

entity.components.set(ReverbComponent(reverb: reverb))
```

## ~21:33 — Custom audio materials (absorption / scattering)

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

---

## Useful API facts surfaced by the code

- Soft shadows: `SpotLightComponent.Shadow.lightSize` is the light diameter in meters (default 0 = hard); `quality` must be `.medium` or `.high` (`.low` forces hard shadows).
- Projective textures: `SpotLightComponent.ProjectiveTexture(texture:)`; physical space lighting: `SpotLightComponent.SurroundingsLight()` (spotlights + point lights only).
- Navigation: `NavigationController(entity:)`, `await navigator.computePath(from:to:)` returns nil on failure / empty on arrival; path nodes have a `category` of `.meshPoint` or `.offMeshConnection`.
- Cloth: pin via `clothMesh.vertices(in: .sphere(ClothSphereShape(radius:)), center:)` then `clothBody.motionTypes.set(vertexIndices:value: .kinematic)`.
- LOD convenience: `LevelOfDetailComponent.addByCameraDistance(to:levels:)` (tuples of `entities` + `maxDistance`) and `addByScreenArea(to:levels:)` (tuples of `entities` + `minArea` as a screen fraction); final level uses `.infinity` distance.
- Thermal: `ProcessInfo` `.thermalStateDidChange` notification; states `.nominal`/`.fair`/`.serious`/`.critical`.
- Gaussian splats: `GaussianSplatResource.BufferResource(count:position:scale:rotation:opacity:sphericalHarmonics:(buffer, degree))` → `GaussianSplatResource` → `GaussianSplatComponent`.
- Audio: `ReverbMeshResource.shoebox(size:)`, `Reverb.simulated(mesh:materials:)`, `ReverbComponent(reverb:)`; `Audio.Material` presets (`.dryWall`, `.carpet`) with `.scalingAbsorption { freq in ... }`; build from scratch with `Audio.Absorption([10 bands])` + `Audio.Scattering([freq: coeff])`. Custom materials need an immersive space; shared space uses system room-sense reverb.
