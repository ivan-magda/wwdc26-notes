---
title: "Iterate your spatial scenes faster with Reality Composer Pro 3 — Full Digest"
session: WWDC26 · 280
url: https://developer.apple.com/videos/play/wwdc2026/280/
duration: 17m
speakers: JP (Reality Composer Pro engineer, Apple)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 280 — Iterate your spatial scenes faster with Reality Composer Pro 3

## TL;DR

Reality Composer Pro 3 is rebuilt around one goal: **cut the friction between an idea
and seeing it run in a spatial scene.** Five things carry that message:

1. **Standalone app** — RCP 3 is no longer an Xcode developer tool. Download it directly
   from developer.apple.com and launch from Applications. You can get far in development
   "without the need to touch Xcode."
2. **Entity-component model + Compute Graph** — the familiar entity/component editor now
   includes a **Compute Simulation** component backed by node-based **Compute Graphs**
   that put GPU programming (particles → fluids) in reach without writing shaders.
3. **Prototypes & instancing** — a new reusable-asset system: author once, instantiate
   many, override per-instance, and reset or propagate overrides. Nothing is permanently
   changed unless you want it to be.
4. **Live Preview on Apple Vision Pro** — author on the Mac and see edits reflected
   **instantly in-headset** via a visionOS companion app. "What you see is truly what you
   get." (Ships later this year.)
5. **Lightmaps + the RCP Assistant** — bake indirect lighting / ambient occlusion /
   beauty for static scenes, and use an in-editor **AI assistant** that generates 3D
   objects and materials from natural-language prompts.

The whole session is a live demo: building features into the "Alchemy Area" of the
sample game **Chaparral Village** (assets modeled in Blender, imported as USD).

---

## 1. Standalone tool + project overview

- RCP 3 is **no longer shipped inside Xcode** — it's a standalone download from
  developer.apple.com, launched from the Applications folder. The framing throughout is
  "get further along without touching Xcode," aimed at fast/iterative/collaborative work.
- For editor basics, the talk points back to **"Meet Reality Composer Pro" (WWDC23)**.
- The demo project is the **Alchemy Area** of **Chaparral Village**. All objects were
  modeled in **Blender**, exported as **USD**, and laid out in RCP. Focus Mode (View
  menu) is used to explore.

## 2. Entities and components

- Assets are imported via the **Project Browser's import-asset icon** (here, a Cauldron
  USD). On import, a USD file is organized and optimized into an **import bundle** that
  can be expanded to inspect geometry, materials, textures, and more.
- Dragging the bundle into the viewport creates an **entity** with a **Transform
  Component** shown in the inspector. Entities + components are "the core building blocks
  of everything you create in Reality Composer Pro 3."
- The **hierarchy panel** lists all entities; they can be re-ordered and nested
  (cauldron dragged under the fireplace). Transform values position/angle the entity.
- **Add Component** offers lights, physics, audio, and more. The demo builds a "Magic
  Effect" child under the Table, with a "Glow" child carrying a **Point Light** component
  (tuning position, attenuation, color, intensity).

### Compute Graph

- The new **Compute Simulation** component is added to the Magic Effect; in the inspector
  a **Compute Graph picker** lists the project's graphs ("Magic Graph", "Brewing Graph").
- **Compute Graph makes GPU programming accessible** — node-based graphs span simple
  particle systems to complex fluid simulations.
- A Compute Graph **only runs during the simulation stage**, so it's invisible until you
  press **Play** in launch control.
- Deep dive: **"Supercharge your spatial workflows with Reality Composer Pro 3."**

### Simulation tab

- Pressing Play runs the Alchemy Area and shows the Compute Graph simulating. The
  **simulation tab can be docked next to the scene tab** so you can **keep authoring
  while the game runs** — e.g., placing the Magic Effect into a bowl and tweaking the
  graph's "twist amount" live.
- The pitch: from physics to script graphs to animations, everything authored in RCP can
  be previewed in real time with **no deployment process** in the way.

## 3. Prototypes and instances

- **Prototypes** are a new system for reusable objects. Drag an entity from the hierarchy
  into the **Project Browser** to create a **prototype asset**.
- Drag the prototype into the viewport to **instantiate** it (the demo makes a "Brewing
  Effect" instance alongside the original "Magic Effect" — two instances of the same
  prototype).
- **Per-instance overrides** customize an instance (e.g., swap the brewing instance to
  the Brewing Graph; adjust the Glow's color, attenuation, falloff) without altering the
  source.
- **Reset** an override back to its source value via the context menu (demo resets a bad
  Attenuation Falloff edit).
- The mental model: edit content in one place and the system handles the rest —
  instantiate many times, override individually, **reset** to source, or **propagate**
  overrides back to the source. "Nothing is ever permanently changed unless you want it
  to be."

## 4. Live Preview on Apple Vision Pro

- Because the experience targets Vision Pro, you can **target a simulation to any Vision
  Pro currently connected to your Mac** from the launch-control panel, starting a **Live
  Preview** session that opens a **companion app on visionOS**.
- You keep authoring in RCP on the Mac and **see updates reflected instantly** on device.
- The demo highlights a blue fill light using the **new physical space lighting** feature,
  and stresses that authoring on-device gives an instant feel for an effect's spatial
  impact. "This way of live previewing dramatically cuts down on iteration times… What
  you see is truly what you get."
- **Live Preview ships later this year.**

## 5. Lightmaps

- After the lighting edits, the scene's previously generated **indirect lighting** no
  longer matches, so the demo rebakes with **Lightmaps**.
- Indirect lighting captures how light bounces and reaches areas not directly lit (e.g.,
  under the table). Simulating it is costly, but since the Alchemy Area's lights **don't
  move**, the new **lightmapping component** pre-calculates the indirect term and saves it
  to a texture — a **Lightmap**.
- The **Lightmap component** (attached to the Alchemy Area entity) controls which lighting
  term is baked and exposes quality settings under **Bake Settings** (changed low → high).
- The **Lightmap Preview tab** (Tab menu) shows in real time how much indirect lighting
  affects the scene, so you can dial settings in **before committing to a full bake**.
- Three supported lighting terms:
  - **Indirect Lighting** — bounced light filling darker areas.
  - **Ambient Occlusion** — each point's visibility to its surroundings.
  - **Beauty** — the final per-point color, combining indirect *and* direct lighting.

## 6. Reality Composer Pro Assistant

- A new **AI assistant** is always available from the **right panel**. You **prompt it in
  natural language** and it adds content — the demo asks it for a few more items on the
  work bench, then a few candles.
- It uses **powerful generative models to craft 3D objects and materials on demand**, to
  iterate faster and experiment freely.
- It can also **answer Reality Composer Pro questions** directly in the editor.

## Next steps (from the talk)

- Download RCP 3 from developer.apple.com and explore the available **sample projects**.
- Watch the broader **Reality Composer Pro sessions** for everything not covered.
