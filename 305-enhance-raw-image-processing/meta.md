# Session 305 — Enhance RAW image processing with Core Image

- **URL:** https://developer.apple.com/videos/play/wwdc2026/305/
- **Duration:** 16m
- **Speaker:** David Hayward (Core Image)

## Description

Enhancements to Core Image and its support for RAW image files across Apple
platforms. The headline is **RAW 9**, Apple's biggest update to its RAW pipeline,
a tiled Core ML model that combines demosaic and denoise and runs on the Apple
Neural Engine. The session covers how to opt in via `CIRAWFilter`, performance
best practices for interactive editing and batch export, and two new features
added to the `CIImageProcessor` API.

## Key topics

- RAW processing stages — parse/unpack mosaic → demosaic → denoise → convolution (sharpen + local contrast) → color/tone/white-balance adjustments
- Built-in RAW support reaches every app using **Image IO**; advanced editing controls via **`CIRAWFilter`** (Photos, Pixelmator Pro, Nitro, Acorn, …)
- Pipeline history: 21 camera models in 2006 → **784 models** today; pipeline updated 8 times, now **RAW 9** (the 9th version)
- **RAW 9** — tiled Core ML model combining demosaic + denoise, ANE-accelerated; sharper, more accurate color, far better high-ISO noise reduction
- Opt-in via `CIRAWFilter`: check `supportedDecoderVersions` for `.version9`, set `decoderVersion`; `supportedCameraModels(...)` class method; DNG-native cameras (iPhone) auto-supported; camera list grows over the air
- 20 calibrated editing properties; top four: `exposure`, `luminanceNoiseReductionAmount`, `sharpnessAmount`, `contrastAmount`
- Properties retired/no-op in RAW 9: `colorNoiseReductionAmount` (no effect), `detailAmount`, `moireReductionAmount` (unsupported) — guard with the `isSupported` checks
- Performance — interactive editing: `scaleFactor`, one `CIContext` per view with `cacheIntermediates: true`, Extended Virtual Addressing entitlement, render to Metal-backed `MTKView`
- Performance — export: `cacheIntermediates: false`, raise `memoryLimit` (default 256 MB on iOS → 512/1024), use `heifRepresentation`/`jpegRepresentation`
- New `CIImageProcessor` features: explicit output tile sizes (`apply(withTiledExtent:)`) and recyclable temporary buffers (`output.temporaryPixelBuffer(...)`)

## Related sessions to fetch (referenced in this talk)

- [ ] Capture and process ProRAW images (WWDC 2021)
- [ ] Display EDR content with Core Image, Metal, and SwiftUI (WWDC 2022)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Enhancements to Core Image and its support for RAW image files across Apple platforms; four topics.
- **0:52 How Core Image supports RAW** — overview of the RAW processing stages (demosaic, denoising, convolution, color adjustments) and their support in Apple's OSes and developer frameworks (Image IO, CIRAWFilter).
- **2:48 The evolution of RAW support** — how Apple's RAW processing pipeline has evolved across nine versions, now supporting 784 camera models.
- **3:33 RAW 9 overview** — the new RAW 9 pipeline leverages a Core ML model to significantly improve demosaic and denoise for the highest image quality (ANE-accelerated).
- **3:56 RAW 9 quality improvements** — side-by-side RAW 8 vs RAW 9 comparisons (Sony A7 II, Canon 5D Mark III, Fujifilm X-T5) showing sharpness, color accuracy, and noise-reduction gains.
- **5:50 Enable and edit RAW 9 with CIRAWFilter API** — opt in via `decoderVersion`, check `supportedCameraModels`, and use CIRAWFilter editing properties.
- **8:33 RAW 9 performance overview** — RAW 9 is more resource intensive (Core ML runs hundreds of times per image), but cached intermediates keep edits responsive.
- **9:19 Interactive editing** — render one RAW repeatedly at screen resolution: `scaleFactor`, `cacheIntermediates`, Extended Virtual Addressing entitlement, Metal-backed `MTKView`.
- **10:52 Exporting to other formats** — export many RAWs at full res to HEIF/JPEG: disable `cacheIntermediates`, tune `memoryLimit`, use `heifRepresentation`/`jpegRepresentation`.
- **11:50 New CIImageProcessor features** — explicit output tile sizes and temporary buffers.

## Code

See `code.md` — 3 snippets extracted from the Code tab.
