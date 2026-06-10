---
title: "Supercharge your spatial workflows with Reality Composer Pro 3 — Full Digest"
session: WWDC26 · 393
url: https://developer.apple.com/videos/play/wwdc2026/393/
duration: 22m
speakers: Vincent
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 393 — Supercharge your spatial workflows with Reality Composer Pro 3

## TL;DR

Reality Composer Pro 3 leans hard into **visual, node-based authoring** — you can build
an interactive spatial scene with animation, autonomous behavior, interactivity, pathfinding,
and GPU particles **without writing code**. The whole talk is a single worked example: the
"alchemy area" from the **Chaparral Village** game (built entirely in Reality Composer Pro 3
+ Swift). An alchemist character gets brought to life across six tools:

1. **Animation Graph** — runtime animation blending via a State Machine (Idle ↔ Walk) driven by an `isWalking` boolean.
2. **Behavior Tree** — autonomous, multi-step routines from Composite + Action nodes (walk to table → prepare → walk to cauldron).
3. **Script Graph** — event-driven interactivity (On Initialize setup + On Tap → set a `readyToBrew` parameter), testable live on Vision Pro.
4. **Navigation Mesh** (new) — automatic pathfinding around obstacles, with off-mesh connections for ladders/bridges.
5. **Compute Graph** — Metal-backed GPU particle simulation in four phases (Emitter / Initialize / Simulate / Output) for the cauldron smoke.
6. **Shader Graph enhancements** — RealityKit PBR Surface 2 (sheen + subsurface scattering), Hair Surface, and portal surface/geometry.

The throughline: visual tools mean **fast prototyping with no build cycle**, and **anyone on
the team** (not just engineers) can author and test behavior directly in the editor.

---

## 1. Animation Graph — blending animations at runtime

Animation Graph is a visual node editor for controlling how a character animates at runtime.
It supports motion warping, blend spaces, inverse kinematics, and more. The demo keeps it
simple: blend the alchemist between **Idle** and **Walk**.

The build:

- The graph starts with a **Final Pose** node by default — whatever pose flows in is what the character displays.
- Connect a **State Machine** node into Final Pose. The State Machine decides which animation is active at any moment.
- Double-click to step into the State Machine editor. Add two **Animation State** nodes: `Idle` and `Walk`.
- Add **transitions** `Idle → Walk` and `Walk → Idle`.
- Transitions need conditions, and conditions need an input: add a boolean `isWalking` in the **Inputs Inspector** (set at runtime).
- `Idle → Walk` gets a **Bool Condition** `isWalking == true`; `Walk → Idle` gets the inverse `isWalking == false`.
- Back in the main graph, each State Machine state input gets an **Animation Clip** node — one bound to the idle clip, one to the walk clip.

Testing: press **Play**, toggle `isWalking` in the Inspector. True blends into walk; false lets
the character finish its current walk cycle, then blends back to idle. The **active state node is
highlighted** in the editor as transitions happen — handy for debugging graphs with many states.

## 2. Behavior Tree — autonomous, multi-step routines

Behavior Trees author autonomous behavior (patrol an area, react to events, follow a routine)
directly in the editor, no code.

**Mental model:** a Behavior Tree is a hierarchy of nodes, evaluated **top-to-bottom**, and
**left-to-right** among siblings — higher and leftmost nodes run first. Two node kinds:

- **Composite nodes** control flow:
  - `Sequence` — runs children one by one in order; if any action fails, the whole sequence stops immediately.
  - `Selector` — evaluates children until one succeeds, then stops.
  - `Parallel` — runs all children simultaneously.
- **Action nodes** do the work and are bound to a Composite parent that dictates their order/conditions. Built-ins used here: `Move To`, `Rotate To Face`, `Wait`, `Parameter Setter` (the editor offers many more).

The routine:

- A **Sequence** for the table sub-routine: `Rotate To Face` → `Move To` → `Wait` (1s to "prepare ingredients").
- The Rotate/Move nodes need a target and speed → inputs `tablePosition`, `rotationRate`, `movementRate`.
- To drive the Animation Graph, add an `isWalking` input and bracket the movement with **Parameter Setter** nodes: set `isWalking = true` before `Rotate To Face`, and `isWalking = false` after `Move To`. So the walk animation plays only while moving.
- The cauldron sub-sequence is identical with `cauldronPosition`. A **Parent Sequence** wires table-then-cauldron.

**Making it interactive (revisited after Script Graph):** delete the table `Wait` node so the
alchemist waits indefinitely; add a Boolean input `readyToBrew` (default `false`); put a
**Precondition → Bool Condition** on the cauldron sub-sequence so the character stays at the
table until `readyToBrew` becomes true.

## 3. Script Graph — event-driven interactivity, no code

