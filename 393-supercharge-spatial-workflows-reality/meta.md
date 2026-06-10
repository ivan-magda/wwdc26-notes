# Session 393 — Supercharge your spatial workflows with Reality Composer Pro 3

- **URL:** https://developer.apple.com/videos/play/wwdc2026/393/
- **Duration:** 22m
- **Speakers:** Vincent (Reality Composer Pro engineer)

## Description

A tour of the visual, node-based authoring tools in Reality Composer Pro 3 —
Animation Graph, Behavior Tree, Script Graph, Navigation Mesh, Compute Graph, and
Shader Graph — and how they combine to build an interactive spatial scene without
writing code. Built around the "alchemy area" scene from the Chaparral Village game,
the session brings an alchemist character to life: it blends idle/walk animations,
follows an autonomous routine, responds to a tap, pathfinds around obstacles, and
the cauldron gets a GPU-driven smoke particle effect.

## Key topics

- **Animation Graph** — Final Pose node, State Machine (Idle/Walk states), transitions with Bool Conditions, an `isWalking` runtime input, and Animation Clip nodes; live state highlighting for debugging.
- **Behavior Tree** — top-to-bottom / left-to-right evaluation; Composite nodes (Sequence, Selector, Parallel) vs Action nodes (Move To, Rotate To Face, Wait, Parameter Setter); a patrol-and-react routine plus a `readyToBrew` Precondition gate.
- **Script Graph** — event-driven visual scripting; On Initialize, On Tap, Set Entity Parameter nodes; reusable subgraphs (function-like); Live Preview on Apple Vision Pro via the Companion App + Mac Virtual Display.
- **Navigation Mesh** (new) — walkable-surface definition; Shapes/bounding box for generation, Off-Mesh Connections (ladders/bridges) with viewport gizmos, Generation Parameters (cell size / voxel sampling); usable from Behavior Tree, Animation Graph, or custom Swift via the navigation component.
- **Compute Graph** — Metal-backed GPU particle simulation; four phases (Emitter → Initialize → Simulate → Output); custom nodes via a Compute Graph bundle; Shader Graph material for rendering.
- **Shader Graph enhancements** — RealityKit PBR Surface 2 (sheen, subsurface scattering, better diffuse/occlusion), Hair Surface shader, Portal Surface + Portal Geometry Modifier.

## Related sessions to fetch (referenced in this talk)

- [ ] Iterate Your Spatial Scenes Faster with Reality Composer Pro 3
- [ ] Design No-Code Games with Reality Composer Pro 3
- [ ] Explore Advances in RealityKit (Dennis)
- [ ] Explore materials in Reality Composer Pro (Niel)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Vincent; overview of the visual, node-based tools (Animation Graph, Behavior Tree, Script Graph, Navigation Mesh, Compute Graph, Shader Graph) and the plan to bring the Chaparral Village alchemy scene to life without code.
- **2:10 Animation Graph** — blend character animations at runtime; build a State Machine with Idle/Walk states, transition conditions, runtime `isWalking` parameter, and Animation Clip nodes.
- **6:21 Behavior Tree** — author autonomous, multi-step routines; Composite nodes (Sequence/Selector/Parallel), Action nodes (Move To, Rotate To Face, Wait, Parameter Setter); compose the alchemist's patrol-and-react routine.
- **11:22 Script Graph** — add event-driven interactivity without code; On Initialize + On Tap nodes, subgraphs to set Behavior Tree parameters, Live Preview on Apple Vision Pro.
- **14:46 Navigation Mesh** — automatic pathfinding around obstacles; configure bounding box, off-mesh connections (ladders), and generation parameters like cell size.
- **17:08 Compute Graph** — GPU-driven particle sim backed by Metal; the four phases (Emitter, Initialize, Simulate, Output) walked through for the cauldron's smoke effect.
- **19:43 Shader Graph enhancements** — RealityKit PBR Surface 2 (sheen, subsurface scattering), Hair Surface shader, portal-rendering support.
- **21:12 Next steps** — download Reality Composer Pro 3; links to "Design No-Code Games with Reality Composer Pro 3" and "Explore Advances in RealityKit."

## Code

See `code.md` — this session has no Code tab (it is a live-editor walkthrough); `code.md`
records the concrete nodes, parameters, and APIs named on screen.
