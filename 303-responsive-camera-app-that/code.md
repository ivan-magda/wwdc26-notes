# Code samples — Session 303

Extracted from the session's Code tab. Timestamps are approximate, mapped
to the matching transcript chapters.

## Deferred start delegate

```swift
import AVFoundation

class DeferredStartDelegate: NSObject, AVCaptureSessionDeferredStartDelegate {
    func sessionWillRunDeferredStart(_ session: AVCaptureSession)
    {
        // This is called before deferred start begins for the deferred outputs
    }

    func sessionDidRunDeferredStart(_ session: AVCaptureSession)
    {
        // This is called after deferred start completes for all outputs
    }
}
```

## Automatic deferred start (preview layer)

```swift
import AVFoundation

let captureSession = AVCaptureSession()
captureSession.beginConfiguration()
captureSession.automaticallyRunsDeferredStart = true

let videoPreviewLayer = AVCaptureVideoPreviewLayer(session: captureSession)
videoPreviewLayer.isDeferredStartEnabled = false

let photoOutput = AVCapturePhotoOutput()
photoOutput.isDeferredStartEnabled = true
captureSession.addOutput(photoOutput)

captureSession.setDeferredStartDelegate(deferredStartDelegate, deferredStartDelegateCallbackQueue: sessionQueue)

captureSession.commitConfiguration()
captureSession.startRunning()
```

## Manual deferred start (video data output for preview)

```swift
import AVFoundation

let captureSession = AVCaptureSession()
captureSession.beginConfiguration()
captureSession.automaticallyRunsDeferredStart = false

let videoOutput = AVCaptureVideoDataOutput()
captureSession.addOutput(videoOutput)
videoOutput.isDeferredStartEnabled = false

let photoOutput = AVCapturePhotoOutput()
photoOutput.isDeferredStartEnabled = true
captureSession.addOutput(photoOutput)

captureSession.setDeferredStartDelegate(deferredStartDelegate, deferredStartDelegateCallbackQueue: sessionQueue)

captureSession.commitConfiguration()
captureSession.startRunning()
```

## Triggering manual deferred start on first frame

```swift
import AVFoundation
import QuartzCore

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

## Responsive capture on the photo output

```swift
import AVFoundation

func configurePhotoOutput(for session: AVCaptureSession, device: AVCaptureDevice) {
    let photoOutput = AVCapturePhotoOutput()

    guard session.canAddOutput(photoOutput) else { return }
    session.addOutput(photoOutput)

    photoOutput.maxPhotoQualityPrioritization = .quality
    // Responsive capture lets the photo output capture immediately
    photoOutput.isResponsiveCaptureEnabled = photoOutput.isResponsiveCaptureSupported
}
```

## Checking hardware cost and observing system pressure

```swift
import AVFoundation

let captureSession = AVCaptureSession()
let device = activeVideoInput?.device
captureSession.beginConfiguration()
// ...
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

## AVProVideoStorage — capacity check

```swift
import AVFoundation

func configureProVideoStorage() {
    guard AVProVideoStorage.isSupported else { return }
    let storage = AVProVideoStorage.shared
    guard storage.remainingCapacity != 0 else {
        storage.openSettings()
        return
    }
}
```

## AVProVideoStorage — recording with the movie file output

```swift
import AVFoundation

guard AVProVideoStorage.isSupported else { return }
guard let pvs = AVProVideoStorage.shared else { return }

// Configure and set up AVCaptureSession, AVCaptureConnections and format
// ...
let movieOutput = AVCaptureMovieFileOutput()

guard movieOutput.isProVideoStorageSupported else { return }
guard !pvs.isBusy else { return }

let movieFileURL = FileManager.default.temporaryDirectory
            .appendingPathComponent(UUID().uuidString)
            .appendingPathExtension("mov")

movieOutput.usesProVideoStorage = true // Also available with AVAssetWriter
movieOutput.startRecording(to: movieFileURL, recordingDelegate: delegate)
```

---

## Useful API facts surfaced by the code

- Deferred start delegate: `AVCaptureSessionDeferredStartDelegate` with `sessionWillRunDeferredStart` / `sessionDidRunDeferredStart`; attach via `setDeferredStartDelegate(_:deferredStartDelegateCallbackQueue:)`.
- `AVCaptureSession.automaticallyRunsDeferredStart` (Bool) selects automatic vs manual mode; `runDeferredStartWhenNeeded()` triggers manual mode.
- Each output (and `AVCaptureVideoPreviewLayer`) has `isDeferredStartEnabled`. Set `false` on the preview output, `true` on deferred outputs (e.g. photo output).
- `AVCapturePhotoOutput.isResponsiveCaptureEnabled` / `.isResponsiveCaptureSupported`; `maxPhotoQualityPrioritization = .quality`.
- `AVCaptureSession.hardwareCost` (Double, 0–1; >1 = unsupported). Observe `AVCaptureDevice.systemPressureState` via KVO.
- `AVProVideoStorage`: `isSupported`, `shared` (singleton), `remainingCapacity`, `openSettings()`, `isBusy`. `AVCaptureMovieFileOutput.isProVideoStorageSupported` and `usesProVideoStorage` (also on `AVAssetWriter`).
- `frameRateOverride` reduces hardware cost when running below a format's max frame rate (named in the talk, not in the snippets).