Script Graph is a visual scripting system defining how entities behave and interact. It's
**event-driven**: a graph runs in response to scene-level or per-entity events. Because it's
visual, prototyping and iteration are fast and **anyone on the team** can build/test behaviors
with **no build cycle**.

The setup:

- **On Initialize** node — fires once when the Scripting component initializes; good for setup.
- A teammate's reusable **Setup Behavior Tree Position** subgraph (subgraphs work like functions) finds the table and cauldron entities and writes their world positions to the entity parameter. Drag it from the Project Browser, connect its event input to On Initialize's event output.
- Run it: the alchemist turns to the table, prepares ingredients, then heads to the cauldron — the full Behavior Tree routine.

Adding the tap:

- **On Tap** node — listens for tap gesture events on the entity.
- **Set Entity Parameter** node — name `readyToBrew`, toggle to `true` (matching the Behavior Tree precondition).
- Connect On Tap's event output to Set Entity Parameter.

This is tested with **Live Preview** on Apple Vision Pro: using the **Mac Virtual Display** plus
the **Reality Composer Pro Companion App**, the author iterates on the scene immersively. The
alchemist waits at the table; a tap turns it and walks it to the cauldron to begin brewing.

> Net result: the character animates, follows a routine, and responds to interaction — all
> authored visually by combining Animation Graph, Behavior Tree, and Script Graph.

## 4. Navigation Mesh (new) — automatic pathfinding

A **Navigation Mesh** defines the walkable surfaces in a scene. A **navigation controller**
routes characters point-to-point, automatically avoiding obstacles (trees, water). The
**Navigation Mesh component** has three sections:

- **Shapes** — the bounding box for mesh generation; only geometry inside the box is used to compute the Navigation Mesh resource.
- **Off-Mesh Connections** — explicit links between areas the mesh wouldn't otherwise connect (a ladder to a rooftop, a bridge). Each has a start/end point adjustable via a **viewport gizmo**, so links are quick to set up and reposition as the scene evolves.
- **Generation Parameters** — control how scene geometry is sampled, e.g. `cell size` (the voxel size during sampling): smaller captures finer detail, larger gives a more approximate mesh. Many more parameters exist (see developer.apple.com docs).

Once set up, the Navigation Mesh is usable from a **Behavior Tree, Animation Graph, or your own
custom Swift system** through the **navigation component** — the village character navigates to a
tapped location autonomously, avoids obstacles, and even climbs a ladder to a rooftop via an
off-mesh connection. For the code side, see "Explore Advances in RealityKit" (Dennis).

## 5. Compute Graph — GPU particle simulation

Compute Graph is a visual, node-based tool for **GPU-driven particle simulations backed by Metal**,
directly in Reality Composer Pro, with full control over spawning, simulation, and rendering, plus
custom shaders. It's organized into **four phases**:

1. **Emitter Phase** — how/when particles are born (continuous, bursts, or single shot).
2. **Initialize Phase** — runs once per particle at birth; sets starting velocity, lifetime, size.
3. **Simulate Phase** — runs every frame; applies forces like gravity and turbulence.
4. **Output Phase** — controls on-screen appearance as particles age and move.

The cauldron smoke effect (every Compute Graph starts from this four-phase template):

- **Emitter:** `Continuous Emit` — dense, rolling flow while bounding per-frame spawn count.
- **Initialize:** set size (with random variation) → randomize lifetime (so particles don't all vanish at once) → `Spawn in Sphere` (a **custom node from a Compute Graph bundle**) → `Set Position` clamping Y to 0, flattening the spawn into a circle on the sphere's surface (smoke rising from the liquid surface).
- **Simulate:** a **negative gravity** force makes smoke drift upward like rising steam.
- **Output:** particles fade in/out over lifetime, scale down as they rise, and shift color by height. Rendered with a **Shader Graph material** (a circle drawn on a billboard for a rounder look).

You can write custom Compute Graph nodes using a bundle (the talk points to a link for this).

## 6. Shader Graph enhancements

Shader Graph gets several updates this year. The marquee one is the **Subsurface Scattering**
effect (seen improving the realism of the ice in the Jupiter environment). Beyond that:

- **RealityKit PBR Surface 2** — expands the original RealityKit PBR surface node with new properties: **sheen**, **subsurface scattering**, plus more accurate **diffuse and occlusion** shading.
- **Hair Surface** — a dedicated surface shader for hair and fur; models how light reflects along and scatters through fine strands.
- **Portal Surface** + **Portal Geometry Modifier** — modify per-pixel opacity of a portal surface and drive vertex-animated portal geometry, for more flexible portal looks and behavior.

New to Shader Graph? See "Explore materials in Reality Composer Pro" (Niel).
