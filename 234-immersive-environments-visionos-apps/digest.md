---
title: "Design immersive environments for visionOS apps and the spatial web — Full Digest"
session: WWDC26 · 234
url: https://developer.apple.com/videos/play/wwdc2026/234/
duration: 16m
speakers: Michael Breymann
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 234 — Design immersive environments for visionOS apps and the spatial web

## TL;DR

A visionOS human-interface designer walks through how Apple actually builds its
**immersive system environments** — and how you can build your own for visionOS apps
and the spatial web. The throughline is a three-stage production pipeline borrowed from
film/VFX, illustrated with six shipping environments (Mount Hood, the Moon, Jupiter,
Yosemite, Thorsmork, Bora Bora):

1. **An environment is not a panorama.** It has real depth and parallax over a 3D mesh,
   so it responds to head movement the way the real world does; motion + spatial audio
   complete the illusion.
2. **Pre-production = intent + scouting.** Decide why the place exists and how it's
   used, scout the real location (or gather reference like Apollo photography / DEM
   lighting studies), and plan the scene in layers before capturing anything.
3. **Production = disciplined capture.** Bracketed, high-resolution, leveled 360°
   photography (target 14,400 × 7,200 px), plus secondary photography, lighting charts,
   and photogrammetry/LiDAR for measurements. Or render a panorama in a DCC tool.
4. **Post-production = cleanup + fidelity + life.** Remove unwanted elements, transfer
   to UV-space textures over a mesh, sanity-check fidelity (A/B, flop, extreme
   gamma/gain), then add motion and spatial audio. The Bora Bora section is the
   technically richest: a catalogue of cheap real-time tricks (UV flow maps, scrolling
   shadow masks, flip-book shadows, hierarchical vertex animation + layered sine waves)
   that fake expensive motion within a strict rendering budget.

The closing design philosophy: be intentional, build compositions that "work," tie it
together with sound and motion, and try to **break your own designs** to know they hold up.

---

## 1. What an immersive environment is (0:00)

System environments in visionOS are **photo-realistic natural landscapes designed for
spatial computing** — meant to transport the viewer to another time and place, not just
sit behind content. The key distinction:

- **Flat media / panoramic image** — does *not* respond to changes in perspective.
- **Immersive environment** — offers **true depth and parallax**, so the view shifts
  with the viewer the way the real world would. Adding **motion + audio** makes the
  scene feel alive and immersive.

Six environments anchor the talk as worked examples: **Mount Hood, the Moon, Jupiter,
Yosemite, Thorsmork, Bora Bora.** The pipeline has three stages: pre-production,
production, post-production.

## 2. Pre-production: intent, scouting, layering (1:17)

**Start with intent.** Before building, answer: *Why this environment? What qualities do
we want to bring to life? How will the viewer use the space?* Answering early saves time
and avoids costly production mistakes.

Two contrasting use-case examples show how intent drives design:

- **Media watching** — the team wanted a cinematic feel, so they *sculpted the terrain to
  fit a large screen* and researched the optimal viewing center line.
- **Keynote** (public-speaking practice) — sound is *intentionally absent* to keep the
  speaker focused, and lighting is concentrated on the stage as if presenting to a real
  audience.

Your environment might instead be a **backdrop for app content** or showcase **an
experience from the web** (the "spatial web" in the title).

**Scout the location** if it's a real place (like Mount Hood):

- Decide the viewer's **primary viewpoint** and what's visible if they turn around.
- Note: when fully immersed, viewers see **~81° of the scene** in their field of view
  (the environment itself is 360°).
- Scouting also reveals what to *remove*: the Mount Hood location had a road and dense
  back-side vegetation flagged for replacement; the talk shows before/after panoramas.

When a place isn't physically accessible, lean on **reference material**:

- **Moon** — built from **Apollo-mission photography**.
- **Yosemite** — a lighting study using **Digital Elevation Models** of the valley plus
  Earth's orbit to pick exact capture days/times.

**Plan for the next stages:**

