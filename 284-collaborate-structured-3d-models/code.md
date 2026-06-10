# Code samples — Session 284

Extracted from the Code tab (2 snippets). Timestamps map to the transcript.

## ~7:09 — openAssembly(): push manipulation down to the children

```swift
func openAssembly() {
    components[ManipulationComponent.self] = nil
    components[InputTargetComponent.self] = nil

    for child in assemblyChildren {
        child.components.set(InputTargetComponent())

        var manipulation = ManipulationComponent()
        manipulation.releaseBehavior = .stay
        child.manipulationComponent = manipulation
    }
}
```

Remove `ManipulationComponent` and `InputTargetComponent` from the root entity (so the
whole thing is no longer manipulable as one object), then add both to each child so each
part can be grabbed independently. `releaseBehavior = .stay` keeps a part where the
person releases it.

## ~7:52 — closeAssembly(): pull manipulation back up to the root

```swift
func closeAssembly() {
    for child in assemblyChildren {
        child.manipulationComponent = nil
        child.components[InputTargetComponent.self] = nil
    }

    components.set(InputTargetComponent())
    var manipulation = ManipulationComponent()
    manipulation.releaseBehavior = .stay
    manipulationComponent = manipulation
}
```

The same process in reverse: strip the components off the children and reattach them to
the root, so the assembly once again moves as a single object while preserving the
internal layout.

---

## Spoken APIs (named in the transcript, no Code tab snippet)

- `ManipulationComponent` — RealityKit; makes an entity orientable/movable/scalable with natural hand input. (`releaseBehavior = .stay`)
- `InputTargetComponent` — required alongside `ManipulationComponent` for an entity to receive input.
- `CollisionComponent` — not shown in slides but **required** for event processing; the speaker calls out not to forget it.
- `ClippingComponent` — new in visionOS 27. Four properties; the sample uses:
  - `bounds` — axis-aligned bounding box in entity local space; geometry outside is discarded each frame.
  - `shouldClipChildren` — defaults `false`; the sample sets it `true`.
  - `shouldClipSelf` — defaults `true`.
- Custom sample components (not RealityKit APIs):
  - `ClippingBoundsCache` — remembers the last-edited bounds and feeds them back to `ClippingComponent` on `.on`.
  - `ClippingTransformSync` — tracks the assembly transform and updates the `ClippingControl` when it changes.
  - `ClippingControl` — entity managing the six interactive clipping-plane affordances.
- Clipping three-state machine: `.off`, `.on`, `.editing`.
- `FromToBy` animations — used to move sub-assemblies into their expanded positions.
- Math used: dot product / vector projection onto the plane normal (`{1, 0, 0}` for +x), volume-weighted position variance.
