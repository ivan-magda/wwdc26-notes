# Session 252 — Design no-code games with Reality Composer Pro 3

- **URL:** https://developer.apple.com/videos/play/wwdc2026/252/
- **Duration:** 19m
- **Speakers:** Saschka Unseld (Creative Director, Apple)

## Description

Discover how Reality Composer Pro 3 helps designers prototype and build games
without writing a single line of code. Saschka builds a RealityKit visionOS game
from scratch using ScriptGraph — Reality Composer Pro 3's node-based visual
scripting — wiring up event-driven logic to wake a sleeping squirrel, let the
player steal and drag its nut with their hands, and guide the squirrel home before
night falls.

## Key topics

- **ScriptGraph** — node-based visual scripting; event-driven logic (event node → logic/math nodes → Set nodes that modify components)
- Making an entity interactive: Input Target, Collision, and Hover Effect components; Scripting Component to attach a ScriptGraph
- Building drag logic: `On Drag` event → `Set Transform` node; Scene Location / Scene Translation outputs
- Test and iterate in-viewport (Play button) and on Vision Pro via **Preview on Device**; **Live preview** coming later this year
- **Input variables** (e.g. `dragSpeed`), public variables surface in the Scripting Component; per-instance **Overrides** (bold = overridden)
- Physics: Physics Body Component, `Add Force` node, `Set PhysicsBodyComponent` node (toggle gravity, raise linear damping), tracking `dragDelta` over time
- **Subgraphs** (Compose Subgraph) and **Prototyped Subgraphs** (Convert to Prototyped Subgraph) for reusable logic in the add-node menu
- **Custom Events** via a Custom Node Library (`nutIsDragged` with a `nutPosition` property), `Send` / `On` event nodes, Sync Nodes — multiple ScriptGraphs talking to each other across entities
- **Set Material Parameter** node driving a Shader Graph public input (`isNutDragged`) to swap textures / character reactions
- **Scene Events** bridging ScriptGraph and Swift: `Send Scene Event` (`squirrelTalk` with a `sayThis` String) subscribed from SwiftUI; Reality Composer Pro 3 can generate an Xcode project; Coding Intelligence to write the glue code

## Related sessions to fetch (referenced in this talk)

- [ ] Reality Composer Pro 3 (the "get the basics first" session)
- [ ] Advanced workflows deep dive (named at "take a deep dive into advanced workflows")

## Chapter summary (Summary tab)

- **0:00 Introduction** — Reality Composer Pro 3 lets designers prototype and build games without writing code.
- **1:02 Meet ScriptGraph** — node-based scripting; quickly mock up ideas and build games through event-driven logic; review and iterate directly in Reality Composer Pro.
- **1:50 A wish…** — Saschka's visionOS game concept: wake a sleeping squirrel, steal its nut, and use the nut to guide the squirrel home before winter.
- **2:36 Build the game** — add components to an entity, add game logic with ScriptGraph; create and link nodes; iterate and preview interactions in Reality Composer Pro (and, later this year, directly on Vision Pro).
- **11:16 Advanced techniques** — organize nodes into subgraphs and reusable Prototyped Subgraphs; create custom events; build multiple interacting ScriptGraphs; change materials so characters react; incorporate SwiftUI (speech bubbles) via Scene Events.
- **18:32 Next steps** — download Reality Composer Pro 3; check out the sample project containing this session's game.

## Code

See `code.md` — one snippet from the Code tab (the SwiftUI / Scene Event bridge),
plus the concrete ScriptGraph node names spoken in the talk.
