---
title: "Compose advanced graphics effects with SwiftUI — Full Digest"
session: WWDC26 · 322
url: https://developer.apple.com/videos/play/wwdc2026/322/
duration: 18m
speakers: Haotian
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 322 — Compose advanced graphics effects with SwiftUI

## TL;DR

"Advanced" SwiftUI graphics are ordinary modifiers wired together into a
**creative pipeline**: each stage takes data in,
transforms it, and passes it along; the output of one stage feeds the input of the
next. Haotian (UI Frameworks) demonstrates by upgrading a bare-bones podcast
transcript view into an Apple-Music-style "live lyrics" screen, built from four
composable pieces:

1. **Shader effects** — blur the cover art, then drive it through a Metal
   `layerEffect` shader. SwiftUI exposes three shader modifiers (`colorEffect`,
   `distortionEffect`, `layerEffect`) with different inputs and trade-offs. The
   warp uses a sampled **noise texture** for organic per-pixel offsets, then
   **domain warping** (sample the noise twice) for flowing blobs.
2. **Time** — shaders are stateless, so animation needs an external clock.
   `TimelineView(.animation)` feeds an elapsed-time value every frame and the
   pattern flows.
3. **Time-synced transcript** — `Text` in a `LazyVStack`/`ScrollView`; the playback
   timestamp picks the current line (bold vs. faded) and `onChange` + a
   `ScrollViewReader` keep it centered.
4. **Alignment guides** — a floating timestamp is pinned to a line's edge with a
   semantic `alignmentGuide` override instead of a manual `.offset`.

The closing message: the APIs are fixed; the creativity is in *what you feed in*
and *how you connect the pipes*. Swap audio for gyroscope, a twist for a ripple, a
scroll view for a freeform canvas.

---

## 1. The pipeline mental model (0:00–4:11)

SwiftUI's "progressive disclosure" means each modifier/API already works on its own.
You get advanced effects by **connecting** those independent pipes — branching and
merging the flows. "The advanced lies in the construction, not the complexity."

Decomposing the target podcast design:

- The existing UI already holds all the data needed: **cover art**, **playback
  info**, **transcript text**. The data is already there; the work is *transforming
  it*.
- **Cover art → shader pipe → visualizer.** Merge in a **time pipe** so the
  visualizer animates with playback.
- **Transcript text → timestamp overlays**, then merge the **same time pipe** to get
  time-synced scrolling.
- Background (dynamic visuals) and foreground (scrolling transcript) are two parallel
  pipes connected into one screen.

The finished design = full-screen cover art (shader + time-driven animation) behind a
time-synced scrollable transcript refined with a floating-view attachment.

## 2. Cover art and shader effects (4:11–11:07)

### Blur first

The cover art sits behind the transcript, so soften it so it doesn't compete:

```swift
Image("CoverArt")
    .blur(radius: 30)
```

### What a shader is

A view starts as vectors, is rasterized by the GPU to pixels, and a **shader** is a
GPU program that decides each pixel's color. It runs **in parallel, per pixel, with
no awareness of neighbors** — which is exactly why a Metal shader maps cleanly onto
SwiftUI's shader-effect modifiers.

### Three shader-effect modifiers

| Modifier | Shader receives | Returns | Good for |
| --- | --- | --- | --- |
| `colorEffect` | pixel position + original pixel color | a new color | per-pixel recoloring (e.g. desaturate to B&W) |
| `distortionEffect` | a position | a new position SwiftUI samples from | geometric warps (e.g. shear) |
| `layerEffect` | position + the whole view as a `SwiftUI::Layer` | a sampled color | anything needing neighbors/region (e.g. blur) — most flexible |

Each has required parameters; you can append extra ones to forward data from SwiftUI.
For the warp, `distortionEffect` would work but `layerEffect` gives the most freedom.

### Pass-through shader as a starting point

```swift
GeometryReader { proxy in
    CoverArtView()
        .layerEffect(
            ShaderLibrary.backgroundWarp(),
            maxSampleOffset: .zero
        )
}
.ignoresSafeArea()
```

```cpp
[[stitchable]] half4 backgroundWarp(
    float2 position, SwiftUI::Layer layer
) {
    return layer.sample(position);
}
```

This returns the original image — a scaffold to build on.

### Uniform offset

Add a `float2 offset` parameter and sample from a shifted position. From the SwiftUI
side, pass `.float2(...)`. A uniform offset shifts every pixel identically, so you get
a fixed displacement, not organic motion.

```cpp
[[stitchable]] half4 backgroundWarp(
    float2 position, SwiftUI::Layer layer,
    float2 offset
) {
    return layer.sample(position + offset);
}
```

### Organic variation with a noise texture

Pass the **view size** and a precomputed **NoiseTexture** (smooth random values).
SwiftUI forwards the image with `.image(_)`; on the Metal side it arrives as
`texture2d<half>`. Compute `uv = position / size` to sample without absolute
coordinates, then use the noise's **red and green channels as the X/Y offset** — a
different offset per pixel.

