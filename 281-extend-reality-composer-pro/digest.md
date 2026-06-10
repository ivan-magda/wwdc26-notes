---
title: "Extend Reality Composer Pro 3 functionality with Xcode — Full Digest"
session: WWDC26 · 281
url: https://developer.apple.com/videos/play/wwdc2026/281/
duration: 22m
speakers: Niklas
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 281 — Extend Reality Composer Pro 3 functionality with Xcode

## TL;DR

Reality Composer Pro 3 (Apple's RealityKit game/3D content editor, "available later
this year") gains a **plug-in system** that lets engineers extend the editor with
custom Swift so artists can manipulate project-specific data live — no build-and-deploy
loop. The mechanism is one shared git repo, a dynamic library compiled from your
component/system code, and a trust-on-load step. Four extension points, all demoed on a
"Chaparral Village" cauldron whose water level and vortex swirl are tuned in the
inspector:

1. **Custom components + systems** — define a `Cauldron` component and `CauldronSystem`, register them in a `RealityComposerProPlugin`, and the system *runs inside the editor*. Artists change the water level and see the mesh react in real time. You can even attach Xcode's debugger to the editor.
2. **Driving materials** — extend the component with vortex properties and push them into a `ShaderGraphMaterial` at runtime via `setParameter(name:value:)`.
3. **Custom animation actions** — implement `EntityAction`, hook `.updated` events to interpolate over normalized time, and drag the action onto the sequencer timeline.
4. **Custom Script Graph nodes** — tag a component with `@Scriptable`, register a scripting module, and the generated nodes appear for no-code artist workflows.

The throughline: **one codebase, two audiences.** The same Swift compiles into the
shipping app (`ChaparralVillage` scheme) *and* the editor plug-in (`RCPCustomComponents`
scheme), closing the gap between what engineers write and what artists can touch.

---

## 1. Introduction & the editor plug-in mechanism

Reality Composer Pro 3 adds support for larger scenes, artist-friendly iteration, and
in-headset scene preview (see "Iterate your spatial scenes faster with Reality Composer
Pro 3" and "Design no-code games with Reality Composer Pro 3"). This session is the
**code** angle: building Xcode plug-ins that surface project-specific content in the
editor.

How it fits together on a team:

- A game like **Chaparral Village** has *both* a Reality Composer Pro project and an
  Xcode project, **linked** (launch the app from the editor via the simulation bar) and
  living in the **same git repository**.
- Imported assets are converted to an internal format and saved as **JSON on disk**.
  Standard git merge works, but the editor ships a **custom merge tool** that produces
  fewer conflicts.
- Scenes are exported as a **Reality File** (RealityKit's serialization format), which
  the app links and loads.
- The Xcode project has **two schemes** sharing the same component/system code:
  - `ChaparralVillage` → builds the app.
  - `RCPCustomComponents` → builds the `RCPCustomComponents.framework` plug-in for the
    editor.

When code changes, engineers rebuild the plug-in framework (and the app); when content
changes, a new Reality File is exported to test in the app.

## 2. Custom components and systems

Goal: control a cauldron's water level (it rises/falls as potion ingredients are added).
Niklas notes this *could* be a Script Graph, but code wins when the logic gets complex,
needs to interact with other systems (ingredients floating on the surface), or needs
Apple APIs unavailable to Script Graph (e.g. **SwiftUI**). Big Script Graphs get hard to
maintain — a reason to switch to code.

**The component** is plain RealityKit, but must be `Codable` so it can be represented in
the editor and serialized to Reality Files:

```swift
import RealityKit

public struct Cauldron: Component, Codable {
    public var waterLevel: Float

    enum CodingKeys: CodingKey {
        case waterLevel
    }
}
```

> `CodingKeys` is optional here (this component serializes everything). It matters for
> advanced components with runtime-only properties you *don't* want shown in the editor.

**The system** finds entities with the component, locates the water mesh child by name,
and moves it:

```swift
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

**The plug-in** ties it to the editor via the `RealityComposerProPlugin` protocol (from
the `RealityComposerPro` Swift package, which is auto-added when you link the project
using **Run With Xcode** in the simulation toolbar):

```swift
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

The `createRealityComposerProPlugin()` entry point returns a **raw pointer** and is
marked `@_cdecl` (a C function with an exported name) so the plug-in loader can find it
in the **DLL interface**.

**The loop in practice:** build the plug-in scheme → open the project in the editor →
the editor asks whether you **trust** the plug-in → on Trust, the imported component
appears (also visible in build settings, where you can point at a custom plug-in
directory; components live in the project's **Custom Components** folder). Add the
component to the cauldron entity, drag the water-level slider, and the surface reacts —
the custom system is running **inside the editor**. No rebuild/relaunch for artists.

**Debugging:** set a breakpoint and **attach Xcode to the editor application**; when your
system runs, execution stops in the debugger.

## 3. Controlling the water surface (ShaderGraphMaterial)

To add a swirling vortex, a tech artist built a **vortex shader** with Reality Composer
Pro 3's Shader Graph, exposing parameters like rotation speed. The component grows extra
properties to guide the shape:

```swift
public struct Cauldron: Component, Codable {
    public var waterLevel: Float
    public var rotationSpeed: Float
    public var minWaterLevel: Float
    public var maxWaterLevel: Float
    public var vortexCoeff: Float
}
```

The system retrieves the `ShaderGraphMaterial`, computes the surface shape from the
component, sets named parameters, and assigns the material back:

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

**Rebuild caveat:** changing the plug-in means **restarting Reality Composer Pro**. On
restart it re-asks for trust (there's a "Don't ask again" checkbox) and then shows a
**diff dialog of the component changes** to accept. Increasing the rotation speed in the
inspector deepens the vortex live.

## 4. Custom animation actions (sequencer timeline)

The animation sequencer accepts **custom actions** defined in the plug-in. The action
conforms to `EntityAction` + `Codable` (for Reality File storage), takes start/end
levels, and returns `Transform.self` as its `animatedValueType` (needed to access the
entity in the animation executor):

```swift
public struct SetWaterLevelAction: EntityAction, Codable {
    public let startWaterLevel: Float
    public let endWaterLevel: Float

    public var animatedValueType: (any AnimatableData.Type)? { Transform.self }
}
```

Runtime behavior subscribes to `.updated` events RealityKit fires while running
animations, computes normalized elapsed time (0…1), and interpolates the level:

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

Register it (and call `subscribe()`) in `setup`, exactly like components and systems:

```swift
context.registerAction(SetWaterLevelAction.self)
SetWaterLevelAction.subscribe()
```

**In the editor:** restart → "new action imported" → create a sequence, set its root to a
scene containing the cauldron (`CauldronWorld`), add an animation track targeting the
cauldron, **drag `SetWaterLevelAction`** onto the timeline, set start `0.3` / stop `0.5`
in the inspector, and play — the water animates between the two values.

## 5. Custom Script Graph nodes (@Scriptable)

Script Graphs let designers build interactivity with no code; plug-ins can add **custom
nodes** to extend the built-in set. The quickest path is the **`@Scriptable` macro**
(from `RealityKitScripting` / `RealityKitScriptingMacros`, auto-configured when you
create the Xcode project from the editor):

```swift
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

`@Scriptable` expands to a **schema** describing the component. Register a scripting
module in `setup` — **on the main thread** — by returning your modules from a
configuration's initializer:

```swift
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
```

**In the editor:** add a **Scripting** component to the cauldron entity, double-click to
open the Script Graph editor, and build the graph — an **update** node, an **If** node
hooked to a keypress, and a node to **set the water level**. Demo: the `a` key sets the
level to `0.25`; copy/paste the graph and rebind to `z` → `0.5`. Open a simulation view
and the level rises/falls as the keys are pressed.

## 6. Next steps

Recap: simple Xcode plug-ins extend the editor to work with your app's data and even run
your code inside the editor. Pointers: **"Explore advances in RealityKit"** (latest
RealityKit additions) and **"Supercharge your spatial workflows with Reality Composer
Pro 3"** (editor productivity).
