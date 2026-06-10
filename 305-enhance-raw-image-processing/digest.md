---
title: "Enhance RAW image processing with Core Image — Full Digest"
session: WWDC26 · 305
url: https://developer.apple.com/videos/play/wwdc2026/305/
duration: 16m
speakers: David Hayward
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 305 — Enhance RAW image processing with Core Image

## TL;DR

Three headline themes:

1. **RAW 9** — Apple's biggest-ever update to its RAW pipeline. A **tiled Core ML model** that fuses **demosaic + denoise**, runs on the **Apple Neural Engine**, and ships in **iOS / iPadOS / macOS / visionOS 27**. Sharper detail, more accurate color, and dramatically better high-ISO noise reduction. Opt in with a few lines of `CIRAWFilter`.
2. **Performance best practices** — RAW 9 is heavier (the Core ML model runs hundreds of times per image), so the talk splits guidance into two use cases: **interactive editing** (cache intermediates, scale down, render to Metal) and **batch export** (don't cache, raise the memory limit, use Core Image's encoders).
3. **Two new `CIImageProcessor` features** — explicit **output tile sizes** and recyclable **temporary buffers**, both fallout from building RAW 9 itself.

The whole thing is delivered by David Hayward, who closes by noting he reprocessed his own 7,000-photo, 20-year RAW library with RAW 9.

---

## 1. How Core Image supports RAW

Unlike HEIF or JPEG, a RAW file can't be displayed directly — it must run through a pipeline:

1. **Parse metadata & unpack sensor values** — each pixel location holds only one of red/green/blue, arranged in a mosaic (Bayer-style) pattern.
2. **Demosaic** — reconstruct full RGB at every pixel.
3. **Denoise** — remove photon, read, and thermal noise.
4. **Convolutions** — sharpen edges and add local contrast.
5. **Color/tone adjustments** — white balance, exposure, color, and tone to produce a pleasing final image.

These algorithms are built into iOS, iPadOS, macOS, and visionOS. Two ways apps get them:

- **Image IO** — any app/framework using Image IO gets RAW *viewing* automatically (Finder, Preview, Freeform).
- **`CIRAWFilter`** — advanced editing controls. Used by Photos, Pixelmator Pro, Nitro, Acorn, and more.

## 2. The evolution of RAW support

- **2006:** hand-tuned calibrations for **21 camera models**.
- **Today:** **784 models** across all major vendors, including **Apple ProRAW** from iPhone.
- The pipeline has been **updated 8 times** (each improving demosaic, denoise, color), with older versions kept available for reproducibility.
- The reprocessing promise: an old photo can be re-rendered with the latest algorithms.

## 3. RAW 9 overview

The 9th pipeline version and "the biggest update yet":

- Built atop a **tiled Core ML model** that **combines demosaic with denoise** for best quality (these were traditionally separate stages).
- Runs **on device on the Apple Neural Engine cores**.

### Quality comparisons (RAW 8 vs RAW 9)

- **Sony Alpha 7 II**, low-noise dial-indicator crop — RAW 9 is sharper/clearer, fine text more legible.
- **Canon 5D Mark III**, ISO **51,200**, 10x crop of a crayon box — RAW 8 acceptably recovered colors; RAW 9 has accurate, well-defined colors and even preserves specular highlights.
- **Fujifilm X-T5**, ISO **12,800**, embroidery yarn — Fuji's non-traditional sensor pattern is hard to demosaic; RAW 8 shows color artifacts and detail loss, RAW 9 keeps text legible and yarn texture clear.

## 4. Enable and edit RAW 9 with CIRAWFilter

RAW 9 is **not on by default** — you opt in:

1. Load the file with `CIRAWFilter` (see "Capture and process ProRAW images," WWDC 21).
2. Check `supportedDecoderVersions` contains `.version9`.
3. Set `decoderVersion` to `.version9`.
4. Query supported cameras with the new class method `supportedCameraModels(...)`, which returns the array of models for a given version.

Notes:

- **Hundreds** of models supported at launch across all major pro vendors; the list **grows over the air** via OS updates.
- Cameras that shoot **DNG natively** (e.g. iPhone) are **automatically** supported.

### Editing properties

`CIRAWFilter` exposes **20 calibrated properties**. The four most important:

- **`exposure`** — brighten/darken.
- **`luminanceNoiseReductionAmount`** — how much fine luma grain is visible.
- **`sharpnessAmount`** — edge sharpening.
- **`contrastAmount`** — local contrast near edges.

All work even better under RAW 9 than before.

**Retired in RAW 9:**

- `colorNoiseReductionAmount` — **no effect** (the Core ML model handles color noise automatically).
- `detailAmount` and `moireReductionAmount` — **no longer needed or supported**.

