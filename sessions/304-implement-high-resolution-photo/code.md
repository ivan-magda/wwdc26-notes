# Code samples — Session 304

Extracted from the Code tab. Timestamps map to the transcript.

## 5:26 — Create and begin configuring the session

```swift
import AVFoundation

private let session = AVCaptureSession()
private func configureSession() {
    session.beginConfiguration()
    session.sessionPreset = .photo
}
```

Only the `.photo` preset supports 24 and 48 MP captures.

## 6:11 — Set max photo quality prioritization on the output

```swift
import AVFoundation

private let photoOutput = AVCapturePhotoOutput()
private let configurePhotoOutput: () -> Void = {
    photoOutput.maxPhotoQualityPrioritization = .quality // or .balanced
}
```

This tells the session to prepare resources for all prioritization levels up to the
one set (speed, balanced, quality).

## 6:38 — Pick the largest supported photo dimensions, then commit

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

Complete photo output configuration before `commitConfiguration()` — changing these
after commit triggers a lengthy pipeline reconfiguration. Pick dimensions that fit
your use case rather than always the largest.

## 7:21 — Per-capture settings and capture

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

`maxPhotoDimensions` here is a request, not a guarantee; the resolved values come back
in `AVCaptureResolvedSettings`. Quality and dimensions can vary per capture with no
reconfiguration between shots.

## 9:01 — Preallocate resources with setPreparedPhotoSettingsArray

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

Call this as early as possible (e.g. as soon as 48 MP mode is activated) so resources
are ready before capture. The `prepareSettings` object cannot be reused for the actual
capture — create a new settings object whose configuration matches it so the capture
aligns with the preallocated resources.

---

## Spoken APIs (named in the transcript, not in the Code tab)

- `AVCapturePhotoCaptureDelegate` callbacks: `didCapturePhotoFor` / `didFinishCaptureFor` (resolvedSettings); `didFinishCapturingDeferredPhotoProxy`
- `AVCaptureResolvedSettings.photoProcessingTimeRange`
- `AVCapturePhotoOutput` responsiveness properties: responsive capture (overlapping captures), `captureReadiness`, fast capture prioritization
- Deferred photo processing (proxy photo delivered immediately; final processing on demand via the photo library or in the background)
