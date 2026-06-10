# Session 234 — Design immersive environments for visionOS apps and the spatial web

- **URL:** https://developer.apple.com/videos/play/wwdc2026/234/
- **Duration:** 16m
- **Speakers:** Michael Breymann (Human Interface Designer, Apple — system environments for visionOS)

## Description

In visionOS, system environments are photo-realistic natural landscapes designed for
spatial computing. Unlike flat media or a static panorama, they offer true depth and
parallax, and — combined with motion and audio — make a scene feel alive. This is a
design-craft talk (no code/API): Michael walks through the three stages of building
your own immersive environment — pre-production, production, and post-production —
using six shipping visionOS environments as worked examples: Mount Hood, the Moon,
Jupiter, Yosemite, Thorsmork, and Bora Bora.

## Key topics

- **Environments vs. panoramas/flat media** — environments respond to perspective with
  depth and parallax; panoramas/flat media do not.
- **Pre-production** — establish intent (why this place, what qualities, how the viewer
  uses it); scout real locations or gather reference (Apollo photography for the Moon,
  DEM lighting studies for Yosemite); plan in layers (background → foreground); flag
  motion + spatial-audio targets early.
- **Field of view** — viewers see ~81° of the scene when fully immersed; environments
  are 360°.
- **Production / capture** — tripod, camera leveled at 1 m, deep depth of field; optional
  second camera at 2 m fired simultaneously; 360° rig + stitched panorama; bracketed
  exposures for dynamic range; lighting reference (Macbeth charts, chrome + gray
  spheres); secondary photogrammetry/LiDAR point clouds for measurements; motion video
  + sound notes for shaders/audio.
- **Resolution target** — sharp at **40 px/degree** → ideal **14,400 × 7,200 px** 360°
  panorama.
- **Rendered panoramas** — when real capture isn't possible, render in a DCC tool for
  full control.
- **Post-production / cleanup** — remove rig, footprints, people; matte-painting + CG to
  fix busy/dominant scene elements; mind color balance + lighting consistency.
- **Fidelity techniques** — transfer panorama to textures in UV space over a 3D mesh
  (for parallax); fill unseen areas with secondary photography / CG; A/B against the
  panorama; consistent sharpness for scale; flop the scene and push extreme gamma/gain
  to catch inconsistencies and data loss.
- **Sound + motion** — spatial-audio emitters (e.g. rippling-water emitter by the river);
  power-efficient custom shaders for motion.
- **Bora Bora rendering tricks** — UV flow maps for cloud/wind motion; scrolling masks
  for cloud shadows; pre-rendered flip-book textures for palm-tree shadows; hierarchical
  vertex animation + layered sine waves (low + high frequency) for non-repetitive sway;
  layered normal maps + scrolling textures + HSB modulation for water/subsurface
  scattering. Theme: offload to precomputed data textures, "less is more."

## Related sessions to fetch (referenced in this talk)

- [ ] Optimize your custom environments for visionOS (named at 10:58 — real-time rendering deep dive)
- [ ] Spatial audio talk #1 (unnamed — "two excellent talks" on spatial audio, ~11:32)
- [ ] Spatial audio talk #2 (unnamed — second of the two spatial-audio talks, ~11:32)

## Chapter summary (Summary tab)

- **0:00 Introduction** — What immersive environments are and how they differ from
  panoramas and flat media; preview of designing photorealistic natural landscapes for
  apps and the spatial web. Six example environments named.
- **1:17 Pre-production** — Questions to identify the environment you want; scouting
  local and faraway locations; identifying items to omit; planning for production
  (intent, reference, layering, motion/audio, iteration).
- **5:07 Production** — Capturing high-quality on-location photography for 3D asset
  creation; tips for primary + secondary photography, solo or with a team and advanced
  resources; resolution/exposure/rig guidance; rendered panoramas as an alternative.
- **8:41 Post-production** — Process source into a high-res panorama; cleanup; designing
  the 3D asset; maintaining photorealistic fidelity (UV texture transfer, A/B, flop,
  gamma/gain); incorporating motion and spatial audio; Bora Bora shader techniques.
- **15:07 Next steps** — Make intentional choices; build compositions that work; tie the
  scene together with sound and motion; try to break your own designs.

## Code

See `code.md` — this is a design talk with **no Code tab** and no concrete API names
spoken; `code.md` records the technical/craft parameters instead.
