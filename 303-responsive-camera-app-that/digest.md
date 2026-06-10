---
title: "Build a responsive camera app that launches quickly — Full Digest"
session: WWDC26 · 303
url: https://developer.apple.com/videos/play/wwdc2026/303/
duration: 25m
speakers: Jake (camera performance team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 303 — Build a responsive camera app that launches quickly

## TL;DR

The thesis of the talk: **the single most important factor in making a camera launch feel
fast is how quickly the first preview frame appears on the display.** A camera engineer
on Apple's performance team walks through four levers, in order:

1. **Fast launch** — phase your UI (preview + shutter first, everything else later),
   create and run `AVCaptureSession` *off the main thread* (it blocks), and commit a
   single configuration up front.
2. **Deferred Start API** (iOS 26+) — defer initialization of every output that isn't
   needed for preview (photo output, movie file output) until *after* the first frame.
   Automatic mode is on by default when you recompile against the iOS 26 SDK with an
   `AVCaptureVideoPreviewLayer`. Claimed **~2× faster launch** in a lab lightboard test.
   Pair it with `isResponsiveCaptureEnabled` so deferring the photo output doesn't make
   you miss the first shot.
3. **Steady preview** — `AVCaptureVideoPreviewLayer` for simple, low-overhead, low-latency
   rendering (HDR tone mapping handled for you, auto deferred start); switch to
   `AVCaptureVideoDataOutput` only when you need per-frame access / Metal / overlays — and
   then you must adopt *manual* deferred start yourself.
4. **Sustained performance** — `hardwareCost` and the system-pressure cost APIs (0–1, >1 is
   unsustainable), plus observing `AVCaptureDevice.systemPressureState` to adapt under
   thermal load. And **`AVProVideoStorage`** (iOS 27): pre-allocated, system-wide storage
   that gives deterministic high-bandwidth file writes for ProRes-class captures.

The running demo is a falling-domino chain with a red domino in the middle: a slow launch
misses the moment, a fast one (deferred start + responsive capture) catches it.

---

## 1. Fast launch

There are **four stages** in a camera app launch:

1. **App launch** — linker loads the binary, static initializers run, UI scenes are
   created, plus anything done before the capture session exists.
2. **Session configured and started** — initializing the session, committing config,
   `startRunning`.
3. **Outputs initialize** — every `AVCapture*Output` initializes; cost scales with the
   number of outputs and their quality settings. *This is the most expensive part.*
4. **Preview streams** — frames start flowing.

### Phase the UI

Split launch work into two buckets: resources **critical** for showing preview, and
resources that can be created **after** preview is running. In AVCam (the classic AVFoundation
sample), the preview and shutter button are critical; the image well and mode picker are
not, so they fade in after launch. Any resource created before the first frame renders adds
to launch time — not just UI.

### Build the session correctly

A typical session: an `AVCaptureDeviceInput` (camera/mic) → `AVCaptureConnection` →
outputs (here, an `AVCaptureVideoPreviewLayer` for preview and an `AVCapturePhotoOutput`
for capture).

- Create `AVCaptureSession` **first**, as soon as the main thread finishes UI setup — it
  coordinates everything else.
- **Creating the session blocks the main thread.** Dispatch session creation off-main so it
  runs in parallel with UI scene creation, avoiding a hang.
- Commit a **single** configuration up front; multiple `commitConfiguration` passes extend
  launch.
- `startRunning` / `stopRunning` are **blocking** — never call them on the main thread.

## 2. Deferred Start (iOS 26+)

Initializing outputs is the slowest launch stage, yet to render preview you only need the
preview layer (or one output). The Deferred Start API postpones initialization of every
output that isn't needed for preview until after the first frame has displayed.

Each output and `AVCaptureVideoPreviewLayer` has an `isDeferredStartEnabled` property — set
it `true` to defer that output, `false` on the one used to render preview.

### Delegate callbacks

```swift
import AVFoundation

class DeferredStartDelegate: NSObject, AVCaptureSessionDeferredStartDelegate {
    func sessionWillRunDeferredStart(_ session: AVCaptureSession) {
        // Called before deferred start begins — good place to create background resources
    }
    func sessionDidRunDeferredStart(_ session: AVCaptureSession) {
        // Called after deferred start completes — all outputs are now ready to use
    }
}
```

### Automatic mode (default)

Apps recompiled against the iOS 26+ SDK use automatic mode by default
(`automaticallyRunsDeferredStart == true`). The system picks the best time to initialize
deferred outputs — shortly after preview appears.

```swift
let captureSession = AVCaptureSession()
captureSession.beginConfiguration()
captureSession.automaticallyRunsDeferredStart = true

let videoPreviewLayer = AVCaptureVideoPreviewLayer(session: captureSession)
videoPreviewLayer.isDeferredStartEnabled = false   // render preview, don't defer

let photoOutput = AVCapturePhotoOutput()
photoOutput.isDeferredStartEnabled = true            // defer the expensive output
captureSession.addOutput(photoOutput)

captureSession.setDeferredStartDelegate(deferredStartDelegate,
                                        deferredStartDelegateCallbackQueue: sessionQueue)
captureSession.commitConfiguration()
captureSession.startRunning()
```

### Manual mode

For finer control — reading preferences or setting up UI before heavy initialization, or
when rendering preview via `AVCaptureVideoDataOutput` (which does *not* get automatic
deferred start). Set `automaticallyRunsDeferredStart = false`, then call
`runDeferredStartWhenNeeded()` when startup work is done.

```swift
captureSession.automaticallyRunsDeferredStart = false

let videoOutput = AVCaptureVideoDataOutput()
captureSession.addOutput(videoOutput)
videoOutput.isDeferredStartEnabled = false           // this renders preview

let photoOutput = AVCapturePhotoOutput()
photoOutput.isDeferredStartEnabled = true
captureSession.addOutput(photoOutput)
// ...commit + startRunning as above
```

Trigger it once the first frame is presented (here via a CAMetalLayer drawable's
presented handler):

```swift
private var firstFramePresented = false
guard let drawable = layer.nextDrawable()
if (!firstFramePresented) {
    drawable.addPresentedHandler({ drawable in
        // Set up postponed UI elements
        captureSession.runDeferredStartWhenNeeded()
    })
    firstFramePresented = true
}
```

### The lab result

A lightboard test with two phones — deferred start on the right, off on the left — compared
how far the expanding LED pattern had progressed by the time each showed preview. Without
deferred start, launch was **close to a second**; with it, launch was **cut in half — ~2×
faster**. Complex sessions may improve even more.

### The catch: first capture, and responsive capture

Deferring the photo output gets preview up sooner but **doesn't speed up time-to-first-capture** —
the system still has to finish initializing the photo output before a capture can begin, so
you can still miss the shot. Fix: `isResponsiveCaptureEnabled = true` on the photo output,
which buffers between starting a capture and when processing begins.

```swift
photoOutput.maxPhotoQualityPrioritization = .quality
photoOutput.isResponsiveCaptureEnabled = photoOutput.isResponsiveCaptureSupported
```

In the domino demo, the phone with deferred start + responsive capture got a clean shot;
the other phone missed it. See **Implement High Resolution Photo Capture (WWDC26)** for
responsive capture and high-res image details.

## 3. Steady preview

Once preview runs, a steady frame rate and cadence matter — otherwise the camera feels
laggy. Two ways to render:

**`AVCaptureVideoPreviewLayer`** — shows exactly what the camera sees, optimized for preview:
- No per-frame processing in the app; handles HDR tone mapping automatically.
- Low CPU/GPU overhead (saves power, leaves UI headroom) and tuned for low-latency preview.
- Trade-off: **no per-frame access.**
- Apps using it get **automatic** deferred start when recompiled for iOS 26+.

**`AVCaptureVideoDataOutput`** — takes the place of the preview layer as the primary display
output when you need more control:
- Per-frame access → custom UI overlays per frame, easier Metal integration, frame analysis.
- **Deferred start is *not* automatic here** — adopt manual deferred start to get the same
  launch gains.
- Keep per-frame work short to avoid drops.

## 4. Sustained performance under pressure

A basic session (session + photo output + preview layer) grows in cost as you add cameras
or inputs. Two cost APIs, both returning **0–1** (where **>1 means the configuration is
unsustainable / unsupported**):

- **`AVCaptureSession.hardwareCost`** — the share of the session's hardware actively in use.
  Contributors: number of cameras; active formats (1080p vs 4K); frame rate (hardware cost
  **assumes the format's max frame rate**, so use `frameRateOverride` to lower cost when
  running below it, e.g. 30 instead of 60 fps); and whether **binned formats** are used
  (binning groups pixels → less bandwidth).
- **System-pressure cost** — the cost of the current configuration; >1 is unsustainable.

Workflow: after committing the configuration, check `hardwareCost <= 1.0`; if it exceeds the
device's capabilities, reconfigure to a lower-cost setup before starting. Once at/below 1,
observe `AVCaptureDevice.systemPressureState` and register a change handler.

```swift
captureSession.commitConfiguration()

guard captureSession.hardwareCost <= 1.0 else {
    print("hardwareCost \(captureSession.hardwareCost) — cannot start session. Reconfiguring.")
    setupLowCostConfiguration()
}

captureSession.startRunning()
let systemPressureObserver = device?.observe(\.systemPressureState,
                                             options: [.initial, .new],
                                             changeHandler: { /* Handle state change */ })
```

As system pressure rises (e.g. device heating up), adapt: reduce the capture device's frame
rate, throttle GPU/Apple Neural Engine use, or minimize UI work.

## 5. Deterministic file writing — AVProVideoStorage (iOS 27)

Traditional filesystem I/O is **non-deterministic** — the system juggles competing
operations, memory fragmentation, and storage wear. High-data-rate captures like **ProRes**
need sustained high-bandwidth I/O to record without dropping frames.

**`AVProVideoStorage`** (new in iOS 27) tracks and manages **pre-allocated** storage for
high-data-rate captures. It's a **system-wide singleton** that all apps share, and works
with the existing movie recording APIs. Opt in via `usesProVideoStorage` on
`AVCaptureMovieFileOutput` — or on `AVAssetWriter` when recording from
`AVCaptureVideoDataOutput`. The system handles allocation and file I/O so write performance
stays consistent.

The Camera settings UI is updated so people control how much storage to allocate.
`remainingCapacity` reports what's left (decreases during a recording, stops when it stops);
`openSettings()` jumps to the settings UI.

```swift
func configureProVideoStorage() {
    guard AVProVideoStorage.isSupported else { return }
    let storage = AVProVideoStorage.shared
    guard storage.remainingCapacity != 0 else {
        storage.openSettings()
        return
    }
}
```

Recording flow: check support, get the `shared` singleton, build the movie file output /
session / connections / format, confirm `isProVideoStorageSupported` on the output and that
the storage `isBusy == false`, then set `usesProVideoStorage = true` and start recording.
The recording is written to pre-allocated storage and **moved to the final location once the
capture finishes**.

```swift
guard AVProVideoStorage.isSupported else { return }
guard let pvs = AVProVideoStorage.shared else { return }
// configure session/connections/format...
let movieOutput = AVCaptureMovieFileOutput()

guard movieOutput.isProVideoStorageSupported else { return }
guard !pvs.isBusy else { return }

let movieFileURL = FileManager.default.temporaryDirectory
    .appendingPathComponent(UUID().uuidString)
    .appendingPathExtension("mov")

movieOutput.usesProVideoStorage = true   // Also available with AVAssetWriter
movieOutput.startRecording(to: movieFileURL, recordingDelegate: delegate)
```

## Wrap-up advice

- Adopt deferred start with the **quality** photo output — fast launch *and* gorgeous image
  quality.
- Use **Instruments and Xcode** to measure and fix performance elsewhere in the app.
- Test in the **real world** (e.g. a hot sunny day), not just at a cool desk.
- See **Create a More Responsive Camera Experience (WWDC23)** and **Implement High
  Resolution Photo Capture (WWDC26)**.
