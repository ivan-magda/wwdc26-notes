# Session 284 — Collaborate on structured 3D models in visionOS

- **URL:** https://developer.apple.com/videos/play/wwdc2026/284/
- **Duration:** 25m
- **Speakers:** Bill

## Description

Build collaborative spatial experiences on Apple Vision Pro for working with complex
3D assemblies and multi-dimensional data. The session walks through a SharePlay design
review of an AirPods Pro model and covers the four pillars of the sample code:
preparing assets with a deep nested hierarchy, making whole assemblies (or each part)
manipulable with `ManipulationComponent`, an interactive clipping capability new in
visionOS 27 (`ClippingComponent`), and automatic exploded-view expansion driven by
volume-weighted position variance.

## Key topics

- Why three Vision Pro strengths matter for this: real-time collaboration (SharePlay), manipulation of rich representations, and environment lighting — applicable beyond CAD (urban planning, logistics, real estate, production design)
- Asset preparation: preserve a deep, nested hierarchy in USDZ exports so individual parts stay independently selectable; a flattened-to-root export renders fine but is uninteractable
- Manipulation: attach `ManipulationComponent` + `InputTargetComponent` at the root to move the whole assembly, or push them down to children to make each part independently grabbable; `openAssembly()` / `closeAssembly()` just move components in the tree
- `releaseBehavior = .stay` so a part stays where the person puts it; CollisionComponent is required for event processing (not shown in slides but critical)
- Clipping (new in visionOS 27): `ClippingComponent` with `bounds`, `shouldClipChildren` (default false), `shouldClipSelf` (default true); a three-state machine `.off` / `.on` / `.editing`
- Six interactive plane entities (one per axis face) each control one scalar in the bounds — "six planes, six numbers"
- Four coordinate frames (World, Model, Clipping Control, Clipping Plane) and how a drag delta is transformed between them and constrained via vector projection (dot product onto the plane normal)
- Autoexpansion: pick the expansion axis by computing volume-weighted position variance per axis, expand along the largest; assemble `FromToBy` animations to move sub-assemblies into place
- Statistics primer baked in: deviation, variance, weighted variance

## Related sessions to fetch (referenced in this talk)

- [ ] Share visionOS experiences with nearby people (WWDC25)
- [ ] Optimize your 3D assets for spatial computing (WWDC24)
- [ ] What's new in RealityKit (WWDC25)
- [ ] Better Together: SwiftUI and RealityKit (WWDC25)
- [ ] Discover the spatial preview framework
- [ ] Explore enhancements to visionOS object tracking

## Chapter summary (Summary tab)

- **0:00 Introduction** — Overview of collaborative spatial experiences on Apple Vision Pro: real-time manipulation of rich 3D assemblies, interactive clipping, automatic sub-assembly expansion. Framed by a SharePlay AirPods Pro design review.
- **2:55 Asset preparation** — Key requirements for preparing 3D assets, especially preserving a deep, nested hierarchy in USDZ exports so individual parts remain independently selectable and manipulable at runtime.
- **5:05 Manipulating the hierarchy** — Use `ManipulationComponent` and `InputTargetComponent` to make an entire assembly — or each sub-entity individually — interactive. Covers `openAssembly()` / `closeAssembly()` and the `releaseBehavior` setting.
- **8:15 Interactive clipping** — `ClippingComponent`, a new RealityKit capability in visionOS 27, lets people see through complex assemblies. Covers the three-state clipping machine (`.off`, `.on`, `.editing`), coordinate-frame transformations, and how drag gestures update clipping plane bounds.
- **18:16 Autoexpansion** — Automatically expand an assembly's sub-components along the most meaningful axis using volume-weighted variance. Covers the math for choosing the expansion axis and assembling `FromToBy` animations.
- **24:10 Next steps** — Takeaways and pointers: Model Manipulator sample project, the spatial preview framework session, and recommended background in statistics, vector math, and linear algebra.

## Code

See `code.md` — 2 snippets from the Code tab (`openAssembly()` / `closeAssembly()`), plus the concrete APIs the transcript names.