```swift
GeometryReader { proxy in
    CoverArtView()
        .layerEffect(
            ShaderLibrary.backgroundWarp(
                .float2(proxy.size),
                .image(Image("NoiseTexture"))
            ),
            maxSampleOffset: .zero
        )
}
.ignoresSafeArea()
```

```cpp
[[stitchable]] half4 backgroundWarp(
    float2 position, SwiftUI::Layer layer,
    float2 size, texture2d<half> noiseTex
) {
    constexpr sampler s(address::repeat, filter::linear);
    float2 uv = position / size;

    half4 n = noiseTex.sample(s, uv);
    float2 offset = (float2(n.r, n.g) - 0.5) * 200.0;

    return layer.sample(position + offset);
}
```

The `repeat` sampler tiles the noise; the `- 0.5` recenters the offset, and `* 200.0`
scales it.

### Domain warping

For something richer, sample the noise **twice**: the first sample gives an initial
offset `q`, the second samples at `uv + q`. This layered technique is **domain
warping** and yields flowing, blob-like motion.

```cpp
[[stitchable]] half4 backgroundWarp(
    float2 position, SwiftUI::Layer layer,
    float2 size, texture2d<half> noiseTex
) {
    constexpr sampler s(address::repeat, filter::linear);
    float2 uv = position / size;

    half4 n = noiseTex.sample(s, uv);

    float2 q = float2(n.r, n.g);
    n = noiseTex.sample(s, uv + q);

    float2 offset = (float2(n.r, n.g) - 0.5) * 200.0;

    return layer.sample(position + offset);
}
```

The sample app ships a preview so you can tweak the parameters live.

## 3. Driving animation with time (11:07–12:00)

Unlike SwiftUI's transaction-based animation, **shaders are stateless** — no memory
of the previous frame; output depends only on the parameters. To animate, pass in a
value that changes over time. `TimelineView(.animation)` fires every frame with a
timestamp; compute elapsed time and forward it as another shader argument so the warp
pattern flows.

```swift
@State private var startDate = Date.now

TimelineView(.animation) { timeline in
    let elapsed = timeline.date.timeIntervalSince(startDate)
    CoverArtView()
        .layerEffect(
            ShaderLibrary.backgroundWarp(
                .float2(proxy.size),
                .image(Image("NoiseTexture")),
                .float(elapsed)
            ),
            maxSampleOffset: .zero
        )
}
```

## 4. Time-synced transcript view (12:00–13:18)

The transcript is `Text` views in a `LazyVStack` inside a `ScrollView` — each line its
own view. To follow playback: use the playback timestamp to find the current line
(bold and clear; the rest fades), and use `onChange` plus a `ScrollViewReader` to keep
that line centered.

```swift
@State private var playback = PlaybackState()

ScrollViewReader { scrollProxy in
    ScrollView {
        LazyVStack(alignment: .leading, spacing: 12) {
            ForEach(sampleTranscript) { line in
                Text(line.text)
                    .transcriptLineStyle(isCurrent:
                        line.id == playback.currentLineIndex
                    )
            }
        }
    }
    .onChange(of: playback.currentLineIndex, { _, i in
        scrollProxy.scrollTo(i, anchor: .center)
    })
}
```

## 5. Floating timestamps with alignment guides (13:18–16:16)

Each line carries a timestamp in its `overlay`, but only the current line's is
visible — so it's always laid out and never disturbs the layout. Goal: float the
timestamp at the line's edge. A manual `.offset` can't do this cleanly because it
would need both views' sizes.

Instead, lean on SwiftUI's alignment system. Every view has an alignment point (both
axes). Placing a subview in an overlay "pins" the two views together at their
alignment points — picture a pin punching through both at the same point.

- Default `overlay { }` pins at **center**.
- `overlay(alignment: .bottomLeading)` pins at each view's **bottom-leading** point.

```swift
Text(line.text)
     .overlay(alignment: .bottomLeading) {
          Text(line.formattedTimestamp)
     }
```

To make the subview *float above* the container's bottom edge, override what its
**bottom** alignment means: remap the bottom guide to the view's **top** edge. When the
pin comes through, it follows that overridden point — purely semantic, no manual
offset.

```swift
Text(line.text)
     .overlay(alignment: .bottomLeading) {
          Text(line.formattedTimestamp)
              .alignmentGuide(.bottom) { $0[.top] }
     }
```

You can also define custom alignments; the `alignmentGuide` closure provides
`ViewDimensions` so you can compute a point from the view's actual size. (See the
"SwiftUI Alignment" documentation.)

## 6. Creative pipelines (16:16–end)

Stepping back: take a design, break it into layers, pick the right API per layer to
turn raw data into views, and chain stages so each output feeds the next input. The
podcast app's choices were just one path. The same pipeline generalizes:

- Input: **gyroscope data** instead of audio.
- Shader: a **ripple** instead of a twist.
- Foreground: a **freeform canvas** instead of a scroll view.

The APIs stay the same; the creativity is what you feed in and how you connect the
pipes. Download the sample project, change the noise, tweak the speed, try a different
image.