Guard with the `isSupported` property checks before exposing a control for a given filter instance.

## 5. Performance

RAW 9 is more compute- and resource-intensive — the Core ML model runs **hundreds of times per image**. But once rendered, edits stay responsive because **Core Image caches intermediate results**. Two use cases get separate advice.

### Interactive editing (one RAW, rendered repeatedly at screen resolution)

- Use `CIRAWFilter.scaleFactor` when displaying smaller than full res — avoids rendering more megapixels than the display has.
- **One `CIContext` per view**, with `cacheIntermediates: true` — lets Core Image skip the heavy Core ML work while a property is being scrubbed.
- Add the **Extended Virtual Addressing entitlement** so Core Image can use more memory for between-render caching.
- Render directly to **Metal-backed views (`MTKView`)** — Metal can begin the next frame before the previous one finishes. (See "Display EDR content with Core Image, Metal, and SwiftUI," WWDC 22.)

### Exporting (many RAWs, each rendered once at full res to HEIF/JPEG)

```swift
let exportCtx = CIContext(options : [
  .cacheIntermediate : false,
  .memoryLimit : 512 ])
```

- `cacheIntermediates: false` — no point caching when each file renders once.
- Raise **`memoryLimit`** — iOS default is a conservative **256 MB**; **512** or **1024 MB** can significantly improve performance.
- Use `CIContext.heifRepresentation(...)` / `jpegRepresentation(...)` instead of calling Image IO directly for extra memory savings.

## 6. New CIImageProcessor features

RAW 9 itself is implemented with `CIImageProcessor`, because it lets Core ML run in conjunction with other `CIKernel`s. Building it surfaced two API additions.

### Explicit output tile sizes

A `CIImageProcessor` implements a **region-of-interest** callback (how much input is needed for a given output rect) and a **process** callback that operates on `input.region` / `output.region`. Normally Core Image picks the `output.region` — the whole image if memory allows, smaller tiles when memory is tight. Now you can **explicitly control the tiling**: build an array of tiles covering the image and pass it to `apply(withTiledExtent:)`.

```swift
import CoreImage

class MyProcessor: CIImageProcessorKernel {
    override class func roi(forInput input: Int32,
                            arguments: [String : Any]?,
                            outputRect: CGRect) -> CGRect { return outputRect }

    override class func process(with inputs: [CIImageProcessorInput]?,
                                arguments: [String : Any]?,
                                output: CIImageProcessorOutput) throws {
        guard let input = inputs?.first,
              let iBuffer = input.pixelBuffer,
              let oBuffer = output.pixelBuffer else { return }

        let iRegion = input.region
        let oRegion = output.region // controlled by Core Image
        // MyCopyBuffer(iBuffer, iRegion, oBuffer, oRegion)
    }
}

let extent = inImg.extent
let tileSize = 512.0
var tiles: [CIVector] = []
for y in stride(from: extent.minY, to: extent.maxY, by: tileSize) {
    for x in stride(from: extent.minX, to: extent.maxX, by: tileSize) {
        let tile = CGRect(x: x, y: y,
                          width: min(tileSize, extent.maxX - x),
                          height: min(tileSize, extent.maxY - y))
        tiles.append(CIVector(cgRect: tile))
    }
}

let result = try MyProcessor.apply(withTiledExtent: tiles, inputs: [inImg], arguments: [:])
```

### Temporary buffers

Core ML wants **planar** data, but Core Image uses **interleaved** buffers — so a processor typically needs scratch buffers to convert. Across many tiles, allocating and freeing those repeatedly hurts performance. `CIImageProcessorOutput` now vends **recyclable** scratch buffers via `temporaryPixelBuffer(identifier:format:width:height:pixelBufferAttributes:)`. The **identifier** matters when a callback uses more than one scratch buffer. Core Image **manages the lifecycle** — releasing at the right time and recycling for the next tile.

```swift
import CoreImage

class MyProcessor: CIImageProcessorKernel {
    override class func process(with inputs: [CIImageProcessorInput]?,
                                arguments: [String: Any]?,
                                output: CIImageProcessorOutput) throws {
        guard let input = inputs?.first,
              let srcPixelBuffer = input.pixelBuffer,
              let dstPixelBuffer = output.pixelBuffer else { return }

        guard let scratch = output.temporaryPixelBuffer(identifier : "myScratch",
                   format: kCVPixelFormatType_64RGBAHalf,
                   width: Int(output.region.width),
                   height: Int(output.region.height),
                   pixelBufferAttributes: nil) else { return }

        // Step 1: copy input CVPixelBuffer → scratch
        // Step 2: process pixels in scratch (in-place)
        // Step 3: copy scratch → output CVPixelBuffer
    }
}
```
