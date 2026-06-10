# Session 322 — Compose advanced graphics effects with SwiftUI

- **URL:** https://developer.apple.com/videos/play/wwdc2026/322/
- **Duration:** 18m
- **Speakers:** Haotian (UI Frameworks team)

## Description

A way of thinking about advanced graphics and layout in SwiftUI as a *creative
pipeline* — a series of stages that take data in, transform it, and pass it along.
Working from a podcast app's existing UI (cover art, playback info, transcript
text), the session decomposes a finished "live lyrics" design into pipeline stages:
a shader pipe turns the cover art into an animated visualizer, a time pipe drives
the motion, and another time pipe syncs the transcript scrolling. Along the way it
covers SwiftUI's three shader-effect modifiers, Metal domain-warping, `TimelineView`,
time-synced scrolling, and alignment guides for floating overlays.

## Key topics

- The "creative pipeline" mental model — every modifier/API is a stage; output of one feeds the next; connect, branch, and merge them.
- Softening cover art with `.blur(radius:)` before layering effects.
- Three shader-effect modifiers and their trade-offs: `colorEffect` (per-pixel color in → color out), `distortionEffect` (position → new sample position), `layerEffect` (samples the whole layer; most flexible).
- Writing a Metal `[[stitchable]]` `backgroundWarp` shader; forwarding `float2` and `Image`/texture parameters from SwiftUI.
- Organic per-pixel offsets from a sampled `NoiseTexture` (red/green channels as X/Y offset); UV coordinates via `position / size`.
- Domain warping — sampling the noise twice (second sample offset by the first) for flowing blobs.
- Animating a stateless shader by feeding elapsed time from `TimelineView(.animation)`.
- Time-synced transcript: `Text` in a `LazyVStack` inside `ScrollView`; `ScrollViewReader` + `onChange` to center the current line; bold/fade styling by current index.
- Floating timestamp via `overlay(alignment:)` + `alignmentGuide(.bottom) { $0[.top] }` — a semantic alignment override instead of a manual `.offset`.
- Generalizing the pipeline: swap audio for gyroscope, a twist for a ripple, a scroll view for a freeform canvas.

## Related sessions to fetch (referenced in this talk)

- [ ] SwiftUI Alignment documentation (referenced as a doc, not a session)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Advanced graphics/layout in SwiftUI as a creative pipeline: stages that take data in, transform it, pass it along.
- **1:40 Design breakdown** — Decompose a finished podcast-app design (cover art, playback info, transcript) into pipeline stages: a shader pipe → visualizer, a time pipe → motion, another time pipe → transcript scrolling.
- **4:11 Cover art and shader effects** — Blur the cover art, then layer shader effects. Shaders run per pixel on the GPU; SwiftUI exposes them via three modifiers (color, distortion, layer). Build a `layerEffect` "background warp" shader that samples a noise texture for organic per-pixel offsets (domain warping).
- **11:07 Driving animation with time** — Shaders are stateless; time must come from outside. `TimelineView` fires every frame with a timestamp passed into the shader; the warp pattern flows as time advances.
- **12:00 Time-synced transcript view** — `Text` views in a `LazyVStack` inside a `ScrollView`. Use the playback timestamp to highlight the current line and fade the rest; `onChange` scrolls the current line to center.
- **13:18 Floating timestamps with alignment guides** — Position a timestamp on the edge of the current line without manual offsets. SwiftUI's alignment system pins views together at their alignment points; `alignmentGuide` overrides an alignment semantically (move the subview's bottom guide to its top edge so it floats outside its container).
- **16:16 Creative pipelines** — Each stage's output becomes the next stage's input. The approach generalizes: swap audio for gyroscope data, a twist shader for a ripple, a scroll view for a freeform canvas.
- **17:13 Next steps** — Download the sample project, experiment with the shader, find spots in your own app where a small visual effect makes a big difference.

## Code

See `code.md` — 17 snippets extracted from the Code tab (SwiftUI + Metal).
