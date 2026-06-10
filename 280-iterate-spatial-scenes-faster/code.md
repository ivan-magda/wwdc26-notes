# Code samples — Session 280

_No Code tab for this session._ It is an editor/UI demo in Reality Composer Pro 3 with
no source snippets. No concrete public APIs are named in the transcript; the workflow is
entirely GUI-driven (hierarchy, inspector, Project Browser, launch control, tab menu).

## Editor features / artifacts named (for reference)

- **Import bundle** — optimized container created when a USD file is imported (holds geometry, materials, textures)
- **Entity** + **Transform Component** — base building blocks
- **Add Component** — lights, physics, audio, and more
- **Point Light component** — position, attenuation, attenuation falloff, color, intensity
- **Compute Simulation component** / **Compute Graph** — node-based GPU simulations (e.g. "Magic Graph", "Brewing Graph"); runs only in the simulation stage; exposes parameters like "twist amount"
- **Simulation tab** — dockable real-time preview, launched via the Play button in launch control
- **Prototype** asset + instances — created by dragging an entity into the Project Browser; per-instance overrides, Reset (context menu), and propagate-to-source
- **Live Preview** — launch-control option targeting a connected Apple Vision Pro via a visionOS companion app (ships later this year); **physical space lighting** fill-light feature
- **Lightmap component** — bake settings (quality low→high); lighting terms: **Indirect Lighting**, **Ambient Occlusion**, **Beauty**; **Lightmap Preview** tab (Tab menu)
- **Reality Composer Pro Assistant** — generative AI panel for creating 3D objects/materials and answering RCP questions
