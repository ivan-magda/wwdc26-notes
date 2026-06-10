# Session 280 — Iterate your spatial scenes faster with Reality Composer Pro 3

- **URL:** https://developer.apple.com/videos/play/wwdc2026/280/
- **Duration:** 17m
- **Speakers:** JP (engineer on Reality Composer Pro, Apple)

## Description

Reality Composer Pro 3 is rebuilt from the ground up for fast, iterative, and
collaborative spatial content workflows, and is now a standalone download from
developer.apple.com (no longer an Xcode developer tool). The session walks through the
editor's core entity-component model and demonstrates a set of brand-new capabilities —
the Compute Graph for GPU simulations, a prototype/instancing system, Live Preview on
Apple Vision Pro, Lightmaps for baked indirect lighting, and the AI-powered Reality
Composer Pro Assistant — by adding features to the interactive game Chaparral Village.

## Key topics

- Reality Composer Pro 3 is a **standalone app** — download from developer.apple.com, launch from Applications; no Xcode required
- **Entity-component model** — entities nested in the hierarchy, components (Transform, Point Light, physics, audio, etc.) added via Add Component
- **Compute Graph / Compute Simulation component** — node-based GPU programming for particle systems through fluid simulations; only runs during the simulation stage
- **Simulation tab** — dockable next to the scene tab so you can keep authoring while the game runs; real-time preview of physics, script graphs, animations with no deployment step
- **Prototypes & instancing** — drag an entity to the Project Browser to make a reusable prototype; instantiate, override per-instance, reset overrides, or propagate overrides back to source
- **Live Preview** — target a simulation to a connected Apple Vision Pro via a companion visionOS app; author on the Mac and see updates instantly on device (ships later this year)
- **Lightmaps** — bake indirect lighting, ambient occlusion, and beauty into a texture for static scenes; Lightmap component with bake-quality settings and a Lightmap Preview tab
- **Reality Composer Pro Assistant** — AI assistant in the right panel that uses generative models to create 3D objects and materials from natural-language prompts, and answers RCP questions
- **Physical space lighting** — new fill-light feature shown during Live Preview

## Related sessions to fetch (referenced in this talk)

- [ ] Meet Reality Composer Pro (WWDC23) — basics of the editor
- [ ] Supercharge your spatial workflows with Reality Composer Pro 3 — deep dive on Compute Graph
- [ ] (general) Reality Composer Pro sessions — pointer at the end, no specific titles

## Chapter summary (Summary tab)

- **0:00 Introduction** — JP, engineer on Reality Composer Pro. Overview of RCP 3 as a standalone tool rebuilt for fast, iterative, collaborative spatial workflows; agenda: entities/components, prototypes/instances, Live Preview, Lightmaps, the Assistant.
- **2:25 Overview** — RCP 3 is now a standalone download from developer.apple.com (no longer an Xcode tool). Tour of the Chaparral Village "Alchemy Area" sample (modeled in Blender, imported as USD).
- **3:57 Entities and components** — entity-component model at the core; add/nest/configure entities and components; Transform, Point Light, and the new Compute Simulation component (Compute Graph) for GPU simulations; simulation tab for real-time authoring.
- **8:45 Prototypes and instances** — make a reusable Prototype from any entity; instantiate across the scene; override or propagate instance properties without altering the source; reset overrides.
- **11:06 Live preview** — target a simulation to a connected Apple Vision Pro for live in-headset preview; physical space lighting; ships later this year.
- **11:57 Lightmaps** — bake indirect lighting, ambient occlusion, and beauty maps for static scenes; Lightmap component, bake settings (low→high), Lightmap Preview tab.
- **14:43 Reality Composer Pro Assistant** — new AI assistant; generative models create 3D objects and materials on demand from natural-language prompts, and answer RCP questions.
- **16:07 Next steps** — download RCP 3, explore sample projects, check the Reality Composer Pro sessions.

## Code

See `code.md` — this session has no Code tab; the talk is a UI/editor demo with no source snippets.
