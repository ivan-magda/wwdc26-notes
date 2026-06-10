---
title: "Support the Center Stage front camera in your iOS app — Full Digest"
session: WWDC26 · 341
url: https://developer.apple.com/videos/play/wwdc2026/341/
duration: 18m
speakers: Tracy (Camera Software team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 341 — Support the Center Stage front camera in your iOS app

## TL;DR

The Center Stage front camera (iPhone 17, iPhone Air, iPhone 17 Pro) swaps the
traditional 4x3 front sensor for a **square image sensor** behind a **95° lens** — the
widest FOV on any iPhone front camera. The square shape means you can shoot portrait or
landscape **without rotating the phone**, keeping a secure one-handed grip and natural
eye contact. Three new AVFoundation building blocks let your app exploit it:

1. **Dynamic aspect ratio** (`AVCaptureDevice.dynamicAspectRatio`, iOS 26) — crop any of five aspect ratios out of the square sensor live, with no session rebuild and no preview interruption. The basis for Tap-to-Rotate.
2. **Smart framing monitor** (`AVCaptureSmartFramingMonitor`, iOS 26) — periodic face/gaze-driven recommendations (aspect ratio + zoom) that power **Auto Zoom** and **Auto Rotate**.
3. **Sensor orientation compensation** — the new sensor is mounted in *portrait*, so `AVCapturePhotoOutput` auto-rotates photos to the familiar landscape-left so your existing rotation logic keeps working.

Plus per-process **Center Stage for video calls** (cooperative/app control modes) and
**low-latency stabilization** for smoother footage.

---

## 1. The hardware

Traditional smartphone front sensors are 4x3, which ties your framing to phone
orientation. The Center Stage front camera uses a **square image sensor**, so any aspect
ratio is just a different crop of the same sensor — portrait or landscape selfie without
rotating the iPhone. Benefits called out:

- More secure one-handed grip (no need to flip the phone).
- More natural eye contact (the camera is centered).
- A **95° field of view**, the widest on any iPhone front camera — better group selfies, more headroom for stabilization, and keeps you centered on video calls.

## 2. Center Stage for photos: Auto Zoom + Auto Rotate

The headline photo experience combines the square sensor, the wide FOV, and automatic
face/gaze detection: as people join the shot the frame zooms out; as more crowd in, it
rotates to fit everyone. Building this rests on the capture session setup plus two APIs
(dynamic aspect ratio + smart framing monitor), then sensor orientation compensation.

### Capture session setup

A typical photo pipeline:

1. Create an `AVCaptureSession`.
2. Find the camera: `AVCaptureDevice` with `.builtInUltraWideCamera`, position `.front`.
3. Wrap it in an `AVCaptureDeviceInput`.
4. Add an `AVCaptureVideoPreviewLayer` for preview and an `AVCapturePhotoOutput` for photos.
5. Adding inputs/outputs implicitly forms `AVCaptureConnection`s between compatible media types.

### Dynamic aspect ratio — the building block

`AVCaptureDevice.dynamicAspectRatio` (iOS 26) crops your chosen ratio out of the square
sensor **without rebuilding the session or interrupting preview** — a seamless, quick
switch. It underpins both manual (Tap-to-Rotate) and automatic framing.

Format constraints:

- Requires the front `.builtInUltraWideCamera`.
- Square formats only, resolutions **1280 → 4032**.
- Five aspect ratios: **3x4, 4x3, 9x16, 16x9, 1x1**.
- The **4032 photo format supports only 3x4 and 4x3** — those give the highest photo resolution.

Tap-to-Rotate implementation:

```swift
// Select the Center Stage front camera
import AVFoundation

let deviceDiscoverySession = AVCaptureDevice.DiscoverySession(deviceTypes: [.builtInUltraWideCamera], mediaType: .video, position: .front)

guard let camera = deviceDiscoverySession.devices.first else {
    print("Failed to find the capture device")
    return
}

// Find a format that supports the 4x3 aspect ratio
for format in camera.formats {
    if format.supportedDynamicAspectRatios.contains(.ratio4x3) {
        try! camera.lockForConfiguration()
        camera.activeFormat = format
        camera.unlockForConfiguration()
        break
    }
}

// Set dynamic aspect ratio
try! camera.lockForConfiguration()
let timestamp = try! await camera.setDynamicAspectRatio(.ratio4x3)
print("Applied dynamic aspect ratio at timestamp: \(timestamp)")
camera.unlockForConfiguration()
```

`setDynamicAspectRatio(_)` returns the **timestamp of the first buffer** where the change
takes effect — useful for synchronizing UI or splitting recordings (see §3).

### Smart framing monitor — Auto Zoom + Auto Rotate

`AVCaptureSmartFramingMonitor` (iOS 26) works alongside dynamic aspect ratio, issuing
periodic **framing recommendations** (each an `aspectRatio` + `zoomFactor`) from automatic
face and gaze detection. Your app can apply or ignore each one. Because it's designed for
photo capture, it **only emits recommendations when the 4032 photo format is active**.

```swift
// Find a format that supports smart framing
for format in camera.formats {
    if format.isSmartFramingSupported {
        try! camera.lockForConfiguration()
        camera.activeFormat = format
        camera.unlockForConfiguration()
        break
    }
}

// Configure the monitor (no recommendations by default)
let monitor = camera.smartFramingMonitor!

try! camera.lockForConfiguration()
monitor.enabledFramings = monitor.supportedFramings   // or a subset, e.g. just 4x3 narrow/wide
camera.unlockForConfiguration()

// Apply recommendations via KVO
observation = monitor.observe(\.recommendedFraming, options: [.new,]) { monitor, change in
    if let framing = monitor.recommendedFraming {
        Task {
            try! camera.lockForConfiguration()
            try! await camera.setDynamicAspectRatio(framing.aspectRatio)   // ratio first…
            camera.videoZoomFactor = CGFloat(framing.zoomFactor)           // …then zoom
            camera.unlockForConfiguration()
        }
    }
}

try! monitor.startMonitoring()   // can start while the session is already running

// Tear down when the user disables auto framing
observation?.invalidate()
observation = nil
monitor.stopMonitoring()
```

Two practical notes from the talk: by default the monitor provides **no** recommendations
until you set `enabledFramings` (you can scope it to a subset, e.g. only 4x3 with narrow
and wide zoom factors); and for a smooth preview transition, **set the aspect ratio
before the zoom factor**. The monitor can be started any time, even mid-session.

### Sensor orientation compensation

Historically the iPhone front sensor has been mounted **Landscape Left**: a portrait
selfie arrives at the photo output in native sensor orientation with an EXIF tag saying
"rotate 270° on playback." The Center Stage sensor on iPhone 17 / Air / 17 Pro is instead
mounted **Portrait**, so apps that hardcoded the old rotation values would see photos
sideways or upside down.

To shield existing code, `AVCapturePhotoOutput` applies **sensor orientation compensation
by default**: it physically rotates the photo and rewrites the EXIF metadata before
delivery, yielding the familiar landscape-left result so your existing rotation values
keep working. Caveats:

- Applied only to **HEIC, JPEG, and uncompressed processed photos**.
- **Never** applied to **Bayer RAW or Apple ProRAW**.
- Controllable via `cameraSensorOrientationCompensationEnabled` (iOS 26). Apple recommends testing with compensation **off** for best performance, then verifying your photo orientation is still correct.

For deeper rotation handling with `AVCaptureRotationCoordinator`, see **"Support external
cameras in your iPadOS app"** (WWDC 2023).

## 3. Center Stage for video recordings

Dynamic aspect ratio works for recordings too (Tap-to-Rotate for a wider view), but with
a constraint: **QuickTime movie tracks require all samples to share the same dimensions**,
so recording must stop when you change the aspect ratio mid-capture.

- **`AVCaptureMovieFileOutput`** — swap it in for `AVCapturePhotoOutput`; recording **stops automatically** when the aspect ratio changes.
- **`AVCaptureVideoDataOutput` + `AVAssetWriter`** — use the `setDynamicAspectRatio` completion timestamp to **end the current recording and start a new one** at the updated aspect ratio.

Recordings also get **face-aware cinematic stabilization**: `cinematicExtended` and
`cinematicExtendedEnhanced`, both prioritizing keeping the subject stable over the
background.

## 4. Center Stage for video calls

On a video call, the camera automatically widens to include a friend who joins. Two
adoption paths:

- **Already supported** if your video conferencing app uses the **Voice over IP background mode** (to stay connected while locked). People toggle Center Stage from **Control Center → Video Effects**.
- **Adopt the API directly** if you don't use VoIP background mode. Available on the front camera starting with iPhone 17 / Air / 17 Pro.

Like other system-wide video effects (Portrait, Studio Light, Gestures), Center Stage is
enabled **per process** — once active it applies to any supported camera in the app.

Video-call pipelines typically use an `AVCaptureVideoDataOutput` (buffers streamed to the
app for display/encode/transmit). Enabling Center Stage:

```swift
// Find a format that supports Center Stage
for format in camera.formats {
    if format.isCenterStageSupported {
        try! camera.lockForConfiguration()
        camera.activeFormat = format
        camera.unlockForConfiguration()
        break
    }
}

// Turn on Center Stage
AVCaptureDevice.centerStageControlMode = .cooperative   // or .app
AVCaptureDevice.isCenterStageEnabled = true
```

- **Control modes:** by default people toggle via Control Center. Set `centerStageControlMode` to **`.cooperative`** (people can also control it from a button in your app) or **`.app`** before enabling.
- For the original iPad Center Stage API, see **"What's New in camera capture"** (WWDC 2021).

### Low-latency stabilization

Beyond Center Stage, the front camera supports a **real-time, low-latency stabilization
mode** (iOS 26), **off by default**:

```swift
connection.preferredVideoStabilizationMode = .lowLatency
```

The side-by-side demo shows noticeably less shake while walking with it on.

## 5. Next steps (Apple's recommendations)

- Add framing controls: orientation switching, Auto Zoom / Auto Rotate toggles, Center Stage activation.
- Optimize front-camera performance — test with **sensor orientation compensation off** and confirm photo rotation is still correct.
- Consider supporting **18-megapixel** photo capture. See **"Implement high resolution photo capture"** (WWDC 2026).
