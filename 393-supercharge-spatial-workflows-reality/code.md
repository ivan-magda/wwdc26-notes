# Code samples — Session 393

_No Code tab for this session._ It is a live walkthrough of the Reality Composer Pro 3
visual editors, so there are no copy-paste snippets. Below are the concrete nodes,
parameters, and APIs named on screen, organized by tool.

## Animation Graph — node graph (no source)

- **Final Pose** node (default root; whatever pose flows in is what the character displays)
- **State Machine** node → contains **Animation State** nodes named `Idle` and `Walk`
- Transitions: `Idle → Walk` and `Walk → Idle`, each gated by a **Bool Condition**
  - `Idle → Walk`: `isWalking == true`
  - `Walk → Idle`: `isWalking == false`
- Input (Inputs Inspector): `isWalking` — Boolean, set at runtime
- **Animation Clip** nodes feed each State Machine state input (Idle clip, Walk clip)

## Behavior Tree — node graph (no source)

Evaluation order: top-to-bottom, left-to-right; higher and leftmost nodes run first.

- **Composite nodes:** `Sequence` (runs children in order; stops on first failure),
  `Selector` (runs until one child succeeds, then stops), `Parallel` (runs all children
  at once)
- **Action nodes used:** `Rotate To Face`, `Move To`, `Wait` (set to 1s), `Parameter Setter`
- Table sub-sequence (Sequence): `Parameter Setter (isWalking = true)` → `Rotate To Face` →
  `Move To` → `Parameter Setter (isWalking = false)` → (originally `Wait`, later deleted)
- Cauldron sub-sequence: same structure pointing at `cauldronPosition`, gated by a
  **Precondition → Bool Condition** on `readyToBrew`
- Parent **Sequence** wires table sub-sequence then cauldron sub-sequence
- Inputs: `tablePosition`, `cauldronPosition`, `rotationRate`, `movementRate`,
  `isWalking`, `readyToBrew` (Boolean, default `false`)

## Script Graph — node graph (no source)

- **On Initialize** node — fires once when the Scripting component initializes (setup logic)
- **Setup Behavior Tree Position** subgraph — reusable function-like graph; finds table &
  cauldron entities and writes their world positions to the entity parameter; connected to
  On Initialize's event output
- **On Tap** node — listens for tap gesture events on the entity
- **Set Entity Parameter** node — name field `readyToBrew`, toggle set to `true`; connected
  to On Tap's event output (matches the Behavior Tree's `readyToBrew` precondition)

## Navigation Mesh — component (authored, used from code via navigation component)

- **Shapes** section — bounding box defining which scene geometry is included in mesh generation
- **Off-Mesh Connections** section — start/end points (adjustable via viewport gizmo) to link
  non-contiguous surfaces (e.g. a ladder to a rooftop, a bridge)
- **Generation Parameters** section — e.g. `cell size` (voxel size used during sampling;
  smaller = finer detail, larger = more approximate)
- Consumed by Behavior Tree, Animation Graph, or a custom Swift system through the
  **navigation component**

## Compute Graph — four-phase template (no source)

- **Emitter Phase:** `Continuous Emit` node (dense, rolling flow; bounded per-frame spawn)
- **Initialize Phase:** set size (with random variation) → randomize lifetime →
  `Spawn in Sphere` (custom node from a Compute Graph bundle) → `Set Position` (clamp Y to 0,
  flattening spawn to a circle on the sphere surface)
- **Simulate Phase:** negative gravity force (smoke drifts upward)
- **Output Phase:** fade in/out over lifetime, scale down while rising, shift color by height;
  rendered with a **Shader Graph material** (circle-on-billboard for a rounder look)

## Spoken APIs / surfaces

- `RealityKit PBR Surface 2` — Shader Graph surface node adding sheen, subsurface scattering,
  more accurate diffuse/occlusion shading
- `Hair Surface` — dedicated surface shader for hair/fur strand lighting
- `Portal Surface` + `Portal Geometry Modifier` — per-pixel opacity and vertex-animated portal geometry
- Subsurface Scattering effect (seen in the Jupiter environment ice)
- Navigation: navigation controller + navigation component (RealityKit)
- Live Preview via the Reality Composer Pro Companion App + Mac Virtual Display on Apple Vision Pro
