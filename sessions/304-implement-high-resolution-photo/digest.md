---
title: "Implement high resolution photo capture — Full Digest"
session: WWDC26 · 304
url: https://developer.apple.com/videos/play/wwdc2026/304/
duration: 18m
speakers: Mohit Setia
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 304 — Implement high resolution photo capture

## TL;DR

A practical AVFoundation walkthrough for shipping high-resolution stills (12 / 24 /
48 MP) in your own camera app. Three headline themes:

1. **Know the resolutions and what produces them.** 48 MP is a single full-sensor
   frame off the quad-pixel sensor; 24 MP is the photonic engine *fusing* a 12 MP
   multi-frame HDR image with a 48 MP detail frame — double the pixels of 12 MP for
   only ~50% more file size. Hardware availability has widened (telephoto on iPhone 16
   Pro, ultra wide on iPhone 17).
2. **Configure once, capture flexibly.** Use the `.photo` preset (the only one that
   supports 24/48 MP), set `maxPhotoQualityPrioritization` and `maxPhotoDimensions` on
   the output *before* `commitConfiguration()`, then vary quality and dimensions
   per-capture with no costly reconfiguration. Preallocate with
   `setPreparedPhotoSettingsArray` so the first shot isn't slow.
3. **Responsiveness is an explicit opt-in.** High-res processing takes seconds and
   blocks the next shot by default. Three APIs fix this: **responsive capture**
   (overlapping captures via `captureReadiness`), **deferred photo processing** (a
   proxy now, final pixels later/in background), and **fast capture prioritization**
   (auto-drop from quality to balanced during burst-y shooting). The basketball demo:
   one blocked shot without them vs. five responsive shots with them.

The through-line is the tradeoff: more resolution and higher quality prioritization
mean more memory and more processing time, which costs responsiveness unless you
adopt the responsiveness APIs.

---

## 1. High-resolution photos: the resolution tiers

The preview stream is only screen-resolution — fine for showing the camera's view,
but photography needs detail, low noise, and headroom for cropping, zooming, and image
analysis. High-resolution captures provide that:

- **12 MP** — the typical high-resolution photo; higher resolution than the preview.
- **24 MP** and **48 MP** — captured at an even larger sensor resolution; significantly
  more detail and clarity.

**The quad-pixel sensor** (since **iPhone 14 Pro / 14 Pro Max**) either:

- captures at **full 48 MP** for the highest detail (a single sensor frame, ~4× a
  standard photo), or
- groups pixels into **2×2 same-color clusters** to gather more light.

