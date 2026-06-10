# Session 320 — Explore immersive website environments in visionOS

- **URL:** https://developer.apple.com/videos/play/wwdc2026/320/
- **Duration:** 19m
- **Speaker:** Jean — Engineer, visionOS Safari team

## Description

A preview of the immersive API for the web in visionOS Safari. The HTML `<model>`
element pairs with a new JavaScript `requestImmersive()` API and a `:immersive` CSS
pseudo-class to open a virtual environment *around* an existing webpage rather than
replacing it (unlike the Fullscreen API). The session builds two example sites — a
theater ticket-sales experience with an inline seat preview that transitions into the
full theater, and an escape-room app marketing site that skips the inline preview —
then covers optimizations: video docking into the scene, JavaScript-triggered model
animations, window shadow casting, image controls for spatial photos, and asset
performance tuning.

## Key topics

- HTML `<model>` element + `src` USDZ + `environmentmap` (360° HDR for reflections/lighting)
- Immersive API mirrors the Fullscreen API: `requestImmersive()`, `exitImmersive`/dismiss, `document.immersiveEnabled`, `document.immersiveElement`, `immersivechange`/error events, `:immersive` CSS pseudo-class
- Immersive opens an environment *around* the page (page stays visible); can run alongside an active Fullscreen video
- Inline preview via `entityTransform` (a `DOMMatrix`) to override default fit-to-bounds scaling; eye-level translation; per-seat rotation/translation from a JSON seat map
- Right-handed Y-up coordinate system (web convention)
- Inline vs immersive reference frames differ: inline origin = center of layer, CSS scale; immersive origin = person's feet on the floor, real-world scale
- Immersive environment opens from behind Safari's window — keep the focal point visible
- Listen to `immersivechange` to recompute transform and update page layout (exit affordance); Digital Crown can dismiss at any time
- Escape-room pattern: `display: none` on `<model>` hides inline layer but still allows `requestImmersive()`, and defers asset download/decode until entry (saves bandwidth/memory)
- Loading animation around the immersive request for heavy assets
- Video docking: `requestFullscreen()` on a `<video>` docks it onto a tagged surface (TV/projector/billboard) inside the scene; light spill baked onto materials
- RealityKit annotations (non-standard) authored via Reality Composer Pro or a custom Blender plugin/extension: video docking region, light spill bake, Scene Understanding component for shadow receiving
- JavaScript-triggered model animations (`play()`), `currentTime` to scrub a timeline through multiple stages
- Safari window shadow casting via Scene Understanding component on a dedicated low-poly mesh
- Image controls API: add `controls` to `<img>` for native panorama/spatial-photo fullscreen affordance
- Performance: reduce vertex count (drop unseen meshes), reduce entity count (merge), low-poly meshes, simple/unlit baked-lighting shaders, `usdcrush` to compress USDZ textures

## Related sessions to fetch (referenced in this talk)

- [ ] Get started with the HTML Model Element
- [ ] What's new for the spatial web
- [ ] Optimize your custom environments for visionOS
- [ ] Design immersive environments for visionOS apps and the spatial web

## Chapter summary (Summary tab)

- **0:00 Introduction** — Two example sites preview the immersive API in visionOS Safari: a theater ticket-sales experience and an escape-room marketing site, each transporting visitors into a virtual environment with a few lines of code.
- **1:46 Meet the immersive API** — High-level overview: the HTML `<model>` element pairs with `requestImmersive()` and a `:immersive` CSS pseudo-class. Unlike Fullscreen, immersive opens an environment around the existing page rather than replacing its content.
- **4:16 Preview environments inline** — Build the inline portion of the ticket site: load a theater model, let visitors pick a seat by applying a `DOMMatrix` transform to the `<model>` element, and prepare the same model for an immersive transition.
- **7:01 Go immersive** — Transition from inline preview into a full immersive environment. Covers inline vs immersive coordinate systems, `immersivechange` events, dismissing the environment, and skipping the inline preview for the escape-room site.
- **12:04 Optimize the experience** — Polish with RealityKit annotations (Reality Composer Pro or Blender plugin): dock video into a TV, trigger model animations from JavaScript, cast Safari's window shadow via Scene Understanding, and reduce vertex/entity counts for fast loading.
- **17:17 Image controls** — Add a `controls` attribute to an `<img>` element to give an immersive viewing affordance for panoramas and spatial photos — a small markup change pairing naturally with model-based environments.
- **18:09 Next steps** — Try the demos on webkit.org with Apple Vision Pro, file feedback at bugs.webkit.org, and watch "Design immersive environments for visionOS apps and the spatial web" for design principles.

## Code

See `code.md` — 16 snippets extracted from the Code tab (mix of HTML, JavaScript, and a shell command).
