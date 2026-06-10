# Code samples — Session 281

Extracted from the Code tab. Timestamps map to the transcript chapters.

## Cauldron component (water level)

```swift
// Add a component to represent the water level

import RealityKit

public struct Cauldron: Component, Codable {
    public var waterLevel: Float

    enum CodingKeys: CodingKey {
        case waterLevel
    }
}
```

## CauldronSystem (drive water position)

```swift
// Add a system to control the water level

import RealityKit

public struct CauldronSystem: System {
    let query = EntityComponentQuery(Cauldron.self)
    public init(scene: Scene) {}

    public func update(context: SceneUpdateContext) {
        for (entity, cauldron) in context.entities(matching: query) {
            guard let water = entity.findEntity(named: "Cauldron_Water_mesh")
                else { continue }
            water.setPosition(SIMD3<Float>(0, 1, 0) * cauldron.waterLevel, relativeTo: entity)
        }
    }
}
```

## Register the plug-in with the editor

```swift
// Make sure that Reality Composer Pro 3 knows about the Cauldron and CauldronSystem

import RealityComposerPro

final class RCPCustomComponentsPlugin: RealityComposerProPlugin {
    public func setup(context: any RealityComposerProContext) {
        context.registerComponent(Cauldron.self)
        context.registerSystem(CauldronSystem.self)
    }
}

@_cdecl("createRealityComposerProPlugin")
public func createRealityComposerProPlugin() -> UnsafeMutableRawPointer {
    return RCPCustomComponentsPlugin().passRetained()
}
```

## Cauldron component (vortex surface properties)

```swift
// Properties to control water surface

import RealityKit

public struct Cauldron: Component, Codable {
    public var waterLevel: Float
    public var rotationSpeed: Float
    public var minWaterLevel: Float
    public var maxWaterLevel: Float
    public var vortexCoeff: Float
}
```

## System update driving a ShaderGraphMaterial

```swift
public func update(context: SceneUpdateContext) {
    for (entity, cauldron) in context.entities(matching: query) {
        guard let water = entity.findEntity(named: "Cauldron_Water_mesh") else { continue }
        water.setPosition(SIMD3<Float>(0, 1, 0) * cauldron.waterLevel, relativeTo: entity)

        guard var model = water.components[ModelComponent.self] else { continue }
        guard var mat = model.materials.first as? ShaderGraphMaterial else { continue }
        let surface = computeSurface(cauldron: cauldron)
        try? mat.setParameter(name: "Level Radius", value: .float(surface.levelRadius))
        try? mat.setParameter(name: "Lowest Point",
            value: .float(cauldron.waterLevel - surface.lowestPoint))
        try? mat.setParameter(name: "Height Change", value: .float(surface.heightChange))
        try? mat.setParameter(name: "Level Coeff", value: .float(surface.levelCoeff))
        try? mat.setParameter(name: "Is Level", value: .bool(surface.isLevel))
        model.materials[0] = mat
        water.components.set(model)
    }
}
```

## SetWaterLevelAction (EntityAction)

```swift
// Custom action for setting the water level of the Cauldron

import RealityKit

public struct SetWaterLevelAction: EntityAction, Codable {
    // Parameters for the action
    public let startWaterLevel: Float
    public let endWaterLevel: Float

    // Required by EntityAction protocol
    public var animatedValueType: (any AnimatableData.Type)? { Transform.self }
}
```

## SetWaterLevelAction.subscribe() — interpolate on update events

```swift
extension SetWaterLevelAction {
    static func subscribe() {
        Task { @MainActor in
            SetWaterLevelAction.subscribe(to: .updated) { event in
                let normalizedTime = (event.playbackController.time - event.startTime) /
                    event.duration
                let action = event.action
                let currentLevel = action.startWaterLevel +
                    Float(normalizedTime) * (action.endWaterLevel - action.startWaterLevel)
                guard let entity = event.targetEntity else { return }
                guard var cauldron = entity.components[Cauldron.self] else { return }
                cauldron.waterLevel = currentLevel
                entity.components.set(cauldron)
            }
        }
    }
}
```

## Register the animation action

```swift
// Make sure that Reality Composer Pro 3 knows about the SetWaterLevelAction

import RealityComposerPro

final class RCPCustomComponentsPlugin: RealityComposerProPlugin {
    public func setup(context: any RealityComposerProContext) {
        context.registerComponent(Cauldron.self)
        context.registerSystem(CauldronSystem.self)

        context.registerAction(SetWaterLevelAction.self)
        SetWaterLevelAction.subscribe()
    }
}

@_cdecl("createRealityComposerProPlugin")
public func createRealityComposerProPlugin() -> UnsafeMutableRawPointer {
    return RCPCustomComponentsPlugin().passRetained()
}
```

## Expose the component to Script Graphs (@Scriptable)

```swift
// Expose Cauldron to Script Graphs

import RealityKit
import RealityKitScripting
import RealityKitScriptingMacros

@Scriptable
public struct Cauldron: Component, Codable {
    public var waterLevel: Float
    public var rotationSpeed: Float
    public var minWaterLevel: Float
    public var maxWaterLevel: Float
    public var vortexCoeff: Float
}
```

## Register the scripting module

```swift
// Register scripting module

public func setup(context: any RealityComposerProContext) {
    context.registerComponent(Cauldron.self)
    context.registerSystem(CauldronSystem.self)

    context.registerAction(SetWaterLevelAction.self)
    SetWaterLevelAction.subscribe()

    Task { @MainActor in
        let config = RKS.Configuration(id: "ChaparralVillage")
            .onInitialize { _ in
            [
                Module("ChaparralVillage") {
                    Cauldron.SchemaProvider.schema
                }
            ]
        }
        try! RKS.addConfiguration(config)
    }
}
```

---

## Useful API facts surfaced by the code

- A plug-in is a class conforming to `RealityComposerProPlugin` with a `setup(context:)` method; the editor finds it through a C-exported `createRealityComposerProPlugin()` (`@_cdecl`) returning an `UnsafeMutableRawPointer` via `.passRetained()`.
- Registration calls in `setup`: `context.registerComponent(_:)`, `context.registerSystem(_:)`, `context.registerAction(_:)`, plus `RKS.addConfiguration(_:)` for scripting modules.
- Components must be `Component, Codable` to appear in the editor and serialize to Reality Files. `CodingKeys` lets you hide runtime-only properties from the inspector.
- Custom animation actions conform to `EntityAction, Codable`; `animatedValueType` returns `Transform.self`; runtime behavior is wired via `subscribe(to: .updated)` and interpolated over a normalized 0…1 time.
- Shader parameters are set on a `ShaderGraphMaterial` via `setParameter(name:value:)` with `.float(_)` / `.bool(_)` values, then re-assigned onto the `ModelComponent`.
- `@Scriptable` (from `RealityKitScripting` / `RealityKitScriptingMacros`) generates a `SchemaProvider.schema`; scripting modules must be registered on the main thread.
- The example uses `EntityComponentQuery(Cauldron.self)` to enumerate entities; the water mesh child is found by name `"Cauldron_Water_mesh"`.