**24 MP** (since **iPhone 15**, and the Camera app's default capture mode) is a
multi-step computation:

1. The camera uses combined pixels on the quad sensor to make a **12 MP multi-frame
   fused HDR** image.
2. The **photonic engine** combines that with a **full-resolution 48 MP** frame for
   rich detail.
3. Result: a **24 MP** image — double the resolution of 12 MP, with only ~**50% larger
   file size**. It balances light and detail while keeping files manageable.

24/48 MP support has expanded beyond the main camera: the **telephoto on iPhone 16
Pro** and the **ultra wide on iPhone 17**.

## 2. The four capture types you can request

1. **Fully processed photo** (most common) — multi-frame fused, run through the
   photonic engine to extend dynamic range and improve detail.
2. **Exposure brackets** — multiple exposure frames of the same scene; for building
   your own HDR or selecting among exposures.
3. **Bayer RAW** — minimally processed sensor data; ideal for post-processing/editing.
4. **Apple ProRAW** — RAW flexibility combined with iPhone's image processing; more
   latitude when editing exposure, color, and detail.

Deep-dive for RAW: **"Capture and process ProRAW images" (WWDC 2021).**

## 3. Configure the capture session

Create an `AVCaptureSession`, begin configuration, and select the `.photo` preset —
**only `.photo` supports 24 and 48 MP**:

```swift
import AVFoundation

private let session = AVCaptureSession()
private func configureSession() {
    session.beginConfiguration()
    session.sessionPreset = .photo
}
```

**Pick a quality prioritization** (covered in depth in *"Capture High-Quality Photos
Using Video Formats," WWDC 2021*):

- **Speed** — fastest delivery, least processing.
- **Balanced** — medium delivery, good quality.
- **Quality** — slowest, best quality.

Set `maxPhotoQualityPrioritization` on the output to the *highest* level you'll use; it
prepares resources for every level up to that one:

```swift
import AVFoundation

private let photoOutput = AVCapturePhotoOutput()
private let configurePhotoOutput: () -> Void = {
    photoOutput.maxPhotoQualityPrioritization = .quality // or .balanced
}
```

**Select the dimensions.** Since iOS 16, query `supportedMaxPhotoDimensions` on the
device's active format and set `maxPhotoDimensions` on the output. Finish all output
configuration **before** `commitConfiguration()` — changing these after commit triggers
a lengthy pipeline reconfiguration:

```swift
import AVFoundation

let supportedMaxPhotoDimensions = device?.activeFormat.supportedMaxPhotoDimensions ?? []
if let largestDimension = supportedMaxPhotoDimensions.max(by: { lhs, rhs in
    Int(lhs.width) * Int(lhs.height) < Int(rhs.width) * Int(rhs.height)
} ) {
    photoOutput?.maxPhotoDimensions = largestDimension
}

session?.commitConfiguration()
session?.startRunning()
```

(The demo picks the largest dimensions; you should pick what fits your use case.)

**Capture.** Set `maxPhotoDimensions` and `photoQualityPrioritization` on a fresh
`AVCapturePhotoSettings`, then capture with a delegate:

```swift
import AVFoundation

let settings = AVCapturePhotoSettings()
settings.maxPhotoDimensions = dimension.cmVideoDimensionsValue
settings.photoQualityPrioritization = .quality

var delegate: AVCapturePhotoCaptureDelegate?
// Configure photo request delegate

if let delegate {
    photoOutput?.capturePhoto(with: settings, delegate: delegate)
}
```

Key points:

- `maxPhotoDimensions` is a **request, not a guarantee** — the system weighs light
  level, scene, and available processing and picks the best path. Actual dimensions
  arrive in **`AVCaptureResolvedSettings`** (which also signals capture completion).
- You can vary quality prioritization and dimensions **per capture** in the same
  session — no lengthy reconfiguration between shots.

### Preallocate to avoid first-shot slowdown

High-res captures need specific resource allocations keyed to `photoQualityPrioritization`
and `maxPhotoDimensions`. If they aren't preallocated, allocation happens at capture
time and slows the first shot. Signal your intent up front (e.g. the moment 48 MP mode
is activated) with `setPreparedPhotoSettingsArray`:

```swift
import AVFoundation

let prepareSettings = AVCapturePhotoSettings()
prepareSettings.maxPhotoDimensions = photoOutput.maxPhotoDimensions
prepareSettings.photoQualityPrioritization = .quality

photoOutput.setPreparedPhotoSettingsArray([prepareSettings]) { prepared, error in
    if let error = error {
        print("Failed to prepare: \(error)")
        return
    }
    print("Pipeline prepared: \(prepared)")
}

// Later, when ready to capture — create NEW settings
let captureSettings = AVCapturePhotoSettings()
captureSettings.maxPhotoDimensions = photoOutput.maxPhotoDimensions
captureSettings.photoQualityPrioritization = quality
photoOutput.capturePhoto(with: captureSettings, delegate: self)
```

You **cannot reuse** the `prepareSettings` object for the actual capture — make a new
settings object whose configuration **matches** so the capture aligns with the
preallocated resources.

## 4. Resolution × prioritization availability

Processing time varies by prioritization (quality = longest/best, balanced = optimal
for common cases, speed = fastest without the quality boost). Which resolutions are
available depends on the level:

| Resolution | Speed | Balanced | Quality | Notes |
|---|---|---|---|---|
| 12 MP | ✅ | ✅ | ✅ | available at all three |
| 48 MP | — | ✅ | ✅ | single frame, so no speed mode |
| 18 MP | — | — | ✅ | multi-frame fused; **Center Stage front camera on iPhone 17 only** |
| 24 MP | — | — | ✅ | multi-frame fused |

For the 18 MP front-camera case, see **"Support Center Stage front camera in your iOS
app" (WWDC 2026).**

## 5. Keeping the app responsive

High-res processing can take several seconds. A capture moves through a **capture
stage** then a **processing stage**; `AVCapturePhotoCaptureDelegate` reports progress
(`didCapturePhotoFor` / `didFinishCaptureFor resolvedSettings`).
`AVCaptureResolvedSettings.photoProcessingTimeRange` tells you how long to expect before
delivery. By default the **next photo can't be captured until the previous one finishes
processing** — that gap is the **shot-to-shot delay**.

Three layered fixes, each going further:

### Responsive capture (overlapping captures)

Enable responsive capture on `AVCapturePhotoOutput` so a new capture can begin once the
previous photo's **capture stage** finishes — no waiting for processing. Observe
**`captureReadiness`** to know when the next shot can fire. This cuts the shot-to-shot
delay for the *second* photo so you don't miss the moment, though each photo still takes
the same processing time.

### Deferred photo processing

For high-quality captures, enable deferred processing: the system delivers a **lightly
processed proxy photo immediately** via `didFinishCapturingDeferredPhotoProxy`. Final
processing happens either **on demand** (when you request the final photo through the
photo library) or **in the background** when conditions are favorable (e.g. device
idle).

Because the deferred work runs in the background, it **doesn't share memory with the
capture session** — which is exactly what makes the multi-frame fusion captures (18 and
24 MP) possible. The processing stage shrinks dramatically while the capture stage stays
the same, reducing shot-to-shot delay for all subsequent photos. Deep-dive: **"Create a
More Responsive Camera Experience" (WWDC 2023).**

### Fast capture prioritization

Turn on fast capture prioritization on `AVCapturePhotoOutput` to prioritize
responsiveness further. The system detects rapid successive captures and dynamically
**drops photo quality from quality → balanced**, since balanced needs less time for
both capture and processing — a speed boost exactly when someone is shooting fast.
Starting with **iOS 27 on iPhone 16 and iPhone 17**, the system also processes balanced
fast captures later via deferred processing, minimizing processing time and sustaining
the faster experience much longer.

### The demo

A basketball game: **without** deferred processing / responsive shutter / fast capture,
the shutter button spins while processing and the photographer gets a single shot.
**With** all three enabled, the button stays responsive — the session starts in Quality,
detects fast captures, and intelligently transitions to Balanced — yielding five
responsive shots of the same moment instead of one blocked capture.

## Wrap-up guidance

- Identify the resolution you want — 24/48 MP give more to crop/zoom/explore but cost
  memory and processing time.
- Pick the prioritization (speed / balanced / quality) that matches your app's needs.
- For the highest-quality captures, **turn on deferred processing and responsive
  captures** — without them every photo blocks the next and users miss moments.

To further optimize launch/perf, see **"Build a responsive camera app that launches
quickly" (WWDC 2026).**