- Visualize the environment in **layers**, background → foreground.
- Identify which elements need **motion**, and what **spatial audio** attaches to them.
- Expect discovery: for **Jupiter**, building a scale model of the solar system (to see
  how the Sun lights Jupiter's moons) led to a new requirement — a system that allows
  the **passage of time** — which became a core design constraint.

**Recap:** be clear on intent, scout + collect reference, plan composition and layering
(and what to remove/change), and iterate.

## 3. Production: capturing usable source imagery (5:07)

Complex 3D scenes take time, so make it easier by capturing **high-quality photography at
the source**. **Yosemite** is the model: the team pre-identified the exact viewpoint
(viewer in the valley with a clear shot into the mid-distance) — ideal framing because
*you can always add foreground detail with CG, but it's much harder to remove things that
obstruct the view.*

Capture discipline matters: seasons, weather, and time of day dramatically change the
shot; lighting shifts fast around sunrise/sunset, so **plan for more time and more
photography than you think you need** — every image becomes 3D-asset source later.

**Capture tips (see `code.md` for the full spec list):**

- Tripod, camera **leveled 1 m off the ground**, **deep depth of field**.
- Optional **second camera 2 m off the ground**, triggered simultaneously — fills
  not-visible parts of the scene in post.
- **360° rig + lens** producing a stitched panorama.
- **Bracketed exposures** for the large dynamic range (sun → shadows).
- Know the target display and over-capture resolution. For visionOS, sharp at
  **40 px/degree** → ideal **360° panorama of 14,400 × 7,200 px**.

**Secondary photography** for reference and measurement:

- **Photogrammetry + LiDAR** point clouds → meshable 3D-asset starting points and
  distance measurements (e.g. discovering "flat" terrain actually slopes, which changes
  how you build assets).
- **Lighting reference** shot at the same time as primary: **Macbeth charts + chrome and
  gray spheres** (so CG integrates seamlessly).
- **Video of moving elements** → reference for believable shaders; note associated
  sounds to source matching audio.

If photography isn't possible, create a **rendered panorama** in your DCC tool — the
upside is total control over the entire scene at any time. And the whole process scales:
small teams can adapt these best practices to limited resources.

## 4. Post-production, part 1 — cleanup (8:41)

With a high-res source panorama plus reference content, you start designing the 3D asset —
but the **first step is cleanup**. Using **Thorsmork's** principal photography:

- Remove the obvious: the **camera rig**, **footprints**, and **people**.
- Apply the pre-production notes: the **mid-ground vegetation was too busy** and certain
  **bushes too dominant** in frame.
- Fix via iterations of **digital matte painting + rendered CG assets**, watching **color
  balance and lighting consistency** throughout.

## 5. Post-production, part 2 — maintaining fidelity (9:42)

Beyond creative choices, the critical technical task is **maintaining visual fidelity**
while preparing the final asset:

- A **3D mesh** is required for parallax/depth, so you move the refined panorama into
  **textures in UV space**.
- Areas not visible in the panorama get filled with **secondary photography + CG renders**.
- **A/B compare** the 3D asset against the panorama to confirm texture transfer keeps
  quality.
- Keep **sharpness consistent** between neighboring elements so scale reads correctly.
- **Flop the scene** to see it fresh (your eyes get accustomed to the same view).
- Push **extreme gamma and gain** to expose color/value inconsistencies and texture-
  transfer data loss — issues that surface differently across displays.

> Real-time rendering deep dive: **"Optimize your custom environments for visionOS"**
> (named at 10:58).

## 6. Post-production, part 3 — sound and motion (11:02)

A textured mesh gives the visual base, but immersion needs **sound and motion**.

- **Spatial audio:** place emitters in the scene — e.g. a **rippling-water emitter** set
  in a part of the river where water flows around large rocks. (Two spatial-audio talks
  are referenced but not named.)
- **Motion:** use **custom shaders** for any moving element — best visuals while staying
  **power efficient**.

### Bora Bora — cheap real-time motion tricks (11:48)

Thorsmork (subarctic) has little motion, so the talk moves to **Bora Bora**, rich with
movement: evolving clouds casting shadows, palm trees swaying, waves breaking differently
each time. Motion and light are among the most expensive things to render, so the team
**achieved the visual intent without breaking the real-time budget**. The techniques
(full list in `code.md`):

- **Sky / clouds:** **UV flow maps** drive continuous, directional cloud motion at little
  cost; **weighting flow speed** across the sky gives a massive sense of depth and scale.
- **Cloud shadows:** not rendered lights — a **scrolling mask that darkens terrain
  textures**, with direction/speed matched to the clouds so they feel connected even
  though they're decoupled.
- **Tree shadows:** dense palm-tree shadows are **pre-rendered into flip-book textures**
  that darken terrain textures — trading soft-shadow rendering cost for a believable
  approximation.
- **Palm fronds:** **greatly reduced mesh complexity** + a **UV flow map** for wind;
  trunks/fronds sway more gently than leaflets via **hierarchical vertex animation +
  layered sine waves**. Stacking **low- and high-frequency motion** yields variety that
  never looks repetitive.
- **Water:** **HSB (hue/saturation/brightness) modulated over time** to simulate
  **subsurface scattering** of sunlight; waves move through **layered normal maps +
  scrolling textures**.

The recurring principle: **less is more** — offload expensive shading to **precomputed
data textures**, and let artful layering/compositing make the sum greater than its parts.
Visual reference is your best tool for articulating exactly which shader features and
controls to build.

## 7. Next steps & design philosophy (15:07)

Closing principles for designing your own environment:

- **Be intentional** — be able to explain the reasoning behind everything you add or
  remove.
- **Create a composition that works** — every element should feel like it belongs.
- **Tie it together with sound and motion.**
- Good design strengthens craft and execution whether you're an individual, a small team,
  or a large studio.
- Final thought: **try to break your own designs.** Don't get attached to one idea; do
  something unexpected and stay receptive — some of your best work comes from surprises
  you embrace.
